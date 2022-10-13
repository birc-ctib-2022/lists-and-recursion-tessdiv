# Lists and recursion

Python's lists, that we can index into in constant time, but where it takes linear time to add or remove in the middle of a list, is a data structure known as a *dynamic array* or a *dynamic list*. There are other kinds of lists, and one of them, the *linked lists* is used in many other programming languages, especially functional languages.[^1]

With the kind of linked lists I have in mind you can do one of two things: you can get the value at the front of a list, or you can get a reference to the rest of the list.

I have defined such a list for you:

```python
@dataclass
class L(Generic[T]):
    """
    A single link in a linked list.

    The `head` attribute gives you the value at the head of
    this list while `tail` gives you the rest of the list,
    or None if the rest is the empty list.

    >>> L(1, L(2, L(3, None)))
    L(1, L(2, L(3, None)))
    """

    head: T
    tail: List[T]

    def __repr__(self) -> str:
        """Representation of this object."""
        return f"L({self.head}, {self.tail})"


# A list is either an L(head, tail) or None
List = Optional[L[T]]
```

The details of this definition is not something you need to understand right now, but it defines a list over some generic type `T` (you can use any type you want, like `int`, of `str` or whatever). If you have a list `x`, then `x.head` gives you the value at the front of the list, similar to `x[0]` for a Python list, and `x.tail` gives you the rest of the list, like `x[1:]` except that `x.tail` takes constant time while `x[1:]` takes linear time.

You can create a list of the numbers 1, 2, and 3 with

```python
x = L(1, L(2, L(3, None)))
```

and as you can see we use recursion here as part of defining the data structure. Such data structures are called *recursive*, when an object contains the same kind of objects, here a list containing another list in `tail`. If you have a list `x`, then you can get the head as `x.head` and the tail as `x.tail`.

To show you how we can write recursive functions on this kind of lists, here are two functions that compute the length of a list and the sum of its elements when they are integers:

```python
def length(x: List[T]) -> int:
    return 0 if x is None else 1 + length(x.tail)


def add(x: List[int]) -> int:
    return 0 if x is None else x.head + add(x.tail)
```


## List exercises

* Write a function, `contains`, that tells us whether an element is in a list. What is the runtime complexity?
* Write a function, `drop`, that removes the first `k` elements in a list. What is the runtime complexity?
* Write a function, `keep`, that returns a list of the first `k` elements in the input. What is the runtime complexity?
* Write a function, `append`, that appends an element to a list. Do not modify the original list but return a new list that consists of the original elements and then the new element. What is the runtime complexity?
* Write a function, `concat`, that concatenates two lists. Do not modify the original lists but return a new list that consists of the original elements from the two lists. What is the runtime complexity?
* Write a function, `rev`, that reverses a list. Do not modify the original list but return a new list that consists of the original elements in reverse order. What is the runtime complexity?

## Tail-recursion

Although Python doesn't implement tail-call optimisation, it is important to know how to translate recursive functions into tail-recursive functions since these can often be replaced with a loop. A quite common case is that you have a recursive data structure, like these lists but we will see more later in the class, where recursive functions are the natural approach to implementing operations on them. Then, to make the operations more efficient, you turn those into tail-recursive operations and then into functions that use loop. You might be able to go there directly in some cases, but in my experience it is easier to first think in terms of recursion and then loops later.

To practise tail-recursion, redo all the functions we have seen so far. Some of them might already be tail-recursive, but that just means that you are quickly done.

To get you started, here are the length and summation functions in tail-recursive form:

```python
def length_tr(x: List[T], acc: int = 0) -> int:
    return acc if x is None else length_tr(x.tail, acc + 1)

def add_tr(x: List[int], acc: int = 0) -> int:
    return acc if x is None else add_tr(x.tail, acc + x.head)
```

In the template functions I have not included the accumulator. You have to decide if a function needs one, and in case it does, what that accumulator must look like.

* Write a function, `contains_tr`, that tells us whether an element is in a list. What is the runtime complexity?
* Write a function, `drop_tr`, that removes the first `k` elements in a list. What is the runtime complexity?
* Write a function, `keep_tr`, that returns a list of the first `k` elements in the input. What is the runtime complexity?
* Write a function, `append_tr`, that appends an element to a list. Do not modify the original list but return a new list that consists of the original elements and then the new element. What is the runtime complexity?
* Write a function, `concat_tr`, that concatenates two lists. Do not modify the original lists but return a new list that consists of the original elements from the two lists. What is the runtime complexity?
* Write a function, `rev_tr`, that reverses a list. Do not modify the original list but return a new list that consists of the original elements in reverse order. What is the runtime complexity?

## Loops

Since Python doesn't implement tail-call optimisation, we are still using recursion when we have tail-recursion, and this is sometimes a problem. It will never be a problem for small lists and such, but if you are dealing with large data, e.g., data structures that represent entire genomes, then recursive solutions will exceed the available stack space and your program will crash. Then you need an iterative solution, i.e., you need to use some kind of loop instead of function calls.

Lucklily, if you can translate a recursion into tail-recursion, it is usually straightforward to take the final step and get a looping version. (If you cannot make a tail-recursive function then there are other techniques, but I will have to teach you those at a later point, perhaps a later class such as GSA).

Here are the `length` and `add` functions in a loop version:

```python
def length_loop(x: List[T]) -> int:
    acc = 0
    while x:
        acc += 1
        x = x.tail
    return acc

def add_loop(x: List[int]) -> int:
    acc = 0
    while x:
        acc += x.head
        x = x.tail
    return acc
```

Now you can implement the remaining functions:

* Write a function, `contains_loop`, that tells us whether an element is in a list. What is the runtime complexity?
* Write a function, `drop_loop`, that removes the first `k` elements in a list. What is the runtime complexity?
* Write a function, `keep_loop`, that returns a list of the first `k` elements in the input. What is the runtime complexity?
* Write a function, `append_loop`, that appends an element to a list. Do not modify the original list but return a new list that consists of the original elements and then the new element. What is the runtime complexity?
* Write a function, `concat_loop`, that concatenates two lists. Do not modify the original lists but return a new list that consists of the original elements from the two lists. What is the runtime complexity?
* Write a function, `rev_loop`, that reverses a list. Do not modify the original list but return a new list that consists of the original elements in reverse order. What is the runtime complexity?


[^1]: In the R programming language, that some of you might be familiar with, this kind of lists used to the main structure, way back in the elder days. The current lists are similar to Python's, but there are still some remnants of linked lists in R, used to handle function arguments. Languages that still use them are those where side-effects are not allowed, such as Haskell or SML, where linked lists give us an efficient way to still work with lists and to build persistent data structures. You will learn more about them later in the class.
