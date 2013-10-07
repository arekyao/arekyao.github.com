---
layout: page
title: 基本数据表示 Basic Data Representations 
footer: false
---




[棋子编码 Piece Encoding](http://home.hccnet.nl/h.g.muller/encode.html)
---------


每个棋子都被编码成整数，（这就跟二进制数字电脑上的其它东西一样），当然最终都是 bits。  
这些编码利用二进制的特定位去表示特定含义：  

*  第8位，如果为1，表示是白方   
*  第16位，如果为1，表示是黑方  

这就使得可以通过位并运算（u&8），很容易去判断棋子是否为白方棋子。

代码中变量 k，用于表示哪一方该出手。
k=8，表示白方轮；k=16表示黑方轮。
这就可以通过 u&k 操作来判断应该由哪一方棋子走

* 第32位，如果为1，表示是该棋子是否处于最初的位子（virgin bit）。
有这个位，是考虑到后续有『王车易位』的操作；这个位对于兵&卒而言，并无意义；

Pieces are encoded as integers, which (like anything else on a binary digital computer) are eventually bitpatterns.  The encoding makes use of the binary representation by assigning a specific meaning to some bits of the integer encoding: the '8'-bit is used (i.e. set) to indicate a white piece, the '16'-bit indicates a black piece. This makes it easy to test if a piece u belongs to white, by bitwise anding with 8 (u&8). The code uses a variable k to indicate the side that should move, which has the value 8 on white's turn, or 16 for black, so that u&k tells us if a piece belongs to the side to move. The '32'-bit is used to indicate that a piece is in the original position (the 'virgin bit'). (Since this is only important for Kings and Rooks in connection with castling, Micro-Max doesn't bother to set the virgin bit on pawns.


* 低3位的 bit：用于表示棋子类型（0-7，表示车，马，像，后，王等），与棋子的归属方（白方，黑方）无关。  
基于上述内容，则可知，通过位运算（u&7）获得棋子类型。  
用于表示棋子名称的数组（角色，代表棋子类型的，会被打印展示的），可以很简单的试用低4位，作为索引（n[u&15]）  


The three low-order bits can be interpreted as a number 0-7 that indicates the piece type, and have a meaning that is independent of piece color. This makes tables that do not have to distingush by color (like piece value w[u&7]) half as small. Tables that would like to make this distinction (like the character to used to represent the piece on printout) can simply be indexed with the 4 low-order bits (n[u&15]).



往上走的兵和往下走的兵被认为是不同的类型（类型分别为1和2）。  
每方（白或黑）仅有其中1种兵，向合适的方向前进，当然引擎也是不会将兵往回走的。『不然太囧了』

棋子的编码上，基于进一步的考虑到后续更方便的测试棋子属于某个组，所以棋子的编码上，在能够滑行（移动较多格子，不限格式数量）的棋子编码在更高的值.这样使得:  
判断爬行棋子（只能走少数1，2格的）可以通过棋子类型p (=u&7)<5;  
判断棋子能否相棋盘中间走（除 R，Q 外）可以通过p<6;  
判断兵可以通过p<3;  

完整的棋子类型列表为: {1,2,3,4,5,6,7} = {P+,P-,N,K,B,R,Q}  

P 是兵，pawn  
N 是马，knight，骑士  
K 是王，king  
B 是像，bishop，主教  
R 是车，rook  
Q 是皇后，queen  

更多的走法，可见[这里](http://en.wikipedia.org/wiki/Chess#Movement)

* 类型为0，未试用，当然也可以表示在棋盘格子中为空

Upstream moving pawns are considered different pieces than downstream moving pawns (type 1 and 2, respectively). Each side has only one of the types of pawns, moving in the appropriate direction, although the engine would not frown at putting pieces on the board that move 'backwards'. The encoding is further chosen such that tests if a piece belongs to a certain group is easy: the sliding pieces (BRQ) are giving the highest type numbers (5,6,7), so that crawling pieces can be recognized by piece type p (=u&7) p<5. Pieces that are awarded for moving towards the center of the board (everything but RQ) are recognized by p<6, pawns by p<3. The complete list thus is {1,2,3,4,5,6,7} = {P+,P-,N,K,B,R,Q}. Type 0 is not used, so it can indicate empty squares on the board.


如下高亮代码展示了棋子如何使用的在 micro-max 中。『这里很囧，没有高亮』
[更好的看代码的地址](http://home.hccnet.nl/h.g.muller/encode.html)

The following highlights places that show how the piece encoding is used in Micro-Max.


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
    W(r=p>2&r<0?-r:-o[++j])                    /* loop over directions o[] */
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
     }W(!t);                                   /* if not capt. continue ray*/
  }}}W((x=x+9&~M)-B);                          /* next sqr. of board, wrap */
C:if(m>I/4|m<-I/4)d=99;                        /* mate is indep. of depth  */
  m=m+I?m:-D(24-k,-I,I,0,J,Z,S,z,1)/2;         /* best loses K: (stale)mate*/
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

最初的棋盘开局可能也需要一些解释：  
保存在 数组o[]中棋盘类型列表将被拷贝到棋盘的第1行和第8行中。  
在第1行中，40=8+32，这是为了设置白方棋子和初始位  
在第8行中，48=16+32，这是为了设置黑方棋子和初始位  
在18=16+2，9=8+1，这是黑方兵和白方的兵的编码  

The initial boad setup might deserve some explanation: the piece types in the back of the array o[] are copied to the 1st and 8th rank of the board. On the 1st rank 40 = 8 + 32 is added, which sets the 'white' and the 'virgin' bit. On the 8th rank an extra 8 is added, for a total of 48 = 16 + 32, the 'black' and 'virgin' bits. The 18 = 16 + 2 and 9 = 8 + 1 are the encodings for a black P- and a white P+, respectively.


