---
title: isValidMove
layout: words
---


------

## isValidMove function structure

For me, there are two main ways to look at this function: either in
terms of "what move are they checking?" [DRAW_CARD vs PLAY_CARD vs
END_TURN], or in terms of "what situation/stage are we in at the
moment?" [start-of-their-turn vs previously-played-a-card vs
previously-drew-a-card].

<img
src="https://www.cse.unsw.edu.au/~andrewb/thoughts//static/valid_moves_fcd_18s2.png"
style="max-width: 800px">


For both of these, there are three very discrete situations, which don't
interact/overlap with each other at all: your move is either
DRAW_CARD or PLAY_CARD or END_TURN, it can't be more than one; and you
either previously played a card / drew a card / are at the start of your
turn, it can't be more than one of those either.

Because there's absolutely no overlap between each of these, this is a
good example of when you can just **split your code up into three (in this
case) functions, and just call those functions from isValidMove**.

So, your isValidMove would look something like:

```c
int isValidMove(....) {
    if (move is DRAW_CARD) {
        // call some function to check if it's valid to draw a card
    } else if (move is PLAY_CARD) {
        // call some function to check if it's valid to play a card
    } else if (move is END_TURN) {
        // call some function to check if it's valid to end your turn
    } else {
        // they passed in an invalid action
    }
    // return whether or not it was valid
}
```

(except better variable/function names, and actual C syntax)

This also means that you can hide the "irrelevant" logic from each of
them, e.g. in your function that checks whether it's valid to draw a
card, you don't have to do anything related to e.g. "does this card
match what's on top of the discard pile?", so each of those functions
can be quite "focused" on what they're doing.

-------

The next step would be to work out the logic for each of these
functions. 

If we think about **END_TURN**, for example, whether or not it's valid to end your turn will depend on a few things.

If you have a look at the above diagram:
  - it's not valid to end your turn if you're at the start of your turn (as there's no arrow from "start of turn" to "END_TURN")
  - it's always valid to end your turn if you've just played a card (since there's a plain arrow between "PLAY_CARD" and "END_TURN", so there's no condition to check)
  - it _might be_ valid to end your turn if you've just drawn a card -- depending on some certain conditions (e.g. if a DRAW_TWO was played and you haven't drawn enough cards yet)



-------

##### Nested if statements, "rightwards drift", and "flat" logic structure

Something that's often good to aim for (and can be hard to achieve) is
to have quite a "flat" logic structure, in terms of nested if statements
etc.

As an example, imagine you had some code that was working with
dates/times/locations, and you had to check whether the
date/time/location you've been given is valid.

One way you could structure that would be:

```c
// 'day' = number between 1-31, 'month' = number between 1-12,
// 'year' = let's say 2010-2019 (inclusive) int valid;
if (day >= 1) {
    if (day <= 31) {
        if (month >= 1) {
            if (month <= 12) {
                if (year >= 2010) {
                    if (year <= 2019) {
                        valid = TRUE;
                    } else {
                        valid = FALSE;
                    }
                } else {
                    valid = FALSE;
                }
            } else {
                valid = FALSE;
            }
        } else {
            valid = FALSE;
        }
    } else {
        valid = FALSE;
    }
} else {
    valid = FALSE;
}
```

Hopefully you can see that that's a bit ridiculous; this is what we
might call "rightward drift", where the code slowly goes further and
further and further to the right.

However, we could write the exact same logic in a "flat" way:

```c
if (day < 1 || day > 31) {
    valid = FALSE;
} else if (month < 1 || month > 12) {
    valid = FALSE;
} else if (year < 2010 || year > 2019) {
    valid = FALSE;
} else {
    valid = TRUE;
}
```

If you can limit yourself to as few levels of nesting as possible (or at
least to avoiding unnecessary nesting when there's an equally clear or
even better way to write your code without needing to n have so many
nested ifs), your code will often be a lot easier to read.

(of course, it's possible to take it too far, and to make your code
worse by trying to shove everything into single if statements -- you
start to get an intuition for what would be good to do when)

------

### Back to isValidMove

<!--
Thinking about the PLAY_CARD logic from above, in a similar way:

> 1. are we actually allowed to play a card at the moment (in general)?
> this is where the second set of "situations" from before comes in:
> if you've already played a card this turn: nope!
> if you've already drawn a card this turn: nope!
> if you're at the start of your turn: yes

we could write this like

```c
if (haven't played a card yet this turn) {
    if (haven't drawn a card yet this turn) {
        if (whatever else) {
            // potentially valid to play a card
            // (mentally insert all of the TRUE/FALSE chains from before to close each of the if statements)
```

But you could also write it in a "flat" way:

```c
if (we have played a card already this turn) {
    // definitely not valid to play another card
} else if (we have drawn a card already thus turn) {
    // definitely not valid to play a card
} else {
    // potentially valid to play a card...
}
```

-->

-------

In terms of approaching isValidMove the other way around: break the situation up in terms of "what have you done in your turn so far".

```c
int blah;
if (at the start of turn) {
    blah = isItValidToDoThisAtTheStartOfYourTurn(...);
} else if (previously played a card) {
    blah = isItValidToDoThisIfYouJustPlayedACard(...);
} else if (previously drew a card) {
    blah = isItValidToDoThisIfIfYouPreviouslyDrewACard(...);
}
return blah;
```

(again, silly/excessive variable/function names)

The logic for each of those situations then looks at what move you're
trying to make, and again comes from the diagram:

<img
src="https://www.cse.unsw.edu.au/~andrewb/thoughts//static/valid_moves_fcd_18s2.png"
style="max-width: 800px">



**if you're at the start of your turn**:
  - it's definitely valid to draw a card (arrow with no condition on it)
  - it's maybe valid to play a card depending on certain conditions (e.g. you have a valid card and you don't have to draw cards from a DRAW_TWO)
  - it's not valid to end your turn (no arrow)

**if you've already played a card**:
  - definitely not valid to draw a card or play a card (no arrow)
  - only valid to end your turn

**if you've already drawn a card**:
  - definitely not valid to play a card (no arrow)
  - only valid to draw a card if you need to draw more (the blue arrow looping back to itself)
  - only valid to end your turn if you've drawn "enough" cards (the green arrow)

(there are some more edge cases that aren't mentioned in there, but hopefully this gets the idea across)

----
Once you're at this sort of level of detail, this is where those
function-if-statement conditions come in handy: e.g. if we're
considering the **"you're at the start of your turn" situation**:

```c
if (you want to make the END_TURN move) {
    // definitely not valid -- start of your turn!
if (you want to make the DRAW_CARD move) {
    // that's definitely valid
} else if (you want to make the PLAY_CARD move) {
    // that's only valid if you don't need to draw cards, or if you do but it's a draw two
    if (you need to draw cards because a DRAW_TWO was played) {
        // need to draw more cards, so not valid
    } else if (...... whatever else you might need to check / rule out....) {
         // not valid
    } else {
        // we've ruled out the "you need to draw more cards",
        // (and anything else you needed to rule out), so we know that it is valid!
    }
}
```

<!--
Note: This is an example of where having a second level of nesting makes
sense -- there are the three different situations to consider
(END_TURN/DRAW_CARD/PLAY_CARD), and then within each of those there are
specific things that you need to check depending on the situation.
But you wouldn't want to have any more if statements nested inside these
ones!)

-------

-->
