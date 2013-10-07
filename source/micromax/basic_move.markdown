---
layout: page
title: 棋法 Move Generation
footer: false
---



### [棋法：基础走法 Move Generation: Simple Moves](http://home.hccnet.nl/h.g.muller/movgen.html)


####三层嵌套循环 Three Nested Loops

走法的产生是通过三层嵌套循环完成的：  
* 遍历所有的棋子  
* 遍历棋子所有的方向  
* 遍历该方向上的所有格子  

对于爬行棋子（如兵，马，王），针对棋子的遍历循环通过在第一层循环之后就终止了，尽管兵（E.P.）和王（别忘了还有王车易位）有时也能在特定方向上，移动不止一步。  
对于任何棋子，内层的循环都会放弃，当已经走出棋盘或者走到某个阻挡的棋子。
当然也有阻挡的棋子不影响步法的合法性的。

在讨论一些繁琐细节的 micro-max 实现细节之前，我们先大概了解一下基础步法的产生代码：

Move generation is done by three nested loops:

* over the pieces  
* over all directions for that piece  
* over all squares in that direction  


For crawling pieces (PNK) the loop over squares is usually terminated after the first iteration, although Pawns and Kings (castling!) sometimes also make more than one step in a particular direction. For any piece this inner loop can be aborted because we run off the board or into an obstructing piece, although for obstructing enemy pieces the move might still be legal. Before discussing the nitty-gritty details of the implementation in micro-Max below, we give the general outline of the basic move generator:

```c

static int o[] = {
    -16,-15,17,0,           /* downstream pawn    */
    16,15,17,0,         /* upstream pawn      */
    1,-1,16,-16,0,          /* rook           */
    1,-1,16,-16,15,-15,17,-17,0,    /* king, queen and bishop */
    14,-14,18,-18,31,-31,33,-33,0,  /* knight         */
    -1,3,21,12,16,7,12      /* directory          */
      };

x = 0;
do{                 /* LOOP OVER PIECES                 */
  u = b[x];             /* contents of square                   */
  if(u&k)               /* only do something if it contains a piece of mine     */
  { p = u&7;                /* extract piece type                   */
    j = o[p+30];            /* look up first direction it moves in directory    */
    while(r=o[++j])         /* LOOP OVER DIRECTIONS r taken from vector list    */
    { y = x;                /* start ray at current piece location          */
      do{               /* LOOP OVER SQUARES                    */
        y += r;             /* add one step                     */
        if(y&M) break;          /* terminate ray if off board               */
        t = b[y];           /* contents of target square                */
        if(t&k) break;          /* terminate ray if own piece               */
    if(p<3&&!(r&7)!=!t) break;  /* terminate if pawn and inappropiate mode      */
        ....                /* valid move available: u@x -> t@y. Use it!        */
    t += p<5;           /* make sure t!=0 for crawling pieces           */
      }while(!t)            /* ray continues if move was not capture        */
    }
  }
}while(i=i+9&~M);           /* next square of board                 */

```



#### 遍历棋子的循环 The Loop over Pieces

在 micro-Max 中，对于棋子的遍历，在实现上，是通过遍历棋盘上所有棋子，找到对应白方或者黑方的棋子。  
这里效率上有点低，特别是在尾盘，棋盘越来越空的情况下。  
甚至是在开局，也仅仅只有25%的棋子在棋盘上。  

但是棋盘数组是一个比较直接的方法去找到我们想找到的以及如何走的（这个问题在每次试图走一个棋子，都需要回答的），但是这也导致我们不知道我们的棋子在什么地方。  
『因为会由棋子挂掉，当然也是因为要节约代码。这里也可以通过棋子列表等方式来实现。但是这里也不是性能的瓶颈，还需要额外维护一个数组结构，代码那是成倍的增加』

当然一个『棋子列表』，一个表格存储每个活着的棋子的棋盘编号和棋子类型，这样来玩是更合适的。『啊，你也知道啊』  
这样做，在最外层的棋子遍历上，可以很简单地扫描这个『棋子列表』就O了，每次都能找到1个活棋，而不是在一个大大的棋盘上，偶然发现一个棋子。  

