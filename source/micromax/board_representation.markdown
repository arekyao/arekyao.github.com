---
layout: page
title: 基本数据表示 Basic Data Representations 
footer: false
---


[棋盘 the Board ](http://home.hccnet.nl/h.g.muller/board.html)
-----


###棋盘编码 Square Numbering

棋盘，一个个格子组成，被称为一个'格子数'。『这里也暗含16进制的高位和低位相同』
现在使用的是0x88，这意味着我们能使用16列数据，但是每一行中，只有前面8列是有效的，在使用的，对应着棋盘的8列。  

二维棋盘被映射成一维数组，一行接着一行。
由于这个原因，低4位表示列，高4位表示行。    
这也使得很容易识别棋盘上的每一步是否已经超出棋盘。   

如之前所述，棋子移动到无效的棋盘位子（例如从 h 列到 i 列），  
这会使得第8位为1（第 i-p 列为数字8-15，abcdefgh）。  
更进一步的说，所有超出棋盘的棋子步法，会超出到无效的位中，并且会使得128的 bit（0x80，16进制） 被设置为1。  

格子数的有效性，在此体现无疑，可以通过与0x88（=136）做位的与运算。
这个常数（也是系统的名字，囧），保存在全局变量M中 ，因为它使用得很多，从而节省了大量的字符。  
『该哥们就是千方百计节约字符数呢』
（如果目的不是代码数的多少，而是代码执行速度，这样玩会走向反方向的！）


Squares are designated by a 'square-number'. The '0x88' system is used, meaning that the the board actually has ranks of 16 squares, but only the first 8 squares of each rank are valid and correspond to the 8 files of the board. The two-dimensional board is mapped into a one-dimensional array, rank after rank. Due to this the 4 lowest-order bits of the square number indicate the file, and the higher-order bits the rank. This makes it easy to recognize moves that fall off the board. On the left and they move to invalid square numbers (e.g. from the h-file to the 'i-file'), that have the '8'-bit set (the i-p file have numbers 8-15). Furthermore, all moves that move off the board at the front or back move to invalid rank, and have the 128 bit set (0x80, in hexadecimal). The validity of a square-number can thus be tested by bitwise anding with 0x88 (=136). This constant (which has given the system its name) is held in the global variable M, since it is used quite a lot, thus saving a lot of characters. (If the aim would not be source size, but speed, one would of course do the opposite!)

###棋盘的数组 The Board Array


程序中的主要数据结构就是这个棋盘数组 b[129]，一个一维数组，以格子数为索引。  
由于格子数有较多无用的，所以只有一半的数组真正被用来存储当前棋子的位置。  
8个使用的格子和8个未使用的格子不断交替，最高的有效的格子数是0x77=119。  
一个用格子数0x80=128来表示的虚拟的格子（一个被全局变量 S 所保存的值）也被包括在这个数组中。  

这个格子数有时会被用作表示一个无效的格子（例如，在某变量能表示哪个格子被 e.p.吃掉的是可能的当没有 e.p.吃掉是可能的）   
『这里，e.p.指 :[En passant](http://en.wikipedia.org/wiki/Chess#En_passant) 』
这样能使得在程序非故意的（错误的）保存某些东西时，确保程序不会崩溃掉（使得我们在保存格子数时，不需考虑其有效性的问题）


The principal data structure of the program thus is the board array b[129], a one-dimensional array indexed by square-number. Because of the invalid square-numbers, only half of the array is used to store the current chess position. Blocks of 8 used and 8 unused elements alternate, the highest valid square-number is 0x77=119. A dummy square with square-number 0x80=128 (a value held in the global variable S) is included in the array. This square number is sometimes used to indicate an invalid square (e.g. in the variable that indicates on which square e.p. capture is possible when no e.p. captures are possible), to make sure there are no crashes if the program inadvertently stores something there (saving us the trouble of testing the validity of the square number before we store).

###打印棋盘 Printing the Board




尽管非连续映射到棋盘B[]数组，这是很容易遍历棋盘，跳过无效的。
表达式x = x+9 & ~8，能使x增加至下一个字段。例如，可以使用在第一个for循环的增量。  
由于增加9=8+1代替1平方数的平方'a'-'g'列，'8'位被置位，通过＆〜8处理，它跳过那些无效位。  
但是这样相同的处理'h'列中，还给了第'8'位进位，由于加入1。这将重置第'8'位，并将传递进位到下一行『rank』的位中。   （＆〜8，这又会没有效果。）如果我们想环绕遍历从h8回至a1，则行数rank会溢出，可以使用〜M代替〜8，清除'128'位而不是'8'位。  


Despite the non-contiguous mapping of the chess board into the b[] array, it is quite easy to step through all squares on the board, skipping the invalid ones. The expression to step from a field xto the next field, which can, for instance, be used in th increment part of a for-loop, is x = x+9 & ~8;. Due to adding 9 = 8 + 1 in stead of 1 to a square number of a square in the 'a'-'g' file, its '8'-bit gets set, which is taken care of by the & ~8, which clears it again. But doing the same in the 'h' file also gives a carry into the '8'-bit due to the addition of 1. This resets the '8'-bit, and passes the carry through to the more-significant bits, i.e. the rank number. (The & ~8 then has no effect.) If we want the board scan to 'wrap around' from h8 back to a1, we can and with ~M in stead of ~8, clearing the '128'-bit together with the '8'-bit when the rank number overflows.


在打印出整个棋盘的代码行（in main()函数中），情况有所不同，因为当我们'溢出'到一个新的行『rank』时，也有一个新的换行打印出。  
因此，使用一个常规的for循环（i++），但测试第'8'位是否为1，看是否跑到棋盘的边缘，在这里的printf（）来知道打印什么。  
（一个换行符，10，或能代表棋盘格子的东东，n[b[i]&15]）  如果这个测试为真，一个额外i+=7（其中总是为非零，并因此也始终被认为是真），将我们带回下一个有效棋盘格子。

In the line that prints out the board (in main()) things are done a little differently, because the fact that we 'overflow' to a new rank also has to result in the printing of a new-line. Thus it uses a normal for-loop (with i++ increment), but tests the '8'-bit to see if we run off the board inside the printf() to know what to print (a new-line character, 10, or the representation of the piece on the square, n[b[i]&15]). If this tests true, an extra i += 7 (which always evaluates non-zero, and thus considered true as well) positions us back in front of the next valid square.




下面关键部分已经高亮『当然这里木有如你所想的高亮，请[移步](http://home.hccnet.nl/h.g.muller/board.html) 』

Below the declaration and use of the board are highlighted. (Where the original source contained macro's for for or while loops, these are shown expanded for readability.)


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

 for(i=0;i<8;i++)
 {b[i]=(b[i+V]=o[i+24]+40)+8;b[i+16]=18;b[i+96]=9;   /* initial board setup*/
  F(j,0,8)b[16*j+i+8]=(i-4)*(i-4)+(j-3.5)*(j-3.5);   /* center-pts table   */
 }                                                   /*(in unused half b[])*/
 F(i,M,1035)T[i]=random()>>9;

 W(1)                                                /* play loop          */
 {for(i=0;i<121;i++)printf(" %c",i&8&&(i+=7)?10:n[b[i]&15]); /* print board        */
  p=c;W((*p++=getchar())>10);                        /* read input line    */
  N=0;
  if(*c-10){K=c[0]-16*c[1]+C;L=c[2]-16*c[3]+C;}else  /* parse entered move */
   D(k,-I,I,Q,1,1,O,8,0);                            /* or think up one    */
  F(i,0,U)A[i].K=0;                                  /* clear hash table   */
  if(D(k,-I,I,Q,1,1,O,9,2)==I)k^=24;                 /* check legality & do*/
 }
}

```

