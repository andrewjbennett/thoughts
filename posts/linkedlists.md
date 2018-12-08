---
title: Linked Lists
layout: words
written: COMP1511 18s2
---


### Overview

1. <a href="aside">A brief aside on linked lists</a>
1. <a href="traversal">List traversal</a>
1. <a href="list-struct">Node struct vs List struct</a>


## A brief aside on linked lists
<a href="#overview">(back to top)</a>


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

#### Creating a list

First, let's allocate memory for the head of the list, then assign it a
value.
```c
struct node *head = malloc(sizeof(struct node));
head->value = 3;
head->next = NULL;
```

```
head
 v
(3) -> X
```

Next, let's make a `curr` pointer, so that we can move through our list.
```c
struct node *curr = head;
```

```
head
 v
(3) -> X
 ^
curr
```

Now, let's allocate memory for a second node...


```c
curr->next = malloc(sizeof(struct node));
```

```
head
 v
(3) -> (??) -> ??
 ^
curr
```

And let's move curr along, so that it's pointing to our new node...
```c
curr = curr->next;
```


```
head
 v
(3) -> (??) -> ??
        ^
       curr
```

Next, let's put a value into that node:
```c
curr->value = 4;
```


```
head
 v
(3) -> (4) -> ??
        ^
       curr
```


And let's allocate memory for a third node, hanging off the second one:

```c
curr->next = malloc(sizeof(struct node));
```

```
head
 v
(3) -> (4) -> (??) -> ??
        ^
       curr
```

We can repeat this a few times, to create the rest of our list:

```c
curr = curr->next;
```

```
head
 v
(3) -> (4) -> (??) -> ??
               ^
              curr
```

```c
curr->value = 5;
```


```
head
 v
(3) -> (4) -> (5) -> ??
               ^
              curr
```



```c
curr->next = malloc(sizeof(struct node));
```

```
head
 v
(3) -> (4) -> (5) -> (??) -> ??
               ^
              curr
```


```c
curr = curr->next;
```

```
head
 v
(3) -> (4) -> (5) -> (??) -> ??
                      ^
                     curr
```

```c
curr->value = 6;
```


```
head
 v
(3) -> (4) -> (5) -> (6) -> ??
                      ^
                     curr
```

Let's end our list there:

```c
curr->next = NULL;
```


```
head
 v
(3) -> (4) -> (5) -> (6) -> X
                      ^
                     curr
```

----

## List Traversal

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


## Node struct vs List struct

It would be possible to just use the node struct to represent the discard pile / deck / etc, rather than also having a separate pile struct.


However, I think there are benefits to having a separate pile struct:

1. If you just represent the e.g. discard pile as a `struct node *discardPile` in your game struct, then you can't associate any other information with it -- in the sense that you can't (nicely) also store how many cards are in the list (which might come in handy), or separate pointers to the start/end of the list (which also might come in handy).

I mean, you could just store each of those as separate fields in the game struct, 

```c
struct _game {
    struct node *discardPileTop;
    struct node *discardPileBottom;
    int discardPileSize;
    // ... 
}
```

But it's just messy, and it requires code duplication (effectively copy/pasting those three struct fields for each of the lists of cards you want)

2. It's a common way to store/represent linked lists --

----

The only way you've seen linked lists this semester (COMP1511 18s2)  is
where the "list" is just a pointer to the first node.

But another common approach is having a separate "list struct", which
just holds a pointer to the first node in the list.

This might seem silly/wasteful, but it has advantages: one of the really
nice things is that it makes it a lot easier to work with functions that
might change the start of the list.

Using a separate list struct, your e.g. listAppend function never has to
return anything, because if you change where the first node in the list
is, you just change where the pointer in the list struct points to,
rather than having to return the new head if the head changes, and have
the caller be responsible for updating it.


### "Normal" linked lists (as per COMP1511 18s2):

#### Structs

```c
struct node {
    int data;
    struct node *next;
};
```

#### Interacting with the list
Using some example list functions:

```c
struct node *head = newNode(5);

// `head` is now a linked list with a single node, (5) -> X

// add 10 to the front of the list
// we have to say `head = addToFront(..)`, because the head of the list changes
head = addToFront(head, 10);

// add a node to the end, surely we won't change the front?
// ... except what if it's an empty list? so this also has to return the head
head = addToEnd(head, 2);

// what about deleting from a list? well, we might delete the first
// element, so we'd better return the head in case it changes
head = deleteMatching(head, 5);

// .. etc
```
#### Sample function implementation

A function might look like:

```c
struct node *deleteMatching(struct node *head, int value) {

    if (head->data == value) {
        struct node *tmp = head;
        head = head->next; // head has changed, so need to return it
        free(tmp);
    }

    struct node *curr = head;

    // other code goes here

    return head;
}
```

### Using a Separate List Struct

#### Structs

```c
struct node {
    int data;
    struct node *next;
};

struct list {
    struct node *head;
    // could also have fields for size, tail of list, ...
    // whatever else might be useful
};
```

#### Interacting with the list
Using some example list functions:

```c
struct list *list = newList(); // just allocs memory
list->head = newNode(5);

// `list` is now a linked list with a single node, (5) -> X

// add 10 to the front of the list
// even though the head of the list changes, because it's all
// encapsulated in a list struct, we don't need to return the new head
addToFront(list, 10);

// similarly, adding to the end, deleting a matching node, ...
addToEnd(list, 2);
deleteMatching(head, 5);

// .. etc
```

#### Sample function implementation

A function might look like:

```c
void deleteMatching(struct list *list, int value) {

    if (list->head->data == value) {
        struct node *tmp = list->head;
        list->head = list->head->next;
        // we've changed `list->head`, but that's fine because
        // we haven't changed where `list` points to, so we don't need to
        // return anything to the main function
        free(tmp);
    }

    struct node *curr = list->head;

    // other code goes here

    return head;
}
```

I personally prefer working with lists that have a separate list struct,
since it makes using/calling helper functions much easier, but everyone
has their own preferences.

