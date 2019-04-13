---
title: Exam Dos and Don'ts
layout: words
written: COMP1511 18s2
---

Here is a collection of some of the common mistakes I've seen, and ways
to avoid them.


-----


<div class="alert alert-warning">
<i class="fas fa-question-circle"></i>
This code is correct in some circumstances and wrong in others, make sure you pay attention.
</div>



<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>


<div class="alert alert-success">
<i class="fas fa-thumbs-up"></i>
This code is correct.
</div>




## Linked Lists

### Stopping at the last node vs processing every node



If you want to process every node in a list:

<div class="alert alert-warning">
<i class="fas fa-question-circle"></i>
This code is correct in some circumstances and wrong in others, make sure you pay attention.
</div>


```c
struct node *curr = head;
while (curr != NULL) {
    // do something with the node
    curr = curr->next;
}
// at this point, curr is now NULL
```


If you want to stop at the last node (so that you can e.g. add something
to the end of the list):

<div class="alert alert-warning">
<i class="fas fa-question-circle"></i>
This code is correct in some circumstances and wrong in others, make sure you pay attention.
</div>


```c
struct node *curr = head;
while (curr->next != NULL) {
    // do something with the node
    curr = curr->next;
}
// at this point, curr is pointing to the last node in the list,
// but you haven't done anything with it yet
```

--------

### `curr` vs `head`

Make sure you use curr when you want to refer to the current node you're
processing, e.g.

<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>

```c
int sum = 0;
struct node *curr = head;
while (curr != NULL) {
    sum += head->data; // always adds the value of the first node!
    curr = curr->next;
}
```

<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>

```c
int sum = 0;
struct node *curr = head;
while (head != NULL) { // infinite loop!
    sum += curr->data;
    curr = curr->next;
}
```

<div class="alert alert-success">
<i class="fas fa-thumbs-up"></i>
This code is correct.
</div>

```c
int sum = 0;
struct node *curr = head;
while (curr != NULL) {
    sum += curr->data;
    curr = curr->next;
}
```



-----


### Moving nodes around -- change `next` pointers, not `data`!

<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>
```c
// Want to swap the nodes 4 and 5
// H         P    C   CN
// v         v    v    v
// 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> X

// This is not how to swap two nodes in a linked list
int tmp = curr->next->data;
curr->next->data = curr->data;
curr->data = tmp;
```

<div class="alert alert-success">
<i class="fas fa-thumbs-up"></i>
This code is correct.
</div>

```c
// H         P    C   CN
// v         v    v    v
// 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> X

// Assuming that curr and curr->next aren't NULL

// 3 now points to 5
prev->next = curr->next;

// 4 now points to 6
curr->next = curr->next->next;

// 5 points to 4
curr->next->next = curr;

```

### Don't change the list as you loop through it!


<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>

```c
while (curr != NULL) {
    curr->next = prev;
    prev = curr;
}
```


<div class="alert alert-success">
<i class="fas fa-thumbs-up"></i>
This code is correct.
</div>

```c
while (curr != NULL) {
    prev = curr;
    curr = curr->next;
}

```



## Comparing Strings

You can't compare strings with ==

<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>
```c
if (sightings[i].species == "Orca") {
```



<div class="alert alert-success">
<i class="fas fa-thumbs-up"></i>
This code is correct.
</div>

```
if (strcmp(sightings[i].species, "Orca") == 0) {
```


Be careful to use `strcmp` (<b>STR</b>ing <b>C</b>o<b>MP</b>are), not `strcpy`!
(<b>STR</b>ing <b>C</b>o<b>PY</b>)

<div class="alert alert-danger">
<i class="fas fa-thumbs-down"></i>
<b>This code is broken!</b>
</div>

```
if (strcpy(sightings[i].species, "Orca") == 0) {
```




<!--

## Calculating Averages

- comparing strings
- getting the last node from the list
-


  1: doesn't loop if not matching                                          
  1: early return 

 If they did something like: calculate the average dividing by size       
  rather than a counter -> 2/5                                             
                                                                           
  If they were totally perfect except for remembering to set the           
  pointers -> P/5                                                          
                                                                           
                                                                           
  if they strcmp a single char with the customer name -> 1                 
                                                                           
                                                                           
  basically comes down to: knowledge/understanding problem vs slight       
  algorithm problem                                                        

  Q1: changing data etc -> 0                                               
                                                                           
                                                                           
  just going off end -> 2                                                  
  just not checking null at end -> 2                                       
                                                                           
  both -> 1?                                                               
                                                                           
                                                                           
                                                                           
  Q2: not handling e.g. zero case -> 4/5                                   
                                                                           
  cases correct, but math wrong: -> 2.5/5 ? M                              
                                                                           
  dividing by size, not count -> 2 

  STRCPY not STRCMP OMG -> S                                               
  >= 0 not > 0 -> 4.5                                                      


-->