但是由于性能最好的目标，我们决定牺牲掉这个『棋子列表』。  
这可是两害，取其轻呢！  
因为牺牲掉棋盘数组将是灾难性的，因为每次尝试性的移动，都需要去看这个移动是否被双方的棋子已经阻挡了。  
并且试图移动的个数比试图移动的棋子数多得多。  
原因稍后会变得更明显，棋盘扫描也可以不开始在一个角落里，而可以从任意位置开始，如变量B，可以环绕遍历棋盘，然后直到再次达到起点格。

In micro-Max the loop over pieces is implemented as a loop over all valid squares of the board, searching for pieces of the side to move. This is rather inefficient, especially if the board gets more empty during the game. Even in the beginning only on 25% of the squares it finds own pieces. The board array is a direct way of finding out what we find where we go, (a question that has to be answered frequently once we try to move a piece), but it leaves us unfortunately unaware of where our pieces are. For that a 'piece list', a table listing the square number and piece type for each piece we (still) have, would be better suited. The outer loop of the move generator would then simply scan this piece list, and find a piece every time, rather than only occasionally finding a piece on a board square. Because of the minimalist approach we decided to sacrifice the piece list. This is the best of two evils: sacrificing the board array would be completely disastrous, because for every attempted move the program could only find out if the move was to an occupied square by scanning the piece lists of both sides. And there are many more attempted moves than there are pieces to move. 
For reasons that will become apparent later, the board scan does not begin in a corner, but can begin anywhere on the board, as indicated by variable B. It then wraps around until it reaches that starting square again.

#### The Loop over Directions

If the scan of the board discovers a friendly piece, we start generating moves for this piece. To this end we loop through a list of move vectors, one for every direction a piece of that type can move in. An initialized global array o[] contains the move vectors r (numbers that have to be added to the current square to make one step in that direction). This list is scanned by the loop index j until a 0 is encountered (which obviously is an invalid move vector, since it would make no step at all). The move-vector lists for all piece types are all placed in the same array, one after the other, separated by zeroes. Lists are combined where possible: in the list for a Queen (which is also used for the King) the straight directions all preceed the diagonal directions, so that the final part of the list can be used for a Bishop, by starting it in the middle. For each piece type we need to know where to start scanning the o[] array, this information is stored further in the o[] array itself, so that a piece of type p finds its starting direction index j at o[p+30]. We could say that o[] summarizes most of the rules for chess.

To save some characters in the initializer for the list, we make use of the fact that allowed directions usually come in pairs of opposite directions. The list contains then only contains the positive one, and in the loop over directions each step vector is tried with both signs. Unless the piece is a Pawn, of course. This explains the obfuscated expression p>2&r<0?-r:-o[++j] for the next direction r: first the negative version is tried, and the second time such a negative value is negated and used again.

#### The Loop over Squares

Once a move vector r is selected, the inner loop of the move generator repeatedly adds this to the location of the piece, to sweep the destination square y over the board along a ray. Each new y is tested for being on the board (y&M==0), and if it is, the piece t on the destination square (t=b[y]) is checked. If it is one of our own or if we fall off the board, we break out of the loop immediately. If the piece belongs to the opponent, the move is a capture, and in general valid. So in that case we finish that iteration of the loop, but the test at the end of this do-while loop only continues looping if there was no capture (t==0, written compactly as !t).

For crawling pieces the loop has to terminate after the first iteration irrespective of the fact if the last move was a capture or not. To this end we fake a capture for those pieces (p<5) at the end of the loop, by incrementing t. We still have to deal with the complication that some of the moves generated for pawns are forbidden. In particular straight pawn moves can't be captures, and diagonal moves can't be non-captures. We therefore break out of the loop at the beginning if we detect that the emptyness of the target sqaure (quantified by the expression !t) matches the straightness of the move vector (quantified as the expression !(r&7), i.e. if the least significant bits of the move vector, the file displacement, equals zero or not).

Below the basic move-generator code is highlighted:

