---
title: Undefined Behaviour, or; what happens when you forget to return from your function?
layout: words
written: COMP1511 19T1
---

Suppose you're trying to experiment with recursive functions.

Factorial seems like a great function to try to implement.

In case you've forgotten what a factorial is, `n!` is defined as `n *
n-1 * n-2 * n-3 * ... * 2 * 1`.

So, for example, `5!` would be `5 * 4 * 3 * 2 * 1`.

It has a pretty straight forward recursive definition:  
`factorial(n) = n * factorial(n-1)`

.. and a clear base case -- we stop calculating the factorial once we
get to 1 (we don't keep multiplying by 0 then -1 then -2 etc).


---

So, you're writing your factorial function, and you end up with this:


```c
int factorial(int num) {
    int fact = num;

    if (fact == 1) {
        return fact;
    }
    fact = fact * factorial(fact - 1);
}
```

You compile and run your code at home:
```
$ gcc -o factorial factorial.c
$ ./factorial
Enter a number: 3
3! = 6
```

You then decide to try your code on CSE, where you compile it with dcc:
```
$ dcc -o factorial factorial.c
$ ./factorial
Enter a number: 3

factorial.c:16:17: runtime error: signed integer overflow: 3 * -1094795586 cannot be represented in type 'int'

Execution stopped here in factorial(num=3) in factorial.c at line 16:

        return fact;
    }
--> fact = fact * factorial(fact - 1);

Values when execution stopped:

fact = 3
```

.... what? What's going on? Why did the code work in gcc, but not dcc?

You, my friend, have just stumbled across an _undefined behaviour_ in C.

---

The reason that you're seeing the different behaviours when compiling it
with dcc (it crashes) vs gcc (it doesn't crash) isn't because it "works"
with gcc -- the function is <small>(probably)</small> doing the exact
same thing when it's compiled with gcc or dcc.

However, the function is doing something that we call **"undefined**
**behaviour"** -- something that's not defined according to the rules of C.

When your program tries to do something that's undefined, the compiler /
runtime environment is allowed to do whatever it wants to do, there's no
consistent decision for what it should do in that situation.

This is problematic, because it means that our programs won't always
work in the same way -- your C program might behave in a certain way on
your computer, and running the same program on my computer might behave
in an entirely different way. 

---

A few examples of undefined behaviour that you might have seen thus far
in the course:

__Uninitialised Variables__

__Q:__ What value does a variable have, if you don't give it a value when you
create it?

```
int i;
printf("i is: %d\n", i); // what will this print out?
```

__A:__ Who knows? It could be anything. It could be 0, it could be whatever
value happened to be in that part of memory from earlier, it could be
the number "42".

The C specification doesn't define what has to go in a variable if it's
uninitialised, and so it's entirely up to the compiler to decide what it
wants to do.


__Array overflows__

__Q:__ What happens if you try to read or write past the end of an array?

```
int array[10] = {3, 14, 15, 9, 2, 6, 5, 35, 8, 9};
array[11] = 99;
```

`array` might look something like this:

```
 0   1   2   3   4   5   6   7   8   9
[ 3][14][15][ 9][ 2][ 6][ 5][35][ 8][ 9] ?? ?? ?? ?? ?? ?? ?? ??
```

and if you tried to write to `array[11]`...
```
 0   1   2   3   4   5   6   7   8   9
[ 3][14][15][ 9][ 2][ 6][ 5][35][ 8][ 9] ?? 99 ?? ?? ?? ?? ?? ??
                                            ^^
