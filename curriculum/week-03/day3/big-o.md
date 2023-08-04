# Big O

## Topics Covered / Goals

- Understand algorithmic efficiency
- Know how to read and write notation for runtime complexity
- Take some simple steps to avoid writing inefficient code

## Lesson

Big-O ("big O") notation is used in programming to describe the relative performance of a given algorithm. Note that it is not used to describe how much time an algorithm or program will take to run, but rather how it'll be affected by the change in input size. Programmers are generally  concerned with how much time an algorithm takes to run based on the input size, as well as how much memory it might require to utilize. However, time efficiency is usually what we are focused on improving.

When analyzing the time complexity of an algorithm, we measure how the total number of operations performed increases as the input size becomes very large.

- We're only interested in the worst-case scenario
- It is assumed that all operations take the same amount of time, so this is roughly equal to measuring time.
- only the largest term matters. 3N^2 + 4N + 1000 would just be N^2. As the input size gets really large, those smaller terms become irrelevant.

### Time Complexities

Here are some common complexities that you'll encounter:

| Notation | Complexity | Summary | Description |
| -------- | ---------- | ------- | ----------- |
| O(1) | Constant Time | Instant | The relative runtime does not change relative to the input size increasing |
| O(log N) | Logarithmic Time | Quick | The relative runtime increases slowly relative to the input size increasing |
| O(N) | Linear Time | Proportional | The relative runtime increases proportionally relative to the input size increasing |
| O(N log N) | Log-Linear Time | Slow | The relative runtime increases quickly relative to the input size increasing |
| O(N^2) | Quadratic Time | Very Slow | The relative runtime increases quadratically relative to the input size increasing |
| O(2^N) | Exponential Time | Ridiculously Slow | The relative runtime increases exponentially relative to the input size increasing |

Let's take a look at some simple examples below:

O(1): Constant Time

```python
small_list = [4, 9, 5, 12, 2] # 5 items
large_list = [6, 10, 9, 7, 1, 8, 17, 3, 13] # 10 items

def append_to_list(my_list, new_item):
    my_list.append(new_item)

append_to_list(small_list, 77) # just adds on to end, not impacted by size of list
append_to_list(large_list, 77) # just adds on to end, not impacted by size of list
```

O(log N): Logarithmic Time
[Binary Search](https://github.com/sierraplatoon/algo-binary-search)

O(N): Linear Time

```python
small_list = [4, 9, 5, 12, 2] # 5 items
large_list = [6, 10, 9, 7, 1, 8, 17, 3, 13] # 10 items

def find_item_greater_exists(my_list, value):
    for item in my_list: # ...factor of N
        if item > value:
            return True

    return False

find_item_greater_exists(small_list, 10) # may take up to 5 iterations
find_item_greater_exists(large_list, 10) # may take up to 10 iterations
```

O(N log N)
There aren't many simple algorithms that have O(N log N) complexity, but it's important to know that various sorting algorithms exist that can sort an array this fast. If you're working on an algorithm problem, and you're struggling to come up with a solution that is better than O(N^2), try sorting the array first!

O(N^2): Quadratic Time

```python
small_list = [4, 9, 5, 12, 2] # 5 items
large_list = [6, 10, 9, 7, 1, 8, 17, 3, 13] # 10 items

def find_double_exists(my_list):
    for x in my_list: # ...factor of N
        for y in my_list: # ...factor of N => N * N
            if x * 2 == y:
                return True

    return True

find_double_exists(small_list) # may take up to 25 (5*5) iterations
find_double_exists(large_list) # may take up to 100 (10*10) iterations
```

O(2^N): Exponential Time

```python
calls = 0
def fibonacci_recursive(N):
    global calls
    calls += 1

    if N == 0:
        return 0
    elif N == 1:
        return 1

    return fibonacci_recursive(N - 1) + fibonacci_recursive(N - 2)
    
# 0, 1, 1, 2, 3, 5, 8, 13 ...
f_index = 6
print("fib index:", f_index)
print("fib value:", fibonacci_recursive(f_index))
print("total calls:", calls, "\n") 

f_index = 12
print("fib index:", f_index)
print("fib value:", fibonacci_recursive(f_index))
print("total calls:", calls, "\n") # the number of calls grows very quick as the f_index gets larger
```

Here's a great site to reference (with a useful chart) for algorithmic complexities: <https://www.bigocheatsheet.com/>

### Making Improvements

Anything that is O(N^2) or worse, is considered a bad algorithm in terms of time complexity. Sometimes, there's nothing that we can do about this, because we must solve a problem based on what it requires. Oftentimes though, we can make improvements/optimizations, that might not change our overall complexity, but improve our performance:

```python
def find_double_exists(my_list):
    for x in my_list: # ...factor of N
        for y in my_list: # ...factor of N => N * N
            if x * 2 == y or x == y * 2: # an improvement, but this algo is still O(N^2)
                return True

    return True
```

Other times, we may be able to refactor our algorithm entirely to improve our time complexity:

```python
# a better (more efficient) fibonacci function (non-recursive) ... O(N)!
def fibonacci(N):
    f = 0
    a = 0
    b = 1

    for _ in range(N-1):
        f = a + b
        a = b
        b = f

    return f
```

Going from our naive recursive solution to an iterative one for Fibanacci, we've changed our time complexity from O(2^N) (i.e. "terrible") to be O(N)!

### More Examples

Can you analyze the code snippets below and determine what their relative time complexities are?

Example 1:

```python
def example_1(my_list):
    total = 0
    for x in my_list:
        value = x ** 2
        if value > 20:
            for y in my_list:
                if y > 0:
                    total += y + value
    return total
```

Example 2:

```python
def example_2(my_list):
    for item in my_list:
        counter = my_list.count(item)
        if counter > 2:
            return True

    return False
```

Example 3:

```python
def example_3(my_list):
    yummy_list = ["donut", "cake", "pie", "muffin"]

    food_list = []
    for item in my_list:
        if isinstance(item, str):
            food_list.append(item)

    yummy_count = 0
    for food in food_list:
        for yummy in yummy_list:
            if food == yummy:
                yummy_count += 1

    return yummy_count
```

## Resources

**Big O Notation**

- [Big O Slides](https://docs.google.com/presentation/d/1QtzsFoSPi4Eh4mJKlYk77jJZmHUcfMifWcAC-cyHIYg/edit?usp=sharing)

## Assignments

- [Big O Problems 1](https://github.com/sierraplatoon/algo-big-o-i)
- [Bubble Sort](https://github.com/sierraplatoon/algo-bubble-sort) in JS/python 
