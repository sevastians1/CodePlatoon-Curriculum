# Python OOP Inheritance

## Topics Covered / Goals
- Understand when and why to use inheritance in your code
- Understand the relationship between child classes and parent classes
- Inherit classes successfully
- Understand the use of `super` in OOP and when to use it
- Understand different types of multiple inheritance:
    - one parent with multiple children
    - one child with multiple parents

## Lesson

### Inheritance in Python
> Inheritance is a relationship between two classes, a parent class and a child class (or a superclass and a subclass). Specifically, inheritance helps us model relationships where one class is a subtype of another class. For example, 'a dog is an animal. All dogs are animals, but not all animals are dogs.'


```python
class Dog:
    def __init__(self, name):
        self.name = name

    def eat(self, food):
        print(f"{self.name} eats a {food}.")
    
    def bark(self):
        print("BARK BARK!")

spot = Dog('Spot')
spot.eat('steak')
```

> In the example above, we say that every dog has an attribute called `name`. Every dog also has the ability to `eat` and `bark`.

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def eat(self, food):
        print(f"{self.name} eats a {food}.")
    
    def meow(self):
        print("meow meow!")

garfield = Cat('Garfield')
garfield.eat('tuna')
```
> In the example above, we say that every cat has an attribute called `name`. Every cat also has the ability to `eat` and `meow`. This `Cat` class is pretty similar to our previous `Dog`. This isn't just a coincidence, like how a table and a horse both have four legs. Cats and dogs are both able to eat because they are both types of animals. Using inheritance, we can rewrite the above classes to avoid repeating code.

```python
class Animal:
    def __init__(self,name):
        self.name = name
    def eat(self, food):
        print(f"{self.name} eats a {food}.")
    def speak(self):
        print("I'm an animal!")

class Dog(Animal):
    def speak(self):
        print("BARK BARK!")


class Cat(Animal):
    def speak(self):
        print("meow meow!")

lucky = Cat('lucky')
# the name attribute and eat method aren't defined on the Cat class, but this works because Cat inherits them from Animal
lucky.eat('chicken nugget') 

```
> Notice how we passed the `Animal` class to the `Cat` and `Dog` classes. This indicates that `Animal` is a parent class of both `Cat` and `Dog`. As such, `Cat` and `Dog` have access to all of the methods from animal, even `__init__`. This might seem a little unintuitive because the child class is always 'bigger' than the parent. The child class has access to everything in the parent class(`__init__`, `eat`), and then also has its own method `speak`, which overrides `speak` from the parent class.

**super()**
> Sometimes, we'll want to define a child class that doesn't use the exact same methods as its parent, but we don't want to override it entirely, like we just did with `speak`. 
> We can use the `super()` method to access an instance of the parent class, call any of its methods from the child class, and then add more custom logic afterwards. 
> super() returns a temporary object of the superclass that allows you to call that superclass’s methods.
> This is commonly used to repeat the `__init__` method from the parent class, and then add an extra property at the end. 

For example, a difference between dogs and cats is that a dog can be a registered service animal, whereas a cat cannot.

```python
class Animal:
    def __init__(self,name):
        self.name = name
    def eat(self, food):
        print(f"{self.name} eats a {food}.")
    def speak(self):
        print("I'm an animal!")

class Dog(Animal):
    def __init__(self, name, is_service_animal):
        # use the logic from the parent Animal class without duplicating code
        parent_instance = super()
        parent_instance.__init__(name)

        # add some Dog-specific setup
        self.is_service_animal = is_service_animal

    def speak(self):
        print("BARK BARK!")


class Cat(Animal):
    def speak(self):
        print("meow meow!")

sparky = Dog('sparky', True)
```
> The example above demontrates a common use for inheritance, where you have multiple child classes that share code from a common parent class. Inheritance can also work the other way, where one child class inherits from multiple parent classes. 

```python
import math

class Person:
    def __init__(self, name, job):
        self.name = name
        self.job = job
    def work(self):
        print(f"{self.name} is working hard as a {self.job}.")
    
class Computer:
    def __init__(self, number_of_cores):
        self.number_of_cores = number_of_cores

    def compute(self):
        print(round(math.pi, self.number_of_cores))

toaster = Computer(1)
toaster.compute()

compy386 = Computer(4)
compy386.compute()

class Cyborg(Person, Computer):
    def __init__(self, name, job, number_of_cores):
        Person.__init__(self, name, job)
        Computer.__init__(self, number_of_cores)

    def work(self):
        for n in range(self.number_of_cores):
            Person.work(self)

terminator = Cyborg('Arnold', 'terminator', 8)
terminator.work()
```


**When to use inheritance**
> Use inheritance to write less code. If you are writing an object oriented program with many classes, check if there are many similarities between your classes that have caused you to write redundant code. If you have two classes that are related (e.g. Animal, Dog), but they don't actually share any redundant code, you don't need inheritance. Verify that those similarities are due to inherent is-a relationships that are unlikely to change.
> For example, a table and a dog both have four legs, but this is not due to an inherent relationship, and as your program becomes more complex, there will be more differences between tables and dogs, and it will make less sense for them to inherit from a common ancestor (4-legged). If you have multiple classes that share code with each other, where one is inherently a type of the other, or both of them are inherently types of a third thing, consider using inheritance in your program so that you can write less code. 

## External Resources
- [Python super() method](https://realpython.com/python-super/)
- [Method Resolution Order(MRO)](https://www.educative.io/edpresso/what-is-mro-in-python)

## Assignments
Please Start with School Interface I. Then follow with the Boggle exercise, and lastly App Users III

**Note:** Today in particular has several larger assignments! It is OK if you don't finish them all today.

- [School Interface I](https://github.com/sierraplatoon/oop-school-interface-i)
- [Boggle I](https://github.com/sierraplatoon/oop-boggle-i)
- [App Users III](https://github.com/sierraplatoon/oop-app-users-iii) in Python
- [Boggle II](https://github.com/sierraplatoon/oop-boggle-ii)



