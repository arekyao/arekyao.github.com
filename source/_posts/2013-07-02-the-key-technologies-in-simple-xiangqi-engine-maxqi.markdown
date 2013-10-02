---
layout: post
title: "The Key Technologies in Simple XiangQi Engine MaxQi"
date: 2013-07-02 08:30
comments: true
categories: TechSpark
---

1,mini-max algorithm
----

minimize the opponent's potential max profit.

easy to understand,right?

2,alpha-beta cutoff
--------

 It stops completely evaluating a move when at least one possibility has been found that proves the move to be worse than a previously examined move.    
 Such moves need not be evaluated further.   
 When applied to a standard minimax tree, it returns the same move as minimax would, but prunes away branches that cannot possibly influence the final decision

```
function alphabeta(node, depth, a, b, Player)        
    if depth = 0 or node is a terminal node
        return the heuristic value of node
    if Player = MaxPlayer
        for each child of node
            a := max(a, alphabeta(child, depth-1, a, b, not(Player) ))    
            if b <= a
                break                             (* Beta cut-off *)
        return a
    else
        for each child of node
            b := min(b, alphabeta(child, depth-1, a, b, not(Player) ))    
            if b <= a
                break                             (* Alpha cut-off *)
        return b
(* Initial call *)
alphabeta(origin, depth, -infinity, +infinity, MaxPlayer)
```


3,Quiescence search
-----

When have to recapture , it have to search until there is no tradeoff

4,Iterative search
-------

the key is the search depth.

The efficiency of the alpha-beta search is strongly enhanced (in terms of the number of nodes searched)   
if the best move is tried first, and trying idiotic moves first (such as both Queens getting berserk) can easily lead to an explosion of the search.  

Rather than aggressively starting a search of the required depth, proceed carefully and first invest some time doing a search of shallower depth.  

What it can get ,through the shallower depth search , the window (alpha-beta,the min & max) and the best move, which is supposed to search first in deepen search

5, Hash table
------

Use the result which has been faced last time.

So it is needed some hash key to describe the whole board 

The Zobrist Keys show up!

6,Zobrist hashing
-----

Zobrist hashing starts by randomly generating bitstrings for each possible element of a board game

The final Zobrist hash is computed by combining those bitstrings using bitwise XOR.

the good is the hash key is recalculated by two "xor"


7,Monte Carlo methods
-----

This method can be used to estimate the board status(which side is at better thant the other side)

Monte Carlo methods vary, but tend to follow a particular pattern:

  a). Define a domain of possible inputs.  
  b). Generate inputs randomly from a probability distribution over the domain.  
  c). Perform a deterministic computation on the inputs.  
  d). Aggregate the results.  

However, in maxqi, it's not used.  
The other way the piece's weight sum is used to estimate.
