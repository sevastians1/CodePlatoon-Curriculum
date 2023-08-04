# Decorators, Static Methods vs Instance Methods, Getters and Setters

## Topics Covered / Goals
- **Intermediate Object Oriented Programming**
	- Much of the value of OOP comes from calling instance methods on an object, which can refer to that object's specific data using `self` or `this`. However, there are other aspects of OOP that can be useful as well, such as, class methods, getters and setters. 
- **Decorators in Python**
	- Decorators are functions in Python that are used to modify how another function behaves. They aren't inherently related to OOP, but we'll start seeing them as we dive deeper into Python, so now is a good time to learn about them. 
- **Instance Attributes -vs- Class Attributes**
	- Understanding the different types of variables to use with Classes is important for encapsulating your data, so that data is available only where it needs to be.
- **Instance Methods -vs- Class Methods -vs- Static Methods**
	- Understanding the different types of methods to use with Classes is important for encapsulating your data, so that data is available only where it needs to be.
- **Getters and Setters**
	- Provide encapsulation for data in instances of our class, by separating the interface to our data from its internal representation.


## Lesson


### Decorators
> Python decorators are functions that allow us to modify the behavior of another function when that function is defined. It's not essential that you use decorators in your own code, but many python libraries and frameworks use decorators, so it's good to understand how they work.

Like many programming languages, Python has first-class functions. This means that functions in Python can accept other functions as inputs, and return functions as output. Before we talk specifically about decorators, let's see a brief example of a function that returns other functions. We will create a function `WidgetMakerMaker` which does NOT create widgets. It creates other functions that DO create widgets. 

```python
# this function does not make widgets. it makes functions that create widgets
def widgetMakerMaker(widget_color):

	# this function actually makes widgets
	def widgetMaker():
		return {
			'type': 'widget',
			'color': widget_color,
		}
		
	# return the function we just created, so it can be used outside of WidgetMakerMaker
	return widgetMaker

redWidgetMaker = widgetMakerMaker('red')
red_widget = redWidgetMaker()
another_red_widget = redWidgetMaker()

blueWidgetMaker = widgetMakerMaker('blue')
blue_widget = blueWidgetMaker()
another_blue_widget = blueWidgetMaker()
```

A function that defines other functions can be useful if you have some logic that is repeated in many functions, but you don't want to write redundant code. For example, let's say we're trying to improve the performance of a slow application, so we want to print the time when every function is called to better understand what's taking so long. We can use one function to `decorate` another, which extends its functionality.

```python
from datetime import datetime

def AddOne(num):
	return num + 1

def MultiplyByTwo(num):
	return num * 2

# This function accepts a function as input, then returns a new function that behaves similarly to the one that is passed in
def PrintCurrentTimeAndAlso(func):

	# this function accepts any number of positional arguments (*args) and any number of named arguments (**kwargs)
	def WrapperFunction(*args, **kwargs):
		print(f'Calling {func.__name__} at {datetime.now()}')
		return func(*args, **kwargs)

	return WrapperFunction

# This creates a new, decorated function
PrintCurrentTimeAndAlsoAddOne = PrintCurrentTimeAndAlso(AddOne)
one_plus_one = PrintCurrentTimeAndAlsoAddOne(1)


# This creates a new, decorated function
PrintCurrentTimeAndAlsoMultiplyByTwo = PrintCurrentTimeAndAlso(MultiplyByTwo)
two_times_two = PrintCurrentTimeAndAlsoMultiplyByTwo(2)

# Using the decorator syntax (@) allows us to create a decorated function all at once,
# instead of creating an undecorated function, and then passing it into the decorator
@PrintCurrentTimeAndAlso
def SubtractThree(num):
	return num - 3

nine_minus_three = SubtractThree(9)
```


### Instance Attributes -vs- Class Attributes
- Instance attributes are variables that are unique to each instance of a class that is created (e.g. name)
- Class attributes are variables that are shared between EVERY instance of a class that are created (e.g. dog species)

Let's add some class attributes to our previous Dog class example: 

```python
class Dog:
	species = "Canis Lupus Familiaris" # all dogs have the same species type => *class attribute*
	legs = 4

	def __init__(self, name, breed, color, sound):
		self.name = name # each dog has a unique name => *instance attribute*
		self.breed = breed
		self.color = color
		self.sound = sound

fido = Dog("Fido", "Pointer", "white", "woof!")
print(fido.name)
print(fido.species)

lassie = Dog("Scooby", "Mutt", "Scooby-Dooby-Doo!")
print(lassie.name)
print(lassie.species)
```

### Instance Methods -vs- Class Methods -vs- Static Methods
> Just like attributes can belong to either an instance of a class or the class itself, methods can belong to either an instance (`instance methods`) or the class itself (`static methods`). In python, we also have access to `class methods`, which are very similar to `static methods`.

> In this example, we'll create a class for making Pizzas. Since all of our pizzas are circles, it would be useful if our class knew how to calculate the area of a circle. This knowledge is not specific to any one pizza, but is generic functionality that belongs to the class. 

```python
import math

class Pizza:
    def __init__(self, radius, ingredients):
        self.radius = radius
        self.ingredients = ingredients

    def area(self):
        return self.circle_area(self.radius)

    @staticmethod
    def circle_area(r):
        return r ** 2 * math.pi
```

> Sometimes, it's useful to have access to the class itself in a static method. We can achieve this using class methods, which are mostly the same as static methods, except that they get the class passed in as an argument. This can be useful if you want to create methods that instantiate objects in specific ways. For example, let's say we noticed that when we create new pizzas, we tend to call the constructor with the same sets of arguments, because people like to order familiar pizzas. We can create class methods to construct specific types of pizzas.

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    @classmethod
    def margherita(cls):
        return cls(['mozzarella', 'tomatoes'])

    @classmethod
    def prosciutto(cls):
        return cls(['mozzarella', 'tomatoes', 'ham'])
```

### Getters and Setters
> Often, you might need to modify an instance variable from outside of the class. In simple cases, you can just assign to an object's properties to update them, e.g. `alice.age = 32`. As your application becomes more complex, you might need to add extra rules about how these properties can be updated. If our class is well designed according to OOP principles such as encapsulation and abstraction, these rules should be described in the class itself. Other code outside of the class shouldn't need to be aware of them. We can achieve this using getters and setters in python, using the `@property` decorator.

```python
class Person:
     def __init__(self):
         self._age = 0
	 self.dead = False
       
     # using property decorator
     # a getter function
     @property
     def age(self):
	print("getter method called")
	return self._age

     # a setter function
     @age.setter
     def age(self, a):
		self._age = a
		if(a > 100):
			self.dead = True
  
alice = Person()
  
alice.age = 19
print(alice.age)
```

> A great thing about using setters and getters like this is that we don't even have to plan for this up front. We can design our application in a simple way, and directly assign to instance variables, like `alice.age = 32`. Later on, if we decide that we need extra logic when the age changes, like a Person dying when they reach age 100, we can achieve that only by changing the class definition, by replacing `self.age` with `self._age`, and using setter/getter decorators on `def age()`. The rest of our application does not need to know about the internal details of the person class. 


## Assignments

Start with App Users II. Then do Contact List. **The Caesar Cipher is less important for today, and is more to practice algorithm-style programming problems. However implementing it as a class is a good opportunity to practice writing classes.**
- [App Users II](https://github.com/sierraplatoon/oop-app-users-ii) in Python
- [Contact List](https://github.com/sierraplatoon/oop-contact-list) in Python
- [Caesar Cipher](https://github.com/sierraplatoon/algo-caesar-cipher) in JS/Python


