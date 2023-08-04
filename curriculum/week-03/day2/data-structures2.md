# The Call Stack and Recursion


## Topics Covered / Goals
- Understand what the call stack is and how it operates
- Understand the difference between a recursive function and an iterative function
- Implement recursive solutions to challenges

## Lesson

### The Call Stack
- [The Call Stack](https://docs.google.com/presentation/d/123b2TsW6k0OfvBOH4fmVkKUq7xT3WZ1SEWEUBpfnrqc/edit?usp=sharing)
- A stack is a type of data structure, similar to a Python list. The difference is, a stack follows a Last In First Out (LIFO) model.
  - Think of a deck of playing cards. The first card you're able to pick up is the last card added to the top of the deck.
- The Call Stack is what keeps track of functions that are executing. When a function is invoked, it's added to the top of the stack, and removed once it's resolved.
- If another function is beneath the latest one on the Call Stack, it cannot resolve until the top function has finished resolving
```python
def first():
    print('(PUSH) first function executing...')
    second()
    print('(POP)  first function resolving...')
    return

def second():
    print('(PUSH) second function executing...')
    third()
    print('(POP)  second function resolving...')
    return

def third():
    print('(PUSH) third function executing...')
    print('Hello World!')
    print('(POP)  third function resolving...')
    return

first()
```

- The output of this will be...
```
(PUSH) first function executing...
(PUSH) second function executing...
(PUSH) third function executing...
Hello World!
(POP)  third function resolving...
(POP)  second function resolving...
(POP)  first function resolving...    
```
...because the first function can't resolve fully until the second function is done executing, and the second function can't fully resolve until the third function is done executing.
A good way to visualize the call stack is by using the debugger. If we step through the above program using our debugger, we can watch the individual function calls as they get pushed and popped from the stack.

What happens if an error occurs while our program is running? If you look in your terminal, you should see the call stack. It will tell you not only which function threw the error from which file, but also what function was calling that function, and so on. This is essential information for debugging!

### Recursion

- Recursion is what happens when a function invokes itself. Like an iterative loop, it'll keep invoking itself until a certain condition is met
  - The finishing condition for a recursive solution is called a "base case", a condition where the function no longer returns itself. Without a base case, a recursive function will run until the Call Stack is completely full. In other words, a Stack Overflow.
- Let's consider a simple linear search function. An iterative solution would use a for loop, like this:
```python
def linear_search_iterative(my_list, search_item):
    for i in range(len(my_list)_:
        if my_list[i] == search_item:
            return True

    return False
```
- A recursive solution would need one or more base cases. One of them is obvious: we find the item we're looking for.
  - The other one has to be something that one of our recursions will eventually hit. Perhaps this?
```python
def linear_search_recursive(my_list, search_item):
    if len(my_list) == 0:
        return False

    last_item = my_list.pop()
    if last_item == search_item:
        return True
    else:
        return linear_search_recursive(my_list)

```
- In this solution, `.pop()` removes the last item from the array and returns it. If the last item is a match for our `search_item`, then we can return true. If not, we invoke the function again with the shortened array. Our second base case comes when we empty the array entirely. If the array is empty, the `search_item` is not present at all, so we return false.
- Any problem that can be solved with a loop can also be solved with recursion (and vice versa):
```python
## ------ITERATION
def space_x_countdown_iterative(num):
    for i in range(num, 0, -1):
        print(i)
    
    return console.log("LIFTOFF!")

## ------RECURSION
def space_x_countdown_recursive(num):
    # BASE CASE
    if num == 0:
        return console.log("LIFTOFF!")

    print(num)
    
    # RECURSIVE CASE
    return space_x_countdown_recursive(num-1)
```

Sometimes, it might be hard to know what the base case should be for a recursive algorithm, but your intuition tells you that your algorithm shouldn't need to recurse many times, and you DEFINITELY don't want an infinite loop. In these cases, it can be useful to define a `max_depth` parameter for your recursive algorithm.

```python
def limited_recursion(calls, max_depth):
    if calls < max_depth:
        # do useful stuff here
        print('again!')
        limited_recursion(calls + 1, max_depth)
    }
}

limited_recursion(0, 10)
```

## External Resources
- [Python Tutor](http://www.pythontutor.com/visualize.html#mode=edit)
  - This tool breaks down code step by step, allowing you to see the Call Stack in real-time.

## Assignments
- [School Interface III](https://github.com/sierraplatoon/oop-school-interface-iii)
- [Recursion](https://github.com/sierraplatoon/algo-recursion)
- [Binary Search](https://github.com/sierraplatoon/algo-binary-search) in JS/Python

