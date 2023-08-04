# Intro to Python

​
## Topics Covered / Goals
- Understand basic Python syntax
- Understand variable types
- Understand basic data types
- Understand data structures:
  - lists
  - tuples
  - dictionaries
- Understand control flow in Python:
  - if-statements
  - for-loops
  - while-loops
- Understand some common built-in methods
    - built-in methods for strings, lists, dicts, etc
    - destructive vs non-destructive methods

## Lesson

> In python, like all programming languages, we will manipulate various different types of data, such as numbers and lists. If you want to understand how you can manipulate your data, and compare it to other data, the most important thing to understand is the TYPE of your data. Many of these data types are similar to the data types we use in javascript, but there are some important differences I'll point out. There are two types of types: primitive types, which are just a single value, and non-primitive types, which can contain other data.

### **Primitive Types**
### Numbers

> The first type we'll learn about are numbers. Let's create a number literal and assign it to a variable.

```python
a_small_number = 4
```

> In python, unlike javascript, you don't use any keywords (e.g. `let`, `const`) for defining a variable. You simply assign a value to a variable name. When choosing a name, it's conventional in python to use 'snake_case', which consists of lowercase letters connected with underscores.
> To check the type of a variable, we can use the `type()` function. We can print the result to the terminal with `print()`. We'll learn more about functions later today.

```python
print(type(a_small_number))
```

> You can manipulate numbers in python using numeric operators, such as +, -, *, /, %, <, >, ==, etc. These work the same way they do in javascript, so we won't review them again. Python does not perform type coercion like javascript, so there are no operators like === or !==.

### Strings

> Strings can be created in python similarly to how you create them in javascript, using either single or double quotes.

```python
a_string = 'hello world'
another_string = "welcome to the internet"
print(type(another_string))
```

> Python supports string interpolation, just like javascript, but the syntax is a little different

```python
day = 'Friday'
activity = 'bowling'
print(f"Let's go {activity} on {day} afternoon.") # This is sometimes called an "f string"
```

### Booleans

> The last primitive type in python is boolean. There are only two boolean values, `True` and `False`. These work the same as in javascript, except that we spell them with capital letters. 

### **Non-Primitive Types**
### Lists

> The most fundamental non-primitive type in python is a list. A python list is roughly equivalent to a javascript array, but lists in python are a bit more flexible. 

```python
berries = ['grape', 'tomato', 'cucumber', 'eggplant', 'banana', 'watermelon', 'pumpkin']
print(type(berries))
print(berries[1]) # normal indexing works how you'd expect
print(berries[-1]) # index from the back of the list
print(berries[2:4]) # slice in the middle
print(berries[:3]) # slice from the start to index 3
print(berries[2:]) # slice from index 2 to the end
```

### Tuples

> A tuple in python is like a list, but it is defined with parentheses instead of brackets. It is used for storing mulTiPLE pieces of data in one data structure. Unlike a list, a tuple is immutable, meaning that it cannot be mutated. You cannot add, remove, or modify items in a tuple. You should use a tuple if you want to make sure that the items in your list never change. For example, you might use a tuple to store the seven days of the week, because there's no reason for that to change. You might also store geographical (lat,long) coordinates in a tuple, because the two numbers together refer to one thing, one specific place. It doesn't make sense to modify just one number in a coordinate pair. Instead, you would just create a new coordinate pair. 

```python
days_of_the_week = ('sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday')
days_of_the_week[6] = 'Caturday' # this throws an error! we can't assign to a tuple
```

### Dictionaries

> A dictionary in python is like an object in javascript (though python objects are more complex than javascript objects). It's used to store pairs of data, a key with its value. For example, you might use a dictionary to store information about a person. Unlike in javascript, we must use quotation marks for defining string keys, and we must use bracket notation for accessing values in a dictionary. 

```python
jeff = {
    'name': 'jeff',
    'age': 44,
    'job': 'influencer',
}
print(jeff['age'])

people = [
    {
        'name': 'alice',
        'age': 44,
        'job': 'influencer',
    },
    {
        'name': 'bob',
        'age': 31,
        'job': 'dog walker',
    },
    {
        'name': 'carol',
        'age': 65,
        'job': 'life coach',
    },
]
print(people[1]['name'])
```

