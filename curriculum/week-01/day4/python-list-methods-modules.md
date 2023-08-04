# Python List Methods and Modules

## Topics Covered / Goals
- Understand switch cases
- Understand lambda functions
- Understand list methods map(), filter(), sort(), reduce()
- Understand Try-Except error handling

## Lesson

### Switch Cases
- An alternative way to writing many `elif` statements if you wanted to execute multiple conditionals is using the `match` and `case` keywords
```py
lang = input("What's the programming language you want to learn? ")

match lang:
    case "JavaScript":
        print("You can become a web developer.")

    case "Python":
        print("You can become a Data Scientist")

    case "Java":
        print("You can become a mobile app developer")
    
    case "PHP":
        print("You can become a backend developer")
    
    case "Solidity":
        print("You can become a Blockchain developer")    
    
    case _:
        print("The language doesn't matter, what matters is solving problems.")
```

### Lambda functions
- temporary, unnamed ("anonymous") functions
```python
## typical function example
def add_one(x):
    return x + 1

print(add_one(7)) # output would be 8

## lambda function example
add_two = lambda x : x + 2

print(add_two(7)) # output would be 9
```

### List Methods
**map()**
- creates a new list, using a function that returns a new item
```python
my_list = [1,2,3,4,5,6,7,8,9,10]
new_list = map(lambda item : item + 2, my_list)
for x in new_list:
    print(x)

print(list(new_list)) # need to cast as a list
## [3,4,5,6,7,8,9,10,11,12]
```

**filter()**
- creates a new list, using a function that returns a bool (True => include item in new list)
```python
my_list = [1,2,3,4,5,6,7,8,9,10]
new_list = filter(lambda item : item % 3 == 0, my_list)
for x in new_list:
    print(x)

print(list(new_list)) # need to cast as a list
## [3,6,9]
```

**reduce()**
- creates a single value, using a function that aggregates values
```python
import functools
my_list = [1,2,3,4,5,6,7,8,9,10]
sum = functools.reduce(lambda agg, item : agg + item, my_list)
print(sum)
```

**sort()**
```python
people = [
    {
        'name': 'alice',
        'age': 44,
        'job': 'influencer',
    },
    {
        'name': 'bob',
        'age': 49,
        'job': 'dog walker',
    },
    {
        'name': 'carol',
        'age': 35,
        'job': 'life coach',
    },
]
# This sorts the original list
people.sort(key=lambda k : k['age'])

# key is a 1-argument function that describes how to sort the list.
# This returns a new list (original list is not modified)
people_sorted = sorted(people, key=lambda k : k['age'],reverse=True) 
```
### Dictionary - zip
**zip**
```py

numbers = [1, 2, 3]
letters = ['a', 'b', 'c']
zipped = zip(numbers, letters)
print(zipped)  # Holds an iterator object
#<zip object at 0x7fa4831153c8>
print(type(zipped))
#<class 'zip'>
print(list(zipped))
[(1, 'a'), (2, 'b'), (3, 'c')]
```
**Create dictionary from 2 lists**
```py 
stocks = ['reliance', 'infosys', 'tcs']
prices = [2175, 1127, 2750]
dictionary = dict(zip(stocks, prices))
print(dictionary)
# output {'reliance': 2175, 'infosys': 1127, 'tcs': 2750}
```


### Try-Except
> When building an application, your code doesn't always run the way you expect it to, and sometimes it throws an error! We can use try/except to write code that we think might throw an error, and then prepare an alternate plan in case the code fails. 
```python
try:
    a = 1
    b = 2
    c = "donuts"
    
    d = a + b
    print(d)

    e = a + c # error
    print(e)
except:
    # handle exception
    print("BOO! there was an error")
else:
    # handle success
    print("YAY! there was no error")
finally:
    # always execute regardless of exception or success
    print("donuts are yummy!")
```

## Modules, Packages, Libraries, and Frameworks

- Modules, libraries, frameworks, and packages are just code that someone else wrote ahead of time to make your life as a developer easier. There is no magic here - it's just meant to make your life easier and for you to make more robust applications
- There are a number of package managers to manage the different libraries we will use. The most popular package manager for Python is Pip, and the most popular (and default) package manager for Javascript is npm. 
- You can find some very useful libraries [here](https://pythontips.com/2013/07/30/20-python-libraries-you-cant-live-without/). We'll be using Django in the next few weeks. If you go the data science route, there are some data science heavy libraries like NumPy and Pandas

You can also write your own modules, which allows for you as the author to organize your code and group it together for ease of use. To put it very simply, a module is a file of Python code that you bring into other files. Let's take a look at an example:

### Python modules
- Modules are simply files with the “. py” extension containing Python code that can be imported inside another Python Program.

```python
## file1.py
def say_hello():
    print('Hey there')
def say_goodbye():
    print('Bye bye')

## file2.py
import file1
import file1 as anything
from file1 import say_goodbye

anything.say_hello()
say_goodbye()
```
We just created two files: `file1.py` and `file2.py`. `file1.py` has a `say_hello` and a `say_goodbye` function. `file2.py` has nothing in it, but imports that file in as the name of the file and we can use all the methods in that file. We can also rewrite it as `import file1 as anything` and call `anything.say_hello()`. You can `import` anything into your file by providing the correct relative path to the file. More on that can be found under external resources.

## Resources
- [JS array methods](https://www.w3schools.com/jsref/jsref_obj_array.asp)
- [JS array method lambda-like examples](https://medium.com/@mandeepkaur1/a-list-of-javascript-array-methods-145d09dd19a0)
- [Python vs. JavaScript](https://realpython.com/python-vs-javascript/#javascript-vs-python)
- [Python Modules](https://www.tutorialspoint.com/python/python_modules.htm)
- [Python/JS import syntax comparison](https://www.saltycrane.com/blog/2015/12/modules-and-import-es6-python-developers/)

## Assignments
- [Armstrong Numbers](https://github.com/sierraplatoon/algo-armstrong-numbers) in JS/Python
- [Anagrams I](https://github.com/sierraplatoon/algo-anagrams-i) in JS/Python
- [Sum Pairs](https://github.com/sierraplatoon/algo-sum-pairs) in JS/Python
- [Credit Check](https://github.com/sierraplatoon/algo-credit-check) in JS/Python
- [Debug Deaf Grandma](https://github.com/sierraplatoon/debug-deaf-grandma) in JS


