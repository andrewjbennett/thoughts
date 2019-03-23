---
title: Uninitialised Variables and dcc
layout: words
written: COMP1511 19T1
---

> For some reason, when I run my program, the output is
```
12 24 -1094795586 48 48
```
> rather than
```
12 24 36 48 48
```
>
> What's going on?


When you see output that involves strange numbers that you weren't
expecting (especially large negative numbers like the -1094795586 you
have there), this usually means that you have uninitialised variables
somewhere in your code.

For a start, you should make sure that you're compiling with dcc so
that you get better information about this, but it's worth noting that
"normal" dcc won't usually warn you about uninitialised variables.

There's a second mode for dcc that you can enable which will warn you
about uninitialised variables, but you can only enable one of these at
once.

To compile and run your code with dcc normally, you'd do something like
this:

```
$ dcc -o vector_permutation vector_permutation.c
$ ./vector_permutation
```

To enable the other mode that dcc has, which will check for
uninitialised variables, you can add __"--valgrind"__ to the compile
line, e.g.:

```
$ dcc -o vector_permutation vector_permutation.c --valgrind
$ ./vector_permutation
```

Now when you run your program, it will stop if it detects any
uninitialised variables being used, and it will tell you what's going
on.

---

For example, let's say I have this code:

```c
int main(void) {
    int array[5];
    array[0] = 40;
    array[1] = 42;
    // note: the code doesn't set a value in array[2]
    array[3] = 46;
    array[4] = 48;

    int i = 0;
    while (i < 5) {
        printf("array[%d] is: %d\n", i, array[i]);
        i++;
    }

    return 0;
}
```

Compiling it and running it with "normal" dcc gives me this:

```
$ dcc -o test_normal test.c
$ ./test
array[0] is: 40
array[1] is: 42
array[2] is: -1094795586
array[3] is: 46
array[4] is: 48
```
Compiling it and running it with "dcc --valgrind" gives me this:

```
$ dcc -o test_valgrind test.c --valgrind
$ ./test_valgrind
array[0] is: 40
array[1] is: 42

Runtime error: uninitialized variable accessed.

Execution stopped here in main() in test.c at line 13:

    int i = 0;
    while (i < 5) {
-->     printf("array[%d] is: %d\n", i, array[i]);
        i++;
    }

Values when execution stopped:

array = {40, 42, 0, 46, 48}
i = 2
array[i] = 0
```

So, as you can see, "normal" dcc won't warn you about uninitialised
variables, but if you compile it with "dcc --valgrind", it will.

This is why the autotests / automarker compiles and runs your code twice
(if you have a loop at the very start of the output, you can see it
compiles twice, once with "normal" dcc and once with "dcc --valgrind")