### functions

> To define a function in python, we use the `def` keyword. The body of the function must be indented because python is whitespace-sensitive. Instead of using braces and semicolons to mark the boundaries of our code, python uses indentation and newlines. It's important that you get comfortable defining your own functions to use in your code, but you should also be aware that there are many useful functions predefined in python.

```python
def gimme_five():
    return 5
print(gimme_five() + 10)


# if a function is defined with one parameter, it must be called with one argument
def add_one(n):
    return n + 1
print(add_one(10))

# order matters for positional arguments
def describe_berries(n, color):
    print(f'I have {n} {color}berries.')
describe_berries(3, 'blue')
describe_berries('black', 4)

# keyword arguments can be used in any order
def describe_berries(n=1, color="blue"):
    print(f'I have {n} {color}berries.')
describe_berries(color="rasp", n=3)

# use * to define a function with an unspecified number of parameters
def print_them_all(*args):
    print(args)
print_them_all('alice', 'bob', 'carol')

# use ** to define a function with an unspecified number of keyword arguments
def who_am_i(**kwargs):
    for kwarg in kwargs: # we'll talk more about loops in a minute
        print(f'My {kwarg} is {kwargs[kwarg]}.')
who_am_i(name='dan', age=20, job='skydiving instructor')
```


### If-statements
> If-statements in python work the same as in javascript, except that instead of `&&`, `||`, and `!`, we use `and`, `or`, and `not`.

```python
def can_drink_beer(age, country):
    if age >= 21 or age >= 18 and country == 'Canada':
        return True
    elif country == 'Antarctica':
        return True
    else:
        return False

print(can_drink_beer(20, 'USA'))
print(can_drink_beer(21, 'USA'))
print(can_drink_beer(18, 'Canada'))


```

**for-loop**
```python
## looping over list elements
my_list = ["a", "b", "c"]
for x in my_list:
    print(x)

## loop over a range
for x in range(10):
    print(x)


for i, x in enumerate(my_list):
    print(i, x)

## looping over dictionary entries
my_dict = {
    "donuts": "yummy!",
    "green beans": "icky!",
}
for k in my_dict:
    print(my_dict[k])

for v in my_dict.values():
    print(v) # same output as the previous loop
```

**while-loop**
```python
x = 9
while x > 0:
    print(x)
    x = x - 1

## infinite loop
x = 9
while x > 0:
    print("la la la") 
```


**Built-in Methods**
> Most types of values in python have built-in methods that you can use to manipulate that value. a method is simply a function that belongs to an object. There are two types of methods that you should be aware of: destructive and non-destructive methods. 
> Destructive methods are those that change the original data where non-destructive methods are those that do not change the original data. You have to be careful with the methods that you use as there isn't a clear indication in Python or JS as to which are destructive and which aren't. Let's take a look at the example below:
```python
first_name = 'jonathan'
first_name.capitalize() # this is a string method that converts the first character to upper case
first_name # we see that despite running the method above, my original data does not change

last_name = 'young'
last_name.replace('g', '123')
last_name # same case here - this is a non destructive method

staff = ['jon', 'rod', 'ankur', 'chad', 'alicia']
staff.append('tom')
staff # this is a destructive method because it alters the original
## Let's fire tom. He sucks.
staff.pop()
staff # my original has been changed yet again!
```

## Resources
- https://www.pythoncheatsheet.org/cheatsheet/basics
## Assignments
- [99 Bottles](https://github.com/sierraplatoon/algo-99-Bottles) in Python
- [Deaf Grandma](https://github.com/sierraplatoon/algo-deaf-grandma) in Python
- [Roman Numerals](https://github.com/sierraplatoon/algo-roman-numerals) in Python
- [Fibonacci](https://github.com/sierraplatoon/algo-fibonacci) in Python
- [Factorial](https://github.com/sierraplatoon/algo-factorial) in Python
- [Linear Search](https://github.com/sierraplatoon/algo-linear-search) in Python


