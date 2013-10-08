---
layout: post
title: "Something about Chess series with AI"
date: 2013-06-23 20:48
comments: true
categories: Algorithm
---
Preface
---
One day, I want to learn some knowledge about the Game AI topic.
Chess series is supposed to a big collection in Game.
Xiangqi is my fav chess game, and I am familiar with it.
It's a good choice.

Besides, six years ago, I have developed xiangqi program both in java & C++.
I can use it to learn AI engine ,and the program developed before , can be seen as a GUI.
Techinically, it will be OK then.
Reality is cruel, the Java version is lost at home, and the C++ version have to run on Window, not my Mac OSX.

Chess & AI
------

<!-- more -->

[Chess](http://www.chessvariants.org/d.chess/chess.html) is a two-player strategy board game played on a chessboard, a checkered gameboard with 64 squares arranged in an eight-by-eight grid.   
It is one of the world's most popular games, played by millions of people worldwide at home, in clubs, online, by correspondence, and in tournaments.

So if chess has AI, then we can compete with Computer.  

More interestingly, we can let two Computer with AI fight with each other.

There are all kinds of chess game, which seems alike.

They have these Feature:
* board with X * Y
* pieces with some rule

Chess(International)
http://www.chessvariants.org/d.chess/chess.html

Xiangqi(Chinese Chess)
http://www.chessvariants.org/xiangqi.html


Xboard & FairyMax
----
Xboard is a chess program, FairyMax is a chess engine(AI)

[Xboard](http://www.gnu.org/software/xboard/) is a graphical user interface for chess in all its major forms,   
including international chess, xiangqi (Chinese chess), shogi (Japanese chess) and Makruk,   
in addition to many minor variants such as Losers Chess, Crazyhouse, Chess960 and Capablanca Chess.  

[FairyMax](http://home.hccnet.nl/h.g.muller/CVfairy.html)
FairyMax is developed by H.G.Muller, who is super fan of chess.
Maxqi is the xiangqi engine.

Learing 
---------------------

#####First , install xboard & Fairymax

Here is a way:

[https://github.com/Homebrew/homebrew-games]()

#####Second Problem is how to run xboard....

Don't underestimate it.
It takes me lots of time to test it.

Help information? xboard help?     It does not work   
The output is full of useless infomation

Xboard web help? User guide ? It does not work.  
http://www.gnu.org/software/xboard/user_guide/UserGuide.html

at last, I have to learn how to run, From an appication on Window..

OMG....

1, copy the xboard.ini to be used as xboard.rc

2, then I got the knowledge, the viariant arg...supposed to be the xiangqi

3, use fcp can call the maxqi. from user guide

now, it almost works but have a problem
xiangqi can compatible with maxqi engine

4, firstProtocolVersion is need.

ok,then you can run xboard

```
	xboard -variant xiangqi -firstProtocolVersion 1 -fcp maxqi
```

#####review the maxqi code
Now the biggest problem,which I will face, is coming....

Why?

Becaure somebody want to program the engine with 1200 characters.

Here is the code, are you crazy? especially, the Function D().....

I got the code little by little, and hesitate when D() function come to me...

```c Function D http://fairymax.sourcearchive.com/documentation/4.8q-2/maxqi_8c_source.html Source Article

D(k,q,l,e,z,n)          /* recursive minimax search, k=moving side, n=depth*/
    int k,q,l,e,z,n;        /* (q,l)=window, e=current eval. score, E=e.p. sqr.*/
{                       /* e=score, z=prev.dest; J,Z=hashkeys; return score*/
    int j,r,m,v,d,h,i,P,V,f=J,g=Z,C,s,flag,F;
    unsigned char t,p,u,x,y,X,Y,B,lu;
    struct _*a=A+(J+k&U-1);                       /* lookup pos. in hash table*/
    q-=q<e;l-=l<=e;                               /* adj. window: delay bonus */
    d=a->D;m=a->V;F=a->F;                         /* resume at stored depth   */
    X=a->X;Y=a->Y;                                /* start at best-move hint  */
    if(z&S&&a->K==Z)printf("# root hit %d %d %x\n",a->D,a->V,a->F);
    if(a->K-Z|z&S  |                              /* miss: other pos. or empty*/
            !(m<=q|F&8&&m>=l|F&S))                       /*   or window incompatible */
        d=X=0,Y=-1;                                  /* start iter. from scratch */
    W(d++<n||d<3||              /*** min depth = 2   iterative deepening loop */
            z&S&&K==I&&(GetTickCount()-Ticks<tlim&d<=MaxDepth|| /* root: deepen upto time   */
                (K=X,L=Y,d=3)))                             /* time's up: go do best    */
    {x=B=X;lu=1;                                  /* start scan at prev. best */
        h=Y-255;                                       /* if move, request 1st try */
        P=d>2&&l+I?D(16-k,-l,1-l,-e,2*S,d-3):I;      /* search null move         */
        m=-P<l|R<5?d-2?-I:e:-P;   /*** prune if > beta  unconsidered:static eval */
        N++;                                         /* node count (for timing)  */
        do{u=b[x];                                   /* scan board looking for   */
            if(u)m=lu|u&15^3?m:(d=98,I),lu=u&15^3;        /* Kings facing each other  */
            if(u&&(u&16)==k)                            /*  own piece (inefficient!)*/
            {r=p=u&15;                                  /* p = piece type (set r>0) */
                j=od[p];                                   /* first step vector f.piece*/
                W(r=o[++j])                                /* loop over directions o[] */
                {A:                                        /* resume normal after best */
                    flag=h?3:of[j];                           /* move modes (for fairies) */
                    y=x;                                      /* (x,y)=move               */
                    do{                                       /* y traverses ray, or:     */
                        y=h?Y:y+r;                               /* sneak in prev. best move */
                        if(y>=16*BH|(y&15)>=BW)break;            /* board edge hit           */
                        t=b[y];                                  /* captured piece           */
                        if(flag&1+!t)                            /* mode (capt/nonc) allowed?*/
                        {if(t&&(t&16)==k||flag>>10&zn[y])break;  /* capture own or bad zone  */
                            i=w[t&15];                              /* value of capt. piece t   */
                            if(i<0)m=I,d=98;                        /* K capture                */
                            if(m>=l&d>1)goto C;                     /* abort on fail high       */
                            v=d-1?e:i-p;                            /*** MVV/LVA scoring if d=1**/
                            if(d-!t>1)                              /*** all captures if d=2  ***/
                            {v=centr[p]?b[x+257]-b[y+257]:0;        /* center positional pts.   */
                                b[x]=0;b[y]=u;                         /* do move                  */
                                v-=w[p]>0|R<10?0:20;                   /*** freeze K in mid-game ***/
                                if(p<3)                                /* pawns:                   */
                                {v+=2;                                 /* end-game Pawn-push bonus */
                                    if(zn[x]-zn[y])b[y]+=5,               /* upgrade Pawn and         */
                                        i+=w[p+5]-w[p];                      /*          promotion bonus */
                                }
                                if(z&S && GamePtr<6) v+=(rand()>>10&31)-16; // randomize in root
                                J+=J(0);Z+=J(4);
                                v+=e+i;V=m>q?m:q;                      /*** new eval & alpha    ****/
                                C=d-1-(d>5&p>2&!t&!h);                 /* nw depth, reduce non-cpt.*/
                                C=R<10|P-I|d<3||t&&p-3?C:d;            /* extend 1 ply if in-check */
                                do
                                    s=C>2|v>V?-D(16-k,-l,-V,-v,/*** futility, recursive eval. of reply */
                                            0,C):v;
                                W(s>q&++C<d); v=s;                     /* no fail:re-srch unreduced*/
                                if(z&S&&K-I)                           /* move pending: check legal*/
                                {if(v+I&&x==K&y==L)                    /*   if move found          */
                                    {Q=-e-i;
                                        a->D=99;a->V=500;                    /* lock game in hash as loss*/
                                        R-=i/FAC;                            /*** total captd material ***/
                                        Fifty = t|p<3?0:Fifty+1;
                                        return l;}                /*   & not in check, signal */
                                        v=m;                                  /* (prevent fail-lows on    */
                                }                                      /*   K-capt. replies)       */
                                J=f;Z=g;
                                b[y]=t;b[x]=u;                         /* undo move                */
                            }                                       /*          if non-castling */
                            if(v>m)                                 /* new best, update max,best*/
                                m=v,X=x,Y=y;                           /* no marking!              */
                            if(h){h=0;goto A;}                      /* redo after doing old best*/
                        }
                        s=t;
                        t+=flag&4;                               /* fake capt. for nonsliding*/
                        if(s&&flag&8)t=0,flag^=flag>>4&15;       /* hoppers go to next phase */
                        if(!(flag&S))                            /* zig-zag piece?           */
                            r^=flag>>12,flag^=flag>>4&15;           /* alternate vector & mode  */
                    }W(!t);                                   /* if not capt. continue ray*/
                }}
                if((++x&15)>=BW)x=x+16&240,lu=1;            /* next sqr. of board, wrap */
                if(x>=16*BH)x=0;
        }W(x-B);           
C:if(a->D<99)                                  /* protect game history     */
      a->K=Z,a->V=m,a->D=d,a->X=X,                /* always store in hash tab */
          a->F=8*(m>q)|S*(m<l),a->Y=Y;                /* move, type (bound/exact),*/
  if(z&S&&Post){
      printf("%2d ",d-2);
      printf("%6d ",m);
      printf("%8d %10d %c%c%c%c\n",(GetTickCount()-Ticks)/10,N,
              'i'-(X>>4&15),'9'-(X&15),'i'-(Y>>4&15),'9'-(Y&15)),fflush(stdout);}
    }                                             /*    encoded in X S,8 bits */
    if(z&4*S)K=X,L=Y&~S;
    return m+=m<e;                                /* delayed-loss bonus       */
}

```

Then I think for a while....

maxqi engine is so pop, why nobody discuss this??

why no notes? Google maxqi,no good results.

Then I Google fairymax, still no good results...

At last, I saw that the fairymax derived from Micro-Max...

then ,i got everything....

##### [micro-max](http://home.hccnet.nl/h.g.muller/max-src2.html)


####### Basic Data Representations

* Piece Encoding
* Board Representation
* Move Generation

####### Basic Moves

* En Passant Capture
* Castling
* Pawn Promotions

####### Search Algorithm

* Alpha-Beta Minimax
* Quiescence Search
* Do and Undo Move
* Iterative Deepening
* Move Sorting

####### Transposition Table

* Hashing
* Replacement

#######Evaluation
* Material
* Piece-Square Table
* Checkmate and Stalemate
* Delay Penalty

####### The Interface
Move Legality Checking






