---
layout: post
title: "Something Interesting about AI"
date: 2013-04-21 19:53
comments: true
categories: TechSpark
tags: [AI,JustForFun]
---

* [Generate Music Algorithmically](http://www.databozo.com/2013/04/12/Generating_music_algorithmically.html)
------

###Music Feature

Music is compose by lots of note.  
The feature of note is frequece and length.

###Markov Chain

The idea tech of Markov Chain is that, the next state (to us,it's music note) is decided by the previous N states, only.
<!-- more -->
That means:  
the next note's freq is decided by the previous's.
the next note's length is decided by the previous's.



###Machine Learning

First, learn the trans probability throught lots of music sample.

Then, we got the probability from Freq A to Freq B, From length 1/2 to 1/4

Last, generate note by probability, random. Create music file according to the note data.

###Code

The lib "pysynth.py" for gen music through two arg (freq and length)

The key program "MarkovBuilder.py" for TransArray&GenerateNote

```py

'''
Created on May 14, 2009

@author: darkxanthos
'''
import random

class MarkovBuilder:
    def __init__(self, value_list):
        self._values_added = 0
        self._reverse_value_lookup = value_list
        self._value_lookup = {}
        for i in range(0, len(value_list)):
            self._value_lookup[value_list[i]] = i
        #Initialize our adjacency matrix with the initial
        #probabilities for note transitions.
        self._matrix=[[0 for x in range(0,len(value_list))] for i in range(0,len(value_list))]

    def add(self, from_value, to_value):
        """Add a path from a note to another note. Re-adding a path between notes will increase the associated weight."""
        value = self._value_lookup
        self._matrix[value[from_value]][value[to_value]] += 1
        self._values_added = self._values_added + 1
        
    def next_value(self, from_value):
        value = self._value_lookup[from_value]
        value_counts = self._matrix[value]
        value_index = self.randomly_choose(value_counts)
        if(value_index < 0):
            raise RuntimeError, "Non-existent value selected."
        else:
            return self._reverse_value_lookup[value_index]
            
    def randomly_choose(self, choice_counts):
        """Given an array of counts, returns the index that was randomly chosen"""
        counted_sum = 0
        count_sum = sum(choice_counts)
        selected_count = random.randrange(1, count_sum + 1)
        for index in range(0, len(choice_counts)):
            counted_sum += choice_counts[index]
            if(counted_sum >= selected_count):
                return index
        raise RuntimeError, "Impossible value selection made. BAD!"


```


Main Program "MarkovMusic.py"

```

'''
Created on May 12, 2009

@author: Justin Bozonier
'''
import pysynth
from MarkovBuilder import MarkovBuilder

class MusicMatrix:
    def __init__(self):
        self._previous_note = None
        self._markov = MarkovBuilder(["a", "a#", "b", "c", "c#", "d", "d#", "e", "f", "f#", "g", "g#"])
        self._timings = MarkovBuilder([1, 2, 4, 8, 16])

    def add(self, to_note):
        """Add a path from a note to another note. Re-adding a path between notes will increase the associated weight."""
        if(self._previous_note is None):
            self._previous_note = to_note
            return
        from_note = self._previous_note
        self._markov.add(from_note[0], to_note[0])
        self._timings.add(from_note[1], to_note[1])
        self._previous_note = to_note
        
    def next_note(self, from_note):
        return [self._markov.next_value(from_note[0]), self._timings.next_value(from_note[1])]

# Playing it comes next :)
#test = [['c',4], ['e',4], ['g',4], ['c5',1]]
#pysynth.make_wav(test, fn = "test.wav")

musicLearner = MusicMatrix()

# Input the melody of Row, Row, Row Your Boat
# The MusicMatrix will automatically use this to 
# model our own song after it.
musicLearner.add(["c", 4])
musicLearner.add(["c", 4])
musicLearner.add(["c", 4])
musicLearner.add(["d", 8])
musicLearner.add(["e", 4])
musicLearner.add(["e", 4])
musicLearner.add(["d", 8])
musicLearner.add(["e", 4])
musicLearner.add(["f", 8])
musicLearner.add(["g", 2])

musicLearner.add(["c", 8])
musicLearner.add(["c", 8])
musicLearner.add(["c", 8])

musicLearner.add(["g", 8])
musicLearner.add(["g", 8])
musicLearner.add(["g", 8])

musicLearner.add(["e", 8])
musicLearner.add(["e", 8])
musicLearner.add(["e", 8])

musicLearner.add(["c", 8])
musicLearner.add(["c", 8])
musicLearner.add(["c", 8])

musicLearner.add(["g", 4])
musicLearner.add(["f", 8])
musicLearner.add(["e", 4])
musicLearner.add(["d", 8])
musicLearner.add(["c", 2])

random_score = []
current_note = ["c", 4]
for i in range(0,100):
    print current_note[0] + ", " + str(current_note[1])
    current_note = musicLearner.next_note(current_note)
    random_score.append(current_note)

pysynth.make_wav(random_score, fn = "first_score.wav")

```



code src:   
[http://github.com/jcbozonier/MarkovMusic/tree/master](http://github.com/jcbozonier/MarkovMusic/tree/master)


* [AI Snake](http://hawstein.com/posts/snake-ai.html)
------

####Strategy 1

If Target is the food, choose the shortest path

####Strategy 2

If Target is the tail, choose the longest path

code src:  
[https://github.com/Hawstein/snake-ai]()

* [AI 'solves' Super Mario Bros. and other classic NES games](http://www.wired.co.uk/news/archive/2013-04/12/super-mario-solved)
-------


<embed src="http://player.youku.com/player.php/sid/XNTQzMTM1NDky/v.swf" allowFullScreen="true" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" type="application/x-shockwave-flash">

[http://v.youku.com/v_show/id_XNTQzMTM1NDky.html]()

[http://www.cs.cmu.edu/~tom7/mario/mario.pdf]()



