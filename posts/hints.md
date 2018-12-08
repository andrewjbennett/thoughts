---
title: Andrew's Tips & Tricks for Assignment 2 - Final Card-Down
layout: words
written: COMP1511 18s2
---

## Contents:

1. [Introduction](#introduction)
1. [Understanding The Assignment](#understanding-the-assignment)
  - [Game.c](#gamec)
  - [testGame.c](#testgamec)
  - [player.c](#playerc)
  - [What is the game runner?](#what-is-the-game-runner)
  - [How everything fits together](#how-everything-fits-together)
1. <b>[New things in this document!](#new-additions)</b>
  - [More on how to approach isValidMove](#more-on-how-to-approach-isvalidmove)
  - [Game vs game vs struct _game](#game-vs-game-vs-struct-_game)
  - [Enums, Cards, getNumOfSuit](#enums-cards-getnumofsuitcolorvalue)
  - [How does gameWinner work?](#how-does-gamewinner-work)
  - [How do we know when the game has
    ended?](#how-do-we-know-when-the-game-has-ended)
  - [What does "member access within misaligned address 0xbebebebe" mean?](#i-keep-getting-some-error-about-member-access-within-misaligned-address-0xbebebebe)
  - [How do I run the GameRunner again with the same seed?](#how-do-i-run-the-gamerunner-again-with-the-same-seed-how-can-i-make-it-so-that-my-player-doesnt-always-start-last)
  - [How do I make it so that my player isn't always the last one?](#how-do-i-run-the-gamerunner-again-with-the-same-seed-how-can-i-make-it-so-that-my-player-doesnt-always-start-last)
  - [Never #include a .c file!](#dont-include-a-c-file)
  - [Don't have the #ifndef #endif in your Game.c!](#dont-have-the-ifndef-endif-in-your-gamec)
1. [Using the Card ADT](#using-the-card-adt)
1. [Representing a deck of cards, discard pile, players' hands...](#representing-a-deck-of-cards-discard-pile-players-hands)
  - [Cards](#cards)
  - [Nodes](#linked-lists-nodes)
  - [A brief aside on linked lists](#a-brief-aside-on-linked-lists)
  - [Card Nodes](#card-nodes)
  - [What do the deck, discard pile, and players hands all have in common?](#what-do-the-deck-discard-pile-and-players-hands-all-have-in-common)
  - [An aside: helper functions are your friend!](#an-aside-helper-functions-are-your-friend)
  - [Node struct vs List struct](#node-struct-vs-list-struct)
1. [Game.h functions overview (part 1)](#gameh-functions-overview-part-1)
  - [The "big" functions](#the-big-functions)
  - [The "iterate through a list" functions](#the-iterate-through-a-list-functions)
  - [The "one-line" functions](#the-one-line-functions)
1. ["Store it in your game struct!"](#store-it-in-your-game-struct)
1. [Game.h functions overview (part 2)](#gameh-functions-overview-part-2)
  - [newGame](#newgame)
  - [isValidMove](#isvalidmove)
  - [playMove](#playmove)
  - [gameWinner](#gamewinner)
1.  [Debugging](#debugging)
  - [What's an `incomplete definition of type 'struct _game'`?](#whats-an-incomplete-definition-of-type-struct-_game)
  - [Debugging tips / strategies](#debugging-tips--strategies)
    - [You can never have too many printfs](#you-can-never-have-too-many-printfs)
1. [Helper functions: Linked Lists](#helper-functions-linked-lists)
    - [An aside: List Traversal](#an-aside-list-traversal)
    - [`addToFront`, `addToEnd`, `removeFromFront`](#addtofront-addtoend-removefromfront)
    - [`printList`](#printlist)  
1. [Helper functions: Cards](#helper-functions-cards)
    - [`printCard`](#printcard)
    - [`printDeck`](#printdeck)
1. [In summary](#in-summary)


<!--
-----


## Contents:
1. Using the Card ADT
1. Representing a deck of cards, discard pile, players' hands...
  - Cards
  - Nodes
  - A brief aside on linked lists
  - Card Nodes
  - Node struct vs List struct
  - What do the deck, discard pile, and players hands all have in
    common?
  - An aside: helper functions are your friend!
1. Game.h functions overview (part 1)
  - The "big" functions
  - The "iterate through a list" functions
  - The "one-line" functions
1. "Store it in your game struct!"
1. Game.h functions overview (part 2)
  - newGame
  - isValidMove
  - playMove
1. Debugging
  - `incomplete definition of type 'struct _game'`?
  - Debugging tips / strategies
    - You can never have too many printfs
  - Helper functions
    - printList
    - printCard
    - printDeck
1. Summary

-->

---

## Introduction

Hi there! I'm Andrew, one of the tutors for COMP1511.

If you're reading this, chances are you're feeling stuck with
the assignment. Maybe you're a bit confused about a few things, or maybe
you have no idea what to do or even where to begin.

I've written this (rather extensive) document for you, to (hopefully!)
help you get your head around exactly what you need to do there.

Before we get started, I have a question for you:

#### Have you started the assignment yet?

It's getting pretty late now, in terms of when the assignment is due. If
you haven't started yet, then you're really starting to run out of
time...

But don't panic -- it's not too late for you to get started. You just
need to make sure that you do get started **now**, and you keep on
steadily working on the assignment throughout the week.

It's not too late to start now _(but it will be too late to start at 10pm
on Thursday night, with mere hours until the deadline)_...

The point is: not all hope is lost, but you _will_ have to work hard
on the assignment over the next week.

#### Make the most of the resources available to you

If you're struggling with the assignment, don't forget about all of the
sources of help available to you:

##### Talk to your tutor

That's literally what they're there for. You should have your
tutor's email address from the email they sent in week 1 -- just
send them an email explaining your problem, and hopefully they can help
you out.

Of course, you can also get help during the lab this week.

##### Use the course forum

There have been a lot of questions (and answers!) posted on the course
forum. If you're confused about something, have a read through the
existing questions and answers, to see whether it's covered there. If you're still confused, try posting a question of your own.

##### Come along to the help sessions

There are help sessions on every day this week! The timetable is posted
on the course webpage.

##### Read through this document

I've tried to fill it with as many useful/helpful things as I possibly
can.

---
## Understanding the assignment

### Game.c

Ah, Game.c This is probably the biggest part of the assginment -- but
don't stress, this page has _heaps_ of information and tips to help you
out.

In Game.c, you need to implement the Game ADT.

The Game ADT is responsible for keeping track of what's going on in the
game.

Imagine you were playing a game of Final Card-Down in the lab with your
fellow COMP1511 students, using physical playing cards. Suddenly, the
fire alarm goes off, and you have to evacuate! In the precious few
seconds before you run screaming out of J17 *, what information do you
need to quickly write down so that you can pick back up from where you
left off once you're allowed back into the lab?

_* note: if the fire alarm ever *does* go off during a lab, please don't
waste time making notes on your Final Card-Down game, and *please* don't
run screaming out of the lab...._

---

There are (in a very broad sense) two parts to the Game ADT:

1. The game struct
1. The Game ADT interface functions

#### The game struct

The game struct is where all of the actual data/information about the
current state of the game is stored.

This includes things like: whose turn is it at the moment? What cards
are in the deck? In the discard pile? In each player's hand? etc etc.

#### The Game ADT interface functions

These functions (found in `Game.h`) are how the outside world interacts
with your Game ADT, and with the information stored inside your game
struct.

Note that **nobody outside of Game.c can see what's inside your game
struct!** The only way that they can interact with it is through calling
the interface functions in `Game.h`.

If you _do_ try to access the fields inside the game struct from outside
Game.c (e.g. from player. or testGame.c), you'll get a compile error
along the lines of `incomplete defintion of type 'struct _game'`. See
the section later in this document for more information on what that
means and how to avoid it.

Here are some extracts from an answer I wrote on the forum, which might
help to further explain what's required for Game.c:

> Are we supposed to implement our own player struct in the game struct?
> I heard mixed information with some saying that we are not supposed to
> implement our own game struct while other saying that we do.

You need to make your own game struct, which will hold all of the
information about the state of the game. This includes information about
what is in the players hands. You don't need to do this by making a
player struct which you store inside the game struct, but it's probably
a good way to do it.

This would look something like:

```c
struct player {
    // various things go in here
    // at the very least you'll need to keep track of the cards in the player's hands
    // you may or may not want more than just that
};
struct _game {
    // various things go in here
    // e.g. the deck, the discard pile, information about whose turn it is, etc...
    
    // make an array of player structs (or an array of player struct pointers,
    // to store info about each player
    struct player players[NUM_PLAYERS];
    // or `struct player *players[NUM_PLAYERS]` -- there are pros and cons to each approach
}
```

> I am a bit confused to how the moves are fed into and kept track the
> game.
>
> I understand that the function ActiveDrawTwos is returning the
> number of active 'drawTwos' where a active drawTwos must mean that the
> player has not entered the draw phase.
>
> This function will be diffcult
> to implement if we dont know what phase the player is in when the
> function is called.

The way that moves are "fed into" the game are through the playMove
function. The way that information is kept track of is up to you -- but
it will need to go into your game struct.


---

### testGame.c

In `testGame.c`, you're writing code to _test the Game ADT_.

In these tests, you need to check that the Game ADT does the right
thing.

Your tests should work with anybody's Game ADT implementation, not just
your own.

_(hint: this is easy, because the Game ADT is an ADT, and
you're always just using the interface function rather than looking
inside the struct, so it doesn't actually matter how you've implemented
it! Your tests will work with my Game.c, my tests will work with your
Game.c, etc.)_

If it helps, you can imagine that I've written my own implementation of
the Game ADT, but it has a few bugs in it (I'm not very good at
coding...).

You should make sure that you write an extensive set of
tests in your testGame, to make sure that you find all of the bugs in my
Game ADT implementation.

Rememver that anywhere that isn't Game.c can't see inside your game
struct, and so you can't access the fields of the game struct directly.
Instead, you have to use the Game ADT interface functions (aka the
functions in Game.h) to interact with the game.

We've provided some tests to get you started -- the stage 1 and stage 2
tests. These are on the assignment page -- make sure you have a look at
them!

They will both help you test the initial stages of your Game.c, as well
as helping you understand what tests would look like.

---

### player.c

In player.c, you're writing a player to _use the Game ADT_.

Your player's decideMove function will be called whenever it's your
player's turn to make a move.

Make sure you take a look at the drawBot lab activitiy from week 12 (and
especially drawBot.c, which is full of comments that explain what's
going on).

---

### What is the "Game Runner"?

The "game runner" is some code (written by us) which takes care of
actually making the game work overall.

See below for more details.

---

### How everything fits together

Again, extracted from a forum answer:

I think there's another answer here about the "game runner" (and
definitely look at the lecture videos from week 10, since Ashesh talks a
lot about the assignment), but if you get an idea of how the game runner
works it might help you understand how everything fits together.

The "game runner" is code written by us, which handles actually making a
game happen with AIs etc. (note that the game runner would have a main
function, whereas Game.c and player'c don't)

Your Game.c file (i.e. your implementation of the Game ADT) is used to
store information about the current state of the game -- [ imagine if
you were playing a game with friends using real cards, and there was say
a fire alarm and you had to leave the lab, what information would you
need to have written down so that you can go back to exactly where you
were at that point in the game once you're allowed to go back in?].

Your player.c is used as one of the players playing the game. The game
runner could run four copies of your player against itself, or your
player against three other people's players.

The process looks like this:

1. The game runner asks your ADT "hey I want to play a game, please set
   up the game using these values/colors/suits" (i.e. it calls your
newGame function).

2. Your ADT returns a pointer to your game struct (i.e. a 'Game') to the
   game runner

3. The game runner passes this point to your player.c's decideMove
   function, and asks it to return you the move it wants to make

3.5 The player calls whatever functions from the Game ADT it wants, in
order to get information about what move it should make. So it would
look at the cards in its hand, it would try calling isValidMove on
possible moves to see whether they're a valid move it could make, etc
etc, and then at the end it returns a playerMove struct with the move it
wants to make.

4. The game runner takes this playerMove struct, and passes it to your
   playMove function. Your playMove function needs to then update the
state of the game based on what's just happened (e.g. if your player
played a card, it needs to "remove" that card from their hand, and "add"
it to the discard pile).

It would be in the playMove function that your code updates various
things about the game -- e.g. if you had a variable storing the number
of DRAW_TWOs on top of the discard pile, and the player just played
another DRAW_TWO, you'd increase that counter, etc etc.

----

Hopefully that makes sense?

Basically, the Game ADT stores all of the information about the game. It
keeps track of whose turn it is, what's in the deck / discard pile,
what's in the player's hands, etc etc. It's your job to write that code.

The game runner will handle actually calling your Game ADT and passing
moves into it.

[incidentally, your testGame.c will also have a main function and will
also set up the arrays of suits/values/colors and call newGame etc --
check out the sample tests that are linked from the assignments section]

---

### Summary: understanding the assignment

The assignment has three components to it:

1. Game.c -- implementing the Game ADT
1. testGame.c -- testing the Game ADT
1. player.c -- using the Game ADT


---

## New additions

## More on how to approach isValidMove

See here <a href="http://www.cse.unsw.edu.au/~andrewb/thoughts/posts/isvalidmove.html">http://www.cse.unsw.edu.au/~andrewb/thoughts/posts/isvalidmove.html</a>

## Game vs game vs struct _game?

When we have types with a capital letter (e.g. 'Game'), we're talking
about a pointer to a struct, whereas lowercase names (e.g. 'struct
game') refer to the struct directly.

So, if you allocate memory for your game struct like this:

`Game new = calloc(1, sizeof(Game)); `

You're only allocating enough memory for a pointer to the game struct,
rather than for the whole game struct itself.

You can fix that error by making it be `sizeof(struct _game)` instead.

I'd recommend you make sure that all of the structs that you make have a
lowercase name (e.g. struct node) rather than uppercase (struct Node),
to avoid any confusion.

--------


## Enums; Cards; getNumOf[Suit/Color/Value]

A few very quick thoughts on the above:

1. You can't access the fields of the card struct directly. See the
   earlier section on the Card ADT for more.
1. You should refer to the enums with their names/contents directly, as
   in rather than `suit s = 0;`, you have to do `suit s = HEARTS;`
1. That said, you can treat the enums as number **only to sum values or
   use as an array index**.
    - This means that you can do `sum += cardValue(card)` when summing
      the values of a player's hand for playerPoints
    - This means that you can do `numberOfSuit[HEARTS]++` to increase
      the number of hearts in your array.
1. On that note: for `getNumOfSuit` etc, your life will be much easier
   if you store the counts in an array in your game struct, rather than
   trying to count/calculate the values each time `getNumOfSuit` etc is
   called.


-------

## How does gameWinner work?

First of all, make sure you've read this: <a
href="https://cgi.cse.unsw.edu.au/~andrewb/thoughts//posts/hints.html#store-it-in-your-game-struct">https://cgi.cse.unsw.edu.au/~andrewb/thoughts//posts/hints.html#store-it-in-your-game-struct</a>

--------

## How do we know when the game has ended?

Taken from <a
href="https://webcms3.cse.unsw.edu.au/COMP1511/18s2/forums/2711452">this
excellent question</a>:

> If the draw deck is empty and the discard pile only has one card in
> it, play is allowed to continue as the next play may be able to make a
> move (likely to be discarding a card).
> 
> This means that in between moves with an empty draw deck and a single
> card discard pile, the game hasn't necessarily ended.

Yup -- it's still perfectly fine for the game to continue and for
players to play cards, even if there's only 1 card in the discard pile
and 0 cards in the deck

> However, when a player want's to draw a card in this situation they
> will return DRAW_CARD from decideMove() as they are unable to
> determine the current situation. 
> 
> This will then be passed to playMove() which will recognise the game
> has ended but is not responsible for the ending of the game so it will
> not return NO_WINNER.

Yes. There's no way for playMove to "say" that the game has ended with
NO_WINNER, but that doesn't matter, because:

> Is it correct that gameWinner() will return NO_WINNER when it is next
> called and it will be called after every playMove()?

Yup. And this is how "we" know that the game has ended (by checking your
gameWinner function after every call to playMove)

--------

## I keep getting some error about "member access within misaligned address 0xbebebebe"?

"runtime error: member access within misaligned address 0xbebebebe for
type struct 'blah', which requires 4 byte alignment"

This means that you haven't initialised whatever the pointer is that
you're trying to look at.

Make sure you initialise every field in every struct that you malloc.

--------
## How do I run the GameRunner again with the same seed? How can I make it so that my player doesn't always start last?

```
./GameRunner --game-seed 12345 --deck-seed 67890
```

Other possible flags include --random-order to randomise which player
plays in which position, and --wait to pause between turns (which was
already there).


------

## Don't #include a .c file!

<big><big><big><big><big><big><strong>
Never #include a .c file!
</strong></big></big></big></big>
</big></big>

<big><big>
<big><big><big><big><strong>
Don't #include Card.c!
</strong></big></big></big></big>
</big></big>

<big><big>
<big><big><big><big><strong>
Don't #include Game.c!
</strong></big></big></big></big>
</big></big>

-----

## Don't have the #ifndef #endif in your Game.c!

It will break.

They should only ever be in a .h file.

---

## Using the Card ADT

Remember, the Card ADT is an ADT (Abstract Data Type). This means that
its _interface_ is separate from its _implementation_.

In practical terms, this means _you cannot see what's inside the card
struct_.

If you want to find out the color of a card, you _can't_ just say
`card->color`! You have to use the Card ADT functions: in this case
`cardColor(card)`.

Similarly, `cardSuit(card)` to get its suit, and `cardValue(card)` to
get its value.

Note that you _don't have to write these functions yourself_. The
implementation is given to you in `Card.c`.

#### Summary: Card ADT

1. Compile your code with 
<code>dcc -o test test.c Game.c <b>Card.c</b></code>
1. Use `cardColor(card)`, not `card->color`
1. Use `cardValue(card)`, not `card->value`
1. Use `cardSuit(card)`, not `card->suit`
1. Use `newCard(...)`, not `Card card = malloc(...)`
1. Use `destroyCard(card)`, not `free(card)`

---

## Representing a deck of cards, discard pile, players' hands...

### Cards

The cards in this assignment are represented by the Card ADT (see
above).

You don't need to worry about what's actually in the card struct -- the
only thing you'll be working with is a `Card` (aka `struct _card *` aka a
pointer to a card struct).

For example, to get a card to represent the "red one of hearts", you
would do the following:

```c
Card card = newCard(ONE, RED, HEARTS);
```

The variable "card" holds a pointer to a card struct, which you can pass
into the other Card ADT functions, e.g. `cardColor(card)` will return
RED.

---

Next, we need to find some way to store multiple cards together, so that
we can represent the players' hands, the deck, and the discard pile.

We can't just do this with `Card`s alone, though -- playing cards don't
have a `next` field, so we can't string them together into a list.

Instead, we need to "wrap" our `Card`s in something.

---


### Linked Lists: Nodes

Hopefully you're feeling somewhat comfortable with "normal" linked lists
by now.

The node struct that we all know and love looks something like:

```c
struct node {
    int data;
    struct node *next;
};
```

We can "link" a series of these structs together, to create a list:


---

### A brief aside on linked lists

See:
<a href="http://www.cse.unsw.edu.au/~andrewb/thoughts/posts/linkedlists.html#a-brief-aside-on-linked-lists">
here</a>.

---


### Card Nodes

For our purposes, we don't want to store a list of ints -- we want to
store a list of `Card`s.

Thankfully, this is very easy -- just swap out `int` for `Card`, and
voila we're done.

<div class="language-c highlighter-rouge">
<div class="highlight">
<pre class="highlight"><code><span class="k">struct</span> <span class="n">node</span> <span class="p">{</span>
    <strike><span class="kt">int</span> <span class="n">data</span><span class="p">;</span></strike>
    <span class="kt">Card</span> <span class="n">card</span><span class="p">;</span>
    <span class="k">struct</span> <span class="n">node</span> <span class="o">*</span><span class="n">next</span><span class="p">;</span>
<span class="p">};</span>
</code></pre></div></div>

<!--
<pre>struct node {
    <strike><span class="hljs-keyword">int</span> data;</strike>
    <span class="hljs-keyword">Card</span> card;
    <span class="hljs-keyword">struct</span> node *next;
};
</pre>
-->


---

### What do the deck, discard pile, and players hands all have in common?

If you think about it, the deck and the discard pile and the players
hands are all very similar -- they all need to hold a collection of
cards.

Maybe you could use the same data structure to represent each
of these  _(hint: you should do this)_.

When playing the game, what operations do you do with cards?

  - To draw a card, you....
     - remove a card from the top of the deck, and you add it to
       your hand
  - To play a card, you...
     - take a card from your hand, and add it to the top of the discard
       pile.
  - When creating the deck, you...
    - make a card, and add it to the deck
    - (where in the deck? the start? the end? the middle?)
  - When dealing the cards, you...
    - take the top card from the deck, and give it to the player (i.e.
      put it into their hand)

You might find it useful to write functions to do these operations (i.e.
add to the start of a list, add to the end of a list, remove from the
start of the list, ....).
_(hint: you should do this)_

This means that you'll only have to write the code once,
rather than writing separate code to interact with the deck vs the
discard pile vs the players' hands. 

---

### An aside: helper functions are your friend!

Let's look in more detail at dealing the cards to players at the start
of the game.

Imagine you were playing the game with your friends, using actual cards.

To deal the cards, you start by taking the top card from the deck, and
then give that card to the first player.

Next, you take the top card from the deck (which was previously the
second card, before you removed the original top card), and then give
that card to the second player.

Next, you take the top card from the deck (which was originally the
third card in the deck), and then give that to the third player.

Rinse and repeat until you've dealt 7 cards to each player.

If you think about how you would code this, it's very tempting to try to
write code to do all of those things at once.

However, that will lead to messy, confusing, hard-to-understand, and
most importantly _hard-to-debug_ code.

You will probably end up with a jumbled mess like this:

<div class="alert alert-danger">
<b>Do not copy this code!</b>
It is intentionally broken and horrible!
</div>

```c
// Note: do not copy this code! it is broken!
// Everything here is a bad idea! do not do it!
player->hand->top->next = deck->top->card;
deck->top->card->next = player->hand->top;
player->hand->top = player->hand->top->next;
deck->top->next->card = player1->hand->top->next;
player2->hand->top->next->next = deck->top->next->next;
```
Imagine, instead, how beautiful your code could be through proper use of
helper functions.

Dealing the cards could be as simple as this:

```c
struct node *top = removeTopCard(deck);
addToList(player, top);
// repeat until you've dealt all of the required cards
```

Beautiful!

And, for an added bonus: it's a _lot_ easier to correctly write code to
do just one simple thing -- e.g. remove a card from the start of the
list. _(hint: you've even done this already as a lab exercise!)_

You will find the assignment _significantly_ better if you write a
series of short, simple helper functions that each do one single thing.

You will find the assignment a _lot_ harder if you try to write all of
your code in one function, where all of your logic gets jumbled
together, and your code _doesn't work_ and you _don't know why_ and you
don't even know where to start with debugging.

I'll say it again: <b>it's _much_ easier to debug your code when it's all
written in small, short, simple functions that each do one thing and one
thing only.</b>

You can narrow your bugs down to which function they're in
-- _"oh, the problem is with how I insert nodes into the start of a
list"_; your `insertAtTheStartOfTheList` \* function is less than ten lines
long, and it's easy for you to work out which part is wrong.

Compare that to _"there's something wrong in my `newGame` function because
the deck isn't right when the function returns"_, where your `newGame`
function is 300 lines long. Trying to find the bug will _not_ be fun.


_\* this isn't a very good name for a function_

---


### Node struct vs List struct

You might find it useful to have a separate struct to represent a "pile" or "list of cards" overall.

Rather than just storing e.g. the deck in your game struct as `struct node *deck`, instead you would have a separate struct dedicated to representing a list of cards (whether it's the deck, discard pile, or a player's hand).

This struct would then store the pointer to the start of the list (aka the top of the pile), as well as any other information that would be useful to store.


----

#### How would a separate list struct work?

I'm so glad you asked.

Please make sure you read [this page](http://www.cse.unsw.edu.au/~andrewb/thoughts/posts/linkedlists.html#node-struct-vs-list-struct) first.

---

_(note: I am now assuming that you've read the page linked above)_

#### Why use a separate list struct?

It would be possible to just use the node struct to represent the discard pile / deck / etc, rather than also having a separate struct to represent the pile / list overall.

However, I think there are benefits to having a separate pile struct:

##### You can store extra information

If you just represent the e.g. discard pile as a `struct node
*discardPile` in your game struct, then you can't associate any other
information with it.

By "can't [store] other information", I mean you can't _(nicely)_ also
store how many cards are in the list _(which might come in handy)_, or
separate pointers to the start/end of the list _(which also might come
in handy)_.

Of course, you could just store each of those as separate fields in the
game struct:

```c
struct _game {
    struct node *discardPileTop;
    struct node *discardPileBottom;
    int discardPileSize;
    // ... 
}
```

But it's just messy, and it requires code duplication (effectively
copy/pasting those three struct fields for each of the lists of cards
you want).

So, if you want to store anything more than just a pointer to the start
of the list, it might be a good idea to create a separate list struct.

##### It makes helper functions easier

_(again: make sure you've read the link above!)_

Imagine that you were going to write a helper function to, say, insert a
node at the end of the list.

If you just have a "normal" linked list (i.e. just a node struct and no
separate list struct), inserting at the end would mean you have to loop
all the way to the end of the list each time just to find the last node.

```c
// NOTE: this function is not complete!
void insertAtEndOfList(struct node *head, struct node *toInsert) {
    // ...code goes here...

    // loop through the whole list to find the end
    struct node *curr = head;
    while (curr->next != NULL) {
        curr = curr->next;
    }
    // curr is now the last node in the list

    // add the new node to the end of the list:
    curr->next = toInsert;

    // ...more code goes here...
}
```

Whereas if you had a separate list struct, which contained both a pointer to the start of the list and a pointer to the end of the list, you could just jump "instantly" to the end of the list


```c
// remember, the list struct looks something like this:
struct list {
    struct node *start;
    struct node *end;
    // ....
};

// NOTE: this function is not complete!
void insertAtEndOfList(struct list *list, struct node *toInsert) {
    // ...code goes here...

    // we can find the end of the list instantly: it's just `list->end`

    // add the new node to the end of the list:
    list->end->next = toInsert;

    // ...more code goes here...
}
```

So, if you want to easily e.g. insert at the end of a list, you might
want to make a separate list struct that can store a pointer to the end
of the list.

-----

#### Summary: Representing Deck/Hands/etc

  - Store the deck, the discard pile, the players' hands, in linked
    lists
  - Write helper functions to e.g. add to the start of a list, add to
    the end of a list, remove from the start of a list, ...
  - Consider creating a separate "list struct" (or "deck struct" or
    "pile struct" or ...) to store additional useful information about the lists
  - **Keep your code simple!**
  - **Write helper functions!**


---

## Game.h functions overview (part 1)

You can categorise the Game.h functions into roughly three groups:

1. The "big" functions -- newGame, destroyGame, isValidMove, playMove
2. The "iterate through a list" functions -- handCard, getDeckCard,
   getDiscardPileCard, ...
3. The "one-line" functions -- everything else in Game.h

---
### The "big" functions

Don't be misled by the name -- these functions should still be kept
short (ideally 10-20 lines long at the most).

These functions are "big" in the sense that they have to actually "do
something", rather than just being a single line of code.

  - `newGame` needs to:
    - create your game struct
    - create the cards (from the arrays passed into it)
    - put those cards into the deck
    - deal the cards from the deck to the players
    - flip over the top card of the deck to make the discard pile
    - fill out any other starting values in your game struct
  - `destroyGame` needs to free _all_ of the memory that you've allocated
      - you'll need to loop through your lists to free every node
        individually (freeing the head of the list doesn't automatically
        free the rest of the values)
      - you need to call `destroyCard` for each of the cards you got
        from `newCard`
  - `isValidMove` needs to determine whether the proposed move would be
    valid at this point in the game
  - `playMove` needs to update the state of the game based on the
    player's move
    - e.g. if the move is `PLAY_CARD`, `playMove` needs to remove the
      card from the linked list for that player's hand, then add it to
      the linked list for the discard pile.
    - note that `playMove` should be the only Game ADT function (other
      than newGame) that changes anything in your game struct!
      - of course, you can (and should!) have helper functions of your
        own which will manipulate the data in your game struct

---

### The "iterate through a list" functions

These functions need you to find a Card at a certain index within one of
your linked lists (e.g. the deck, the discard pile, a player's hand,
...)

These should be fairly simple to write -- loop through the list until
you've found the node you're looking for.

If you look closely, you'll notice that all of these functions do
essentially the same thing, just with different lists (the deck vs the
discard pile vs a player's hand).

Maybe this would be a good place to... use a helper function? _(see
the notes on helper functions above)_

---

### The "one-line" functions

I'm calling these "one-line" functions, because they should only be
(approximately) one line long.

Let me repeat that: **the majority of your Game ADT functions should only
be one line long**.

(if you follow this, you will find the assignment a _lot_ easier, and
you'll need to write much less code!)

You might be wondering:
  - how can I possibly implement `getNumberOfTwoCardsAtTop` in one line?
    don't I have to loop through the discard pile to actually count how
    many `DRAW_TWO` cards are on top?
  - how can I possibly implement `playerPoints` in one line? don't I
    have to loop through the player's hand to actually add up the values
    of each card in their hand?
  - how can I possibly implement `[insert function here]` in one line?
    Don't I have to _[do some sort of calculation every time the function
    is called]_ to _[work out the answer]_?

The answer is simple... store it in your game struct!

---


## Store it in your game struct

Rather than re-calculating the answer every time one of your Game ADT
functions is called, you should instead store as much information in
your game struct as you can, and update those fields in your game struct
when relevant things happen in the game (aka in `playMove`).

To get some ideas on what to store, read through Game.h, and think
about what information you would need for each function.

For example:

  - `getNumberOfTwoCardsAtTop` needs to know how many DRAW_TWO cards are
    on top of the discard pile.
    - Maybe you could store a counter for how many DRAW_TWO cards are on
      top of the discard pile, and increase this counter whenever a
      DRAW_TWO is played...
  - `getNumOfSuit` needs to know how many cards of a given suit there
    when newGame was called.
    - Rather than trying to loop through the deck and discard pile and
      each player's hand.... 
    - ... maybe you could store in your game struct the count of how
      many of each suit (and color and value) newGame was given...
  - `gameWinner` should return the winner of the game, NOT_FINISHED if
    the game is still in progress, and NO_WINNER if the game ends
    without a winner.
    - Maybe you could store a field in your game struct to record who
      the winner is; when a player plays their final card (aka in
      the `playMove` function), update it to store whichever player won.

Again: _read through Game.h_, and for every single function, think about
what information you need to return. And then think about how you could
store that information in your game struct.

Doing this will make the assignment _significantly_ easier.

---

## Game.h functions overview (part 2)


### newGame

This is where it all starts.

A few hints / tips:

  - `newGame` should be the only place in your code where you create
    Cards / create nodes / allocate memory / etc.
    - (of course, by "`newGame`" I mean "helper functions that you
      call from `newGame`", remember, helper functions are your
      friend...)
  - Break your logic up into helper functions. Ideally, your newGame
    function should be no more than 10-20 lines _at most_.


### isValidMove

This is one of the "big" functions, but it doesn't need to be
overwhelming.

  - Again: break it up into helper functions!
  - Think carefully about which moves are valid when
    - i.e. when is it valid to draw a card? when is it valid to end your
      turn?
  - <b>Have a look at the diagram <a
    href="../static/valid_moves_fcd_18s2.png">here</a>.</b>
  - You might find it useful to keep track of what happened in the
    previous move(s) this turn (if anything), because this influences
    what is valid to do for the current move.

### playMove

Another "big" function, but just take it one step at a time.

  - Once again: break it up into helper functions!
    - What might be some sensible helper functions / sub-functions?
    - How about a different function to deal with PLAY_CARD vs DRAW_CARD
      vs END_TURN?
  - `playMove` should be the only function (other than newGame and your
    helper functions) that changes anything in the game struct.
  - As you make the move, update any relevant fields in your game
    struct.
    - (e.g. if you have a counter for how many DRAW_TWOs have been
      played, if the player has just played a DRAW_TWO card, you should
      increase that counter).


### gameWinner

Here's an excerpt from
[a forum post](https://webcms3.cse.unsw.edu.au/COMP1511/18s2/forums/2710865)
that might be useful:

> As you've suggested, gameWinner() should return NO_WINNER when all of
> the following are true:
> - The deck is empty AND
> - A player tries to draw a card AND
> - The discard pile has only one card.
> 
> So how would the gameWinner() function ever know that a player is
> trying to draw a card? My understanding is that gameWinner() doesn't
> have this information. So a correctly implemented gameWinner() will
> never return NO_WINNER. Is this correct?

To make gameWinner work for when there's NO_WINNER, you need to store
the relevant information in your game struct, and update it in playMove.

Then, the gameWinner function should check in the game struct to see
what the answer is.

I've written out an "example game" which might help explain:

#### Example Game

----

Turn #20, Move #0

Deck: [GREEN FIVE CLUBS]->[BLUE THREE SPADES]->[PURPLE C QUESTIONS]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 1

Player 1 makes the DRAW_CARD move.

Turn #20, Move #1

Deck: [BLUE THREE SPADES]->[PURPLE C QUESTIONS]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 1

Player 1 makes the END_TURN move.

-----

Turn #21, Move #0

Deck: [BLUE THREE SPADES]->[PURPLE C QUESTIONS]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 2

Player 2 makes the DRAW_CARD move.

Turn #21, Move #1

Deck: [PURPLE C QUESTIONS]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 2

Player 2 makes the END_TURN move.

----

Turn #22, Move #0

Deck: [PURPLE C QUESTIONS]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 3

Player 3 makes the DRAW_CARD move.

Turn #22, Move #1

Deck: [empty]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 3

Player 3 makes the END_TURN move.

----

Turn #23, Move #0

Deck: [empty]

Discard Pile: [RED THREE HEARTS]

Game winner: IN_PROGRESS

Current player: Player 0

Player 0 makes the DRAW_CARD move.

!!!!!!!!!!!!!!!!!!!!

Player has tried to draw a card, but the deck is empty,

and the discard pile contains only 1 card.

At this point, you need to update the game winner (stored in your game struct) to be NO_WINNER

!!!!!!!!!!!!!!!!!!!!

Turn #22, Move #1

Deck: [empty]

Discard Pile: [RED THREE HEARTS]

Game winner: NO_WINNER

Current player: Player 0

If you called the gameWinner function at this point in time (i.e. after Player 0 tried to make the DRAW_CARD move), it should return NO_WINNER


---

## Debugging

### What's an `incomplete definition of type 'struct _game'`?

The dreaded compiler error, lurking in the nightmares of every first
year student:

```
testGame.c:68:9: error: incomplete definition of type 'struct _game'
    game->next = NULL;
    ~~~~^
./Game.h:19:16: note: forward declaration of 'struct _game'
typedef struct _game *Game;
               ^
1 error generated.
```

This error means that you're trying to dereference a struct, but you
don't know what's inside that struct.

With an ADT, this is intentional -- if you're not inside Game.c (or
Card.c for the Card ADT, or ...), **you cannot dereference the game
struct**.

You _must_ use the Game ADT functions (i.e. the functions in Game.h)
instead.

To reiterate:

This is _wrong_:

<div class="alert alert-danger">
<b>Do not copy this code!</b>
It is intentionally incorrect!
</div>

```c
// (inside e.g. testGame.c or player.c)
// This is wrong! Don't do this!

// Check if the current player is ...
if (game->currentPlayer == ...) {
```

Do it this way instead:

<div class="alert alert-success">
This is the correct way to use the Game ADT
</div>


```c
// (inside e.g. testGame.c or player.c)
// This is the right way to do it!

// Check if the current player is ...
if (currentPlayer(game) == ...) {
```



---

## Debugging tips / strategies

### You can never have too many printfs

See [here](debugging_prints.html) for a worked example on debugging by
adding printfs to your code.

----

Debugging prints can be useful for code that's more complex /
"interesting" than the basic example above.

If you're not sure which part of your code is crashing: add some debug
prints at the start of each function saying that you've reached that
function.

It might also be useful to print out any relevant information about
what's going on, such as the function parameters.

e.g.

```c
int isValidMove(Game game, playerMove move) {

    printf("In isValidMove!\n");
    printf("move.action is: %d\n", move.action);

    // rest of code goes here

}
```

If you have a really long function and you're not sure what part is
going wrong, well, you should probably break your function up into
smaller sub-functions which each do one small/single/specific task, and
call those functions from that original function.

But, since you're probably going to ignore my (very helpful) advice on
writing short functions, and you just want to find the bugs in your
monstrous 400 line long function: add some debug prints throughout the
function, at any relevant places.

For example, you might want to add a print before and after something
important happens, or before/after you enter a loop, etc.

----

It might also be useful to print out information about the state of
certain variables in your code as a function progresses, e.g.

```c
Game newGame(int deckSize, ....) {

    doSomeStuff();

    doSomeMoreStuff();

    // create the deck
    createTheDeckSomehow();

    // GOOD PLACE TO PUT A DEBUG PRINT:
    // print out what's currently in the deck!

    // deal the cards to players
    dealTheCardsSomehow();

    // ANOTHER GOOD PLACE TO PUT A DEBUG PRINT:
    // print out what's in the deck now
    // print out what's in each player's hand
    // etc

}
```


### ... but don't leave them in when you submit!

You need to remove any debugging printfs before you submit your
code!

If you've written helper functions to debug you should leave those
in (but comment out where you call them), but don't leave lots of messy
printfs scattered throughout your code...

---


## Helper functions: Linked Lists

---

### An aside: List Traversal

_(see [the week 7 lecture slides](https://webcms3.cse.unsw.edu.au/COMP1511/18s2/resources/20824)
for more details, in particular the "list traversal" example on slide 12)_

One thing that you might notice as you become more experienced with
linked lists is that many of your helper functions are almost identical
to each other.

Most of the operations we do on linked lists are based around the idea
of "traversing" _(i.e. looping through)_ a linked list.

The `process_list` function below (from the lecture slides) is a good
example of what the structure of the majority of your linked list
functions will look like.

The key difference between your different linked list functions is what
they do with each node, i.e. how they _process_ the node.

When the example below  processes each node, it simply prints out that
node's value; if you wanted to e.g. count the number of nodes in the
list, you would replace the print statement with different code to
increment a counter.


```c
void process_list(struct node *head) {
    struct node *p = head;

    while (p != NULL) {
        // Process the node data here
        // For example, print out the node's data field.
        printf("    p->data = %d\n", p->data);

        p = p->next;
    }
}
```

---

### `addToFront`, `addToEnd`, `removeFromFront`

See the earlier section on "Representing a deck of cards, discard pile,
players' hands" for more details.

---

### `printList`

You will find it very useful to have a helper function that will print
out the contents of your linked lists.


Here is what a helper function for a "normal" linked list might look
like, based on the `process_list` example above:


<div class="alert alert-warning">
<b>This code WILL NOT WORK as-is for your assignment!</b><br>
You should modify it to print out the node's card field instead
<i>(hint: use the printCard function below)</i>
</div>



```c
void printList(struct node *head) {
    struct node *p = head;

    while (p != NULL) {

        // Process the node data here

        // Earlier, we had:
        // printf("    p->data = %d\n", p->data);

        // However, rather than printing the node's (int) data field,
        // we want to print out what the node's card is, instead.
        // 
        // You might want to make use of the "printCard" function below.

        p = p->next;
    }
}
```



---

## Helper functions: Cards


### `printCard`

You might find it useful to print out the contents of an individual
card.

To do this, you can copy the following two helper functions into your
Game.c (or testGame.c or player.c), _but you must write a comment saying
where you got them from!_

```c
// Don't forget to put prototypes for these functions at the start of
// the file that you use these in!

static void printCard(Card card);
static void printCardByComponents(value v, color c, suit s);


// Print out a card, from the provided Card.
// Calls the `printCardByComponents` function from the stage 1 tests.
// This function is copied from Andrew Bennett's assignment hints page.
static void printCard(Card card) {
    value v = cardValue(card);
    color c = cardColor(card);
    suit s = cardSuit(card);

    printCardByComponents(v, c, s);
    printf("\n");
}

// Print out a card from the provided components (value, color, suit).
// This function is copied from the stage 1 tests, written by Jacob.
static void printCardByComponents(value v, color c, suit s) {
    char *valueStrings[] = {
        "ZERO", "ONE", "DRAW_TWO", "THREE", "FOUR",
        "FIVE", "SIX", "SEVEN", "EIGHT", "NINE",
        "A", "B", "C", "D", "E", "F"
    };

    char *colorStrings[] = {
        "RED", "BLUE", "GREEN", "YELLOW", "PURPLE"
    };

    char *suitStrings[] = {
        "HEARTS", "DIAMONDS", "CLUBS", "SPADES", "QUESTIONS"
    };

    printf("%s %s of %s", colorStrings[c], valueStrings[v], suitStrings[s]);
}
```

---

### `printDeck`

You might also find it helpful for your debugging to print out the
contents of the starting deck that was passed into newGame.

To do this, you can copy the following helper function into your
Game.c (or testGame.c or player.c), _but you must write a comment saying
where you got them from!_


```c
// Don't forget to put prototypes for these functions at the start of
// the file that you use these in!

static void printDeck(int deckSize, value values[], color colors[], suit suits[]);
static void printCardByComponents(value v, color c, suit s);

// Print out the starting deck.
// Calls the `printCardByComponents` function from the stage 1 tests.
// This function is copied from Andrew Bennett's assignment hints page.
static void printDeck(
    int deckSize, value values[], color colors[], suit suits[]
) {
    for (int i = 0; i < deckSize; i++) {
        // prints the index of the card within the starting deck, to
        // make it easier to see what's going on.
        printf("[card %2d] ", i);
        printCardByComponents(values[i], colors[i], suits[i]);
        printf("\n");
    }
}

// Print out a card from the provided components (value, color, suit).
// This function is copied from the stage 1 tests, written by Jacob.
static void printCardByComponents(value v, color c, suit s) {
    char *valueStrings[] = {
        "ZERO", "ONE", "DRAW_TWO", "THREE", "FOUR",
        "FIVE", "SIX", "SEVEN", "EIGHT", "NINE",
        "A", "B", "C", "D", "E", "F"
    };

    char *colorStrings[] = {
        "RED", "BLUE", "GREEN", "YELLOW", "PURPLE"
    };

    char *suitStrings[] = {
        "HEARTS", "DIAMONDS", "CLUBS", "SPADES", "QUESTIONS"
    };

    printf("%s %s of %s", colorStrings[c], valueStrings[v], suitStrings[s]);
}

```

---



## In summary

Some parting thoughts:

1. **Helper functions are your friend.**
    - Break up every individual "step" of your code into a separate
      function.
    - Each of these functions should be no more than 10-20 lines at the most.
    - I cannot stress this enough. Doing this will make the assignment
      _significantly_ easier: easier to write, easier to get right,
      easier to debug.
1. **Store it in your game struct.**
    - The vast majority of your Game ADT functions should be one line
      long: they simply return a value directly from your game struct.
    - Update the information in these fields from `playMove`, as
      relevant things happen in the game.
1. **Take everything one step at a time.**
    - The assignment might seem overwhelming. There is quite a lot
      involved, but it's (hopefully!) not as scary as it looks.
    - Most of the Game ADT functions will be very very short, and very
      easy to write (`return game->currentPlayer`)
1. **Ask questions on the forum.**
    - If you don't understand something, _ask on the forum_.
    - Read through the existing answers -- a lot of useful content has
      been posted already.


---

## The end!

If you've made it this far, and read through everything -- nice work!

(I've written _quite a lot_ here, so there's a lot to go through and a
lot to think about).

If you have a moment, I'd appreciate it if you could fill out 
<a href="https://goo.gl/forms/OmxN8UqeAQeA2pAc2">this
(SUPER quick) feedback form</a>.

Good luck with the assignment!

\- Andrew