```c

/***************************************************************************/
/*                               micro-Max,                                */
/* A chess program smaller than 2KB (of non-blank source), by H.G. Muller  */
/***************************************************************************/
/* version 3.2 (2000 characters) features:                                 */
/* - recursive negamax search                                              */
/* - quiescence search with recaptures                                     */
/* - recapture extensions                                                  */
/* - (internal) iterative deepening                                        */
/* - best-move-first 'sorting'                                             */
/* - a hash table storing score and best move                              */
/* - full FIDE rules (expt minor ptomotion) and move-legality checking     */

#define F(I,S,N) for(I=S;I<N;I++)
#define W(A) while(A)
#define K(A,B) *(int*)(T+A+(B&8)+S*(B&7))
#define J(A) K(y+A,b[y])-K(x+A,u)-K(H+A,t)

#define U 16777224
struct _ {int K,V;char X,Y,D;} A[U];           /* hash table, 16M+8 entries*/

int V=112,M=136,S=128,I=8e3,C=799,Q,N,i;       /* V=0x70=rank mask, M=0x88 */

char O,K,L,
w[]={0,1,1,3,-1,3,5,9},                        /* relative piece values    */
o[]={-16,-15,-17,0,1,16,0,1,16,15,17,0,14,18,31,33,0, /* step-vector lists */
     7,-1,11,6,8,3,6,                          /* 1st dir. in o[] per piece*/
     6,3,5,7,4,5,3,6},                         /* initial piece setup      */
b[129],                                        /* board: half of 16x8+dummy*/
T[1035],                                       /* hash translation table   */

n[]=".?+nkbrq?*?NKBRQ";                        /* piece symbols on printout*/

D(k,q,l,e,J,Z,E,z,n)    /* recursive minimax search, k=moving side, n=depth*/
int k,q,l,e,J,Z,E,z,n;  /* (q,l)=window, e=current eval. score, E=e.p. sqr.*/
{                       /* e=score, z=prev.dest; J,Z=hashkeys; return score*/
 int j,r,m,v,d,h,i=9,F,G;
 char t,p,u,x,y,X,Y,H,B;
 struct _*a=A;
                                               /* lookup pos. in hash table*/
 j=(k*E^J)&U-9;                                /* try 8 consec. locations  */
 W((h=A[++j].K)&&h-Z&&--i);                    /* first empty or match     */
 a+=i?j:0;                                     /* dummy A[0] if miss & full*/
 if(a->K)                                      /* hit: pos. is in hash tab */
 {d=a->D;v=a->V;X=a->X;                        /* examine stored data      */
  if(d>=n)                                     /* if depth sufficient:     */
  {if(v>=l|X&S&&v<=q|X&8)return v;             /* use if window compatible */
   d=n-1;                                      /* or use as iter. start    */
  }X&=~M;Y=a->Y;                               /*      with best-move hint */
  Y=d?Y:0;                                     /* don't try best at d=0    */
 }else d=X=Y=0;                                /* start iter., no best yet */
 N++;                                          /* node count (for timing)  */
 W(d++<n|z==8&N<1e7&d<98)                      /* iterative deepening loop */
 {x=B=X;                                       /* start scan at prev. best */
  Y|=8&Y>>4;                                   /* request try noncastl. 1st*/
  m=d>1?-I:e;                                  /* unconsidered:static eval */
  do{u=b[x];                                   /* scan board looking for   */
   if(u&k)                                     /*  own piece (inefficient!)*/
   {r=p=u&7;                                   /* p = piece type (set r>0) */
    j=o[p+16];                                 /* first step vector f.piece*/
    while(r=p>2&r<0?-r:-o[++j])                /* loop over directions o[] */
    {A:                                        /* resume normal after best */
     y=x;F=G=S;                                /* (x,y)=move, (F,G)=castl.R*/
     do{H=y+=r;                                /* y traverses ray          */
      if(Y&8)H=y=Y&~M;                         /* sneak in prev. best move */
      if(y&M)break;                            /* board edge hit           */
      if(p<3&y==E)H=y^16;                      /* shift capt.sqr. H if e.p.*/
      t=b[H];if(t&k|p<3&!(r&7)!=!t)break;      /* capt. own, bad pawn mode */
      i=99*w[t&7];                             /* value of capt. piece t   */
      if(i<0||E-S&&b[E]&&y-E<2&E-y<2)m=I;      /* K capt. or bad castling  */
      if(m>=l)goto C;                          /* abort on fail high       */
    
      if(h=d-(y!=z))                           /* remaining depth(-recapt.)*/
      {v=p<6?b[x+8]-b[y+8]:0;                  /* center positional pts.   */
       b[G]=b[H]=b[x]=0;b[y]=u&31;             /* do move, strip virgin-bit*/
       if(!(G&M)){b[F]=k+6;v+=30;}             /* castling: put R & score  */
       if(p<3)                                 /* pawns:                   */
       {v-=9*(((x-2)&M||b[x-2]!=u)+            /* structure, undefended    */
              ((x+2)&M||b[x+2]!=u)-1);         /*        squares plus bias */
        if(y+r+1&S){b[y]|=7;i+=C;}             /* promote p to Q, add score*/
       }
       v=-D(24-k,-l-(l>e),m>q?-m:-q,-e-v-i,    /* recursive eval. of reply */
            J+J(0),Z+J(8)+G-S,F,y,h);          /* J,Z: hash keys           */
       v-=v>e;                                 /* delayed-gain penalty     */
       if(z==9)                                /* called as move-legality  */
       {if(v!=-I&x==K&y==L)                    /*   checker: if move found */
        {Q=-e-i;O=F;return l;}                 /*   & not in check, signal */
        v=m;                                   /* (prevent fail-lows on    */
       }                                       /*   K-capt. replies)       */
       b[G]=k+38;b[F]=b[y]=0;b[x]=u;b[H]=t;    /* undo move,G can be dummy */
       if(Y&8){m=v;Y&=~8;goto A;}              /* best=1st done,redo normal*/
       if(v>m){m=v;X=x;Y=y|S&G;}               /* update max, mark with S  */
      }                                        /*          if non castling */
      t+=p<5;                                  /* fake capt. for nonsliding*/
      if(p<3&6*k+(y&V)==S                      /* pawn on 3rd/6th, or      */
          ||(u&~24)==36&j==7&&                 /* virgin K moving sideways,*/
          G&M&&b[G=(x|7)-(r>>1&7)]&32          /* 1st, virgin R in corner G*/
          &&!(b[G^1]|b[G^2])                   /* 2 empty sqrs. next to R  */
      ){F=y;t--;}                              /* unfake capt., enable e.p.*/
     }while(!t);                               /* if not capt. continue ray*/
  }}}while((x=x+9&~M)-B);                      /* next sqr. of board, wrap */
C:if(m>I/4|m<-I/4)d=99;                        /* mate is indep. of depth  */
  m=m+I?m:-D(24-k,-I,I,0,J,K,S,z,1)/2;         /* best loses K: (stale)mate*/
  if(!a->K|(a->X&M)!=M|a->D<=d)                /* if new/better type/depth:*/
  {a->K=Z;a->V=m;a->D=d;A->K=0;                /* store in hash,dummy stays*/
   a->X=X|8*(m>q)|S*(m<l);a->Y=Y;              /* empty, type (limit/exact)*/
  }                                            /*    encoded in X S,8 bits */
/*if(z==8)printf("%2d ply, %9d searched, %6d by (%2x,%2x)\n",d-1,N,m,X,Y&0x77);*/
 }
 if(z&8){K=X;L=Y&~M;}
 return m;                                     
}

main()
{
 int j,k=8,*p,c[9];

 F(i,0,8)
 {b[i]=(b[i+V]=o[i+24]+40)+8;b[i+16]=18;b[i+96]=9;   /* initial board setup*/
  F(j,0,8)b[16*j+i+8]=(i-4)*(i-4)+(j-3.5)*(j-3.5);   /* center-pts table   */
 }                                                   /*(in unused half b[])*/
 F(i,M,1035)T[i]=random()>>9;

 W(1)                                                /* play loop          */
 {F(i,0,121)printf(" %c",i&8&&(i+=7)?10:n[b[i]&15]); /* print board        */
  p=c;W((*p++=getchar())>10);                        /* read input line    */
  N=0;
  if(*c-10){K=c[0]-16*c[1]+C;L=c[2]-16*c[3]+C;}else  /* parse entered move */
   D(k,-I,I,Q,1,1,O,8,0);                            /* or think up one    */
  F(i,0,U)A[i].K=0;                                  /* clear hash table   */
  if(D(k,-I,I,Q,1,1,O,9,2)==I)k^=24;                 /* check legality & do*/
 }
}

```