```

__A:__ Who knows? It could be anything. It could overwrite something
else in memory (and this is the most common behaviour), but there's no
rule saying that it has to do that. Again, it's up to the compiler to
decide what to do.

---

In this case, the error message that dcc gives us when we run the
program is:

> runtime error: signed integer overflow: 3 * -1097795586
cannot be represented in type 'int'

What this error means is that the program is trying to store the results
of some calculation in an int variable, but the number that it has
calculated is too big to fit in an int, which we would commonly refer to
as an "integer overflow".

And, as you might have guessed, an integer overflow is yet another
undefined behaviour in C.


**Q:** If the number is too big to fit in an integer, then how can it
store it in one?

**A:** it can't, and so the program can just store whatever value it
wants into that integer.

Usually, in the case of an integer overflow or underflow, the number
will just "wrap around" -- i.e. `2147483647 + 1 = -2147483648`, but
there's no reason that it _has_ to do this -- it would be perfectly
valid for the compiler to just decide to store the number "123456"
instead if an integer overflow ever happened.

---

Because the results of undefined behaviour are unpredictable, it's
important that we never do anything that's undefined in our C programs.

However, actually checking that when a program runs, it never _ever_ does
anything that's undefined, is ... a lot of work.

You would need to do a _lot_ of extra checks, for example:

  - Checking that every time you access an array, you're accessing
    within the bounds of an array.

  - Checking that every time you do any sort of calculation and try to
    store the results in an int, the resulting value will fit in an int.

  - Checking <small>(somehow)[^1]</small> that every time you access a
    variable, it has already been initialised previously.

... and more.

As you can imagine, these extra checks add what we'd call "overhead" to
the program -- because it has to do lots of extra checks, the program
would take longer to run compared to the same program without any of
those safety checks.

This sort of overhead doesn't really matter when it comes to the
programs that you write in this course, but 
imagine making something like Google Chrome running 30% slower --  it's
not going to be a very popular decision.

So, the majority of compilers don't do any of these checks, and will
quite happily let your program write past the end of an array and will
let it trample all over the other variables in your program.

However, when you're just learning to program, it's very important that
you do actually notice if your program tries to do something
defined -- because it may very well be that nothing actually does go
wrong in your program, _this time_, and so you won't realise that you've
done anything wrong.

But imagine if you made the same mistake again, only this time you're
now working for Google, and now your code that accidentally reads off
the end of an array means that somebody could take advantage of that to
steal passwords from the browser's memory. [^2]


This is why in COMP1511 we use a special compiler called dcc, which adds
in all of these extra checks to your program when it runs, and stops
your program if it detects any sort of undefined behaviour.

(it also has some other cool features to help make it easier for you to
see what's gone wrong when your program crashes, which you'll no doubt
become _very_ familiar with as we progress through the course).

---

So, the problem that dcc has identified is that the function is trying
to calculate `3 * -1094795586`, but where did that -1094795586 come from?

The answer to that is that your program is doing something else that's
undefined:
__it's missing a return statement at the end of the function__.

So, if `num == 1` it will return 1, but if num is anything other than
1... it will do the calculation, but then never actually return the
result of that calculation.

So, when your program uses the result of one of those function calls,
the value that it gets from the returned function is unpredictable and
could be __anything__ -- and happened to be  -1094795586 in this case.

---


However, this hasn't _entirely_ answered the question of how it works
in `gcc` vs `dcc`.

Often, code compiled with `gcc` and code compiled with `dcc` work in the
exact same way, with the only difference being that  `gcc` doesn't stop
and yell at you if you do something like write off the end of an array,
whereas `dcc` does.

But, in this case, because not returning from a function is undefined
behaviour, it's up to the compiler to do whatever it wants.

Of course, this means that the compiler is allowed to do the "right"
thing (or rather, what you were expecting it to do), and it looks like
that's what gcc did in this instance -- it effectively added a `return
fact;` to the end of your function.

However, `dcc` seems to have done something else -- the line of code
that it's stopped on was `fact = fact * factorial(num - 1)`, and the
error it's given us is that it's trying to calculate
` 3 * -1094795586` -- this means that for some reason, factorial(2)
returned `-1094795586`.


So: let's dive into how each compiler chose to interpret that undefined
behaviour. Hopefully we'll find why dcc has returned that garbage value
from `factorial(2)`.

---

Just for fun, I decided I'd try compiling that factorial function in
many different ways, and compare them to see what the differences are.

Some background, for a start:

__Compilers__

Out in the "real world", there are two common compilers that are used
for compiling C programs: "clang" and "gcc".

The dcc compiler that we use is a wrapper around the clang compiler,
with some extra stuff added in to make it easier for beginner
programmers to work out what's going on in their code.

__Optimisation__

When you compile your code, you can also specify whether the compiler
should "optimise" your code, and if so how much it should try to
optimise it.

Optimising your code means that it will take shortcuts where it can, to
try to make your code run faster. This will usually lead to compiled
code that "looks different" to your original code -- rather than a more
direct translation of lines of code you've written into the
corresponding assembly code, the compiler squishes things together where
it can. [^3]


You can explicitly specify how mush the compiler should try to optimise
your code using the `-O` flag, followed by a number.

So `-O0` (o zero) means "don't optimise at all", all the way through to
`-O3` which means "optimise as much as possible". In general, I think
people don't usually go further than `-O2` ordinarily [^4].

---

I compiled the code with all three compilers, and all four
optimisation levels, and generated 12 separate compiled binaries.

I then ran these through a disassembler tool[^5], which can
<small>(sort of)</small> convert machine code back to C code.

I've cleaned the generated C code up a bit (e.g. fixed the variable
names, since the assembly code has no idea what your variables were
originally called), and added some comments to explain what's going on.

So, without further ado:

#### Original

The original code, for comparison:

```c
int factorial(int num) {
    int fact = num;

    if (fact == 1) {
        return fact;
    }
    fact = fact * factorial(fact - 1);
}
```

#### Clang

<b> `clang -O0` </b>

```c
// clang -O0
ulong factorial(uint num) {
  uint fact;
  uint tmp;

  fact = num;
  if (num != 1) {
    factorial(num - 1);

    // note: tmp is uninitialised!
    fact = tmp;
  }
  tmp = fact;
  return tmp;
}
```

(note: `uint` means "unsigned int", i.e. an integer value which can
never be negative. `ulong` means "unsigned long", which is another
sort of integer value which can never be negative, except it might be
able to store bigger numbers than an int)

As you can see, it's used an extra temporary variable for some reason
_(although this might be an artefact of how the assembly code was
interpreted as C code, as it might have been a temporary variable stored
in the CPU rather than in memory)_.

It _calls_ the factorial function correctly, but doesn't actually store
its return value anywhere. It also overwrites the `fact` variable with
an uninitialised variable, which means that in the case where `num`
isn't 1, it will return a garbage value.


<b> `clang -O1, -O2 and -O3` </b>

All three levels of optimisation gave the same resulting code:

```c
ulong factorial(uint num) {
  return num;
}
```

This beautiful function will just return the input that it was given.

After all, if num == 1, the correct answer to return is 1.

And if num is anything other than 1, our code didn't specify what value
it should return, and so the compiler could pick anything it wanted. In
this case, it chose to return `num`, because then it doesn't have to
consider whether num is 1 or not.


#### gcc


<b> `gcc -O0` </b>

```c
ulong factorial(uint num) {
  int tmp;

  if (num != 1) {
    tmp = factorial(num - 1);
    num = num * tmp;
  }
  return num;
}
```

A pretty sensible approach -- and it gives the right answer.

This is why you saw the answer you were expecting when you compiled your
code with gcc.


<b> `gcc -O1` </b>
```c
int factorial(int num) {
  int tmp;

  if (num == 1) {
    return 1;
  }
  tmp = factorial(num - 1);
  return tmp;
}
```

Here gcc has optimised out the part where it multiplied the result of
the recursive function call with the number that was passed in -- after
all, it can decide to do whatever it wants for the return value, and
this way it's saved having to do a multiplication.


<b> `gcc -O2 and -O3` </b>

```
int factorial(void) {
  return 1;
}
```

Beautiful. Short and sweet.

And, technically correct -- the only defined behaviour of the function
was that it had to return 1 if `num` was 1. It was allowed to do
whatever it wanted for any other values of num, and so it just decided
to always return 1.


#### dcc

<b> `dcc -O0` </b>

```c
int factorial(int num) {
    // Currently these variables are all uninitialised
    int tmp1;
    uint tmp2;
    int status;
    int fact;


    if (num == 1) {
        status = 1;
        fact = num;
    } else {

        // Check whether `num - 1` would give an integer underflow
        // If it does, then return that we had an underflow.
        // Note: you're not expected to understand what this does.

        if (((would_underflow(num,1) ^ 0xffU) & 1) == 0) {
            __asan_handle_no_return();
            __ubsan_handle_sub_overflow_abort(&PTR_s_factorial.c_00408150,num,1);
        }

        // Calculate factorial(num - 1), store it in an unsigned integer
        // (i.e. an integer that can't be negative)
        tmp2 = factorial(num + -1);

        // Calculate num * (the existing result for factorial(num - 1))
        // and check whether it would have overflowed.
        // If it would havve, then return that we had an overflow.
        // Again, you're not expected to understand what this does.

        tmp1 = (num * tmp2) >> 0x20;
        if (tmp1 != 0 && tmp1 != -1) {
            __asan_handle_no_return();
            __ubsan_handle_mul_overflow_abort(&PTR_s_factorial.c_00408170,num,tmp2);
        }

        // Set the status to 0
        // (this is just an arbitrary flag)
        status = 0;
    }

    // Note that status can only be 0 or 1, so this is always true.
    if (status == 0 || status == 1) {

        // Note that if num wasn't 1, fact is still uninitialised...
        return fact;
    }

    // This code will never actually happen...?
    __asan_init();
    __asan_exp_load4();
    __asan_register_globals(__unnamed_3,1);
    return status + -1;
}
```

You can see all of the extra checks that dcc has added in -- every time
you do a mathematical operation <small>(on a number that it doesn't know
for sure can't overflow/underflow)</small>, it adds checks to see
whether it could underflow or overflow.

<b> `dcc -O1, -O2 and -O3` </b>

```c
int factorial(int num) {
    int tmp1;
    uint tmp2;

    if (num != 1) {
        // Check whether subtracting 1 from num would cause an underflow
        if (would_underflow(num, 1)) {
            __ubsan_handle_sub_overflow(&PTR_s_factorial.c_00727b70,num,1);
        }

        // Calculate the factorial
        tmp2 = factorial(num + -1);

        // Store it in a temporary variable, and check if it would
        // overflow when multiplied with num
        tmp1 = (num * tmp2) >> 0x20);
        if (tmp1 != 0 && tmp1 != -1) {
            __ubsan_handle_mul_overflow
                (&PTR_s_factorial.c_00727b90,num,tmp2,
                 num * tmp2 & 0xffffffff);
        }
    }

    // Note that we haven't changed num at any point, so this will
    // always just return whatever weas passed in, albiet with some
    // extra checks for whether the result would have under/overflowed.

    return num;
}
```


-----

So, there you have it. When you compiled with `gcc`, the way it chose to
implement the undefined behaviour was by doing what you had expected it
to do -- return `fact` at the end of the function.

When you compiled it with `dcc`, it still did the factorial calculation,
but threw away the return value it got from the recursive function call,
and decided to return an uninitialised value instead.

Does this mean that gcc is better than dcc?

Of course not -- for one, this is just a single tiny unrealistic demo
program, and is by no means representative of how the compiler behaves
overall.

The actions that the respective compilers have chosen to do in
response to the undefined behaviour might make more sense in other
situations.

And, either way, you shouldn't ever be trying to rely on the compiler to
"do the right thing" when it comes to undefined behaviour -- after all,
the entire point of undefined behaviour is that it's undefined, and so
you can't assume that what you want to happen is what will actually
happen.


---


And, if you've made it all the way to the end:

Hopefully you've enjoyed reading this -- it turned out a bit longer than
I was expecting, but hopefully you've gotten something out of reading
it.




---

Footnotes:


[^1]: Checking whether variables have always been initialised before
    they're used is a very tricky task, and requires a _lot_ of extra
    work -- you need to somehow keep track of every variable that gets
    made, and what value(s) are put into it when.

    The way that we do this in COMP1511 is by compiling your programs
    with `dcc --valgrind`, which enables a different mode of `dcc` which
    checks for uninitialised variables (but is now unable to detect all
    of the other errors that dcc normally detects).

    However, valgrind is REALLY SLOW -- as an example, running the coco
    referee 10 times with dcc --valgrind takes over 2 minutes. Running
    it 10 times with normal dcc takes about 15 seconds.

    You might have noticed that the autotests and automarking always
    compile your code twice at the start -- once with normal dcc, and
    once with `dcc --valgrind`. Each autotest runs twice, once on the
    normal-dcc-compiled program, and once on the valgrind-dcc-compiled
    program.

[^2]: Of course, companies like Google have very high standards for code
    to make it into their codebase in the first place -- this usually
    means that mutliple people will have read your code before it's
    accepted.

    But, humans aren't perfect, and it's entirely possible that somehow
    you did end up making some sort of dangerous mistake in your code
    that led to a major security vulnerability. 

    So, it's much much _much_ better that you learn to never write code
    with those sorts of mistakes.

[^3]: Incidentally, the compiler is _much_ better at optimising code
    than you are, so you should try to write code that's as clear as
    possible, and let the compiler take care of doing any super fiddly
    things to try to make it as fast as possible.

    Of course, you'll learn in future computing courses (like
    COMP2521) about ways to make your code more _efficient_ -- things
    like choosing what algorithm to use when trying to solve a problem.

    But, for now, in COMP1511, just focus on writing code that's clear
    and correct, and you'll learn more about the tradeoffs involved in
    making your code go faster in future courses.

[^4]: See: [https://stackoverflow.com/questions/11546075/is-optimisation-level-o3-dangerous-in-g](https://stackoverflow.com/questions/11546075/is-optimisation-level-o3-dangerous-in-g).

[^5]: I used [ghidra](https://ghidra-sre.org/), a suite of reverse
    engineering tools recently released by the NSA. It's free and open
    source, and very powerful.

