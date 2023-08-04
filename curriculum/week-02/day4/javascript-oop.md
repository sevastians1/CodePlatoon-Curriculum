# Javascript OOP

## Topics Covered / Goals
- Few programmers truly embrace prototype-based programming, and neither will we. We will use JS prototypes to recreate OOP.
- The `this` keyword in javascript
    - the global object (`window` in the browser vs `global` in node)
    - Using the `function` keyword vs arrow functions
- Object Oriented Programming in Javascript
    - The `new` keyword and constructor functions
    - Adding instance methods to the prototpye
    - The `class`keyword (syntactic sugar)


## Lesson

### Prototypes vs Classes
> In javascript, we don't actually have proper OOP classes like in most programming languages. Instead of classes, javascript has PROTOTYPES, which can be used to define the shared behavior of a group of objects, similarly to classes. Prototypes in javascript have some unique benefits when compared with classes in other languages, but we will not be exploring the ways that prototypes are better than or different from classes. Instead, we will learn how to recreate OOP in javascript using the tools that are available to use. 

### The `this` keyword
> Before learning about OOP or prototypes in javascript, first we need to understand how the `this` keyword works in javascript. It's mostly the same as using `self` in python, but `this` in javascript actually has uses outside of OOP, so let's see that first. In general, when you see `this` inside of a `function(){}`, `this` refers to the object that the function is attached to. Note that fat-arrow functions `()=>{}` do not rebind the value of `this`. Only regular functions defined with the `function` keyword will affect the value of `this`. This is the primary difference between traditional functions and fat-arrow functions. 

```javascript
// `this` is not attached to any object, so it refers to the window in browser js, or {} in node. either way, it's not what you want
console.log(this)

// This is the simplest way to use `this`. `this` appears in the function `sayHello`, which is attached to the `alice` object, so the value of `this.name` in this case would be alice.name === 'alice'
const alice = {
    name: 'alice',
    sayHello: function(){
        console.log(`Hello, my name is ${this.name}`)
    }
}

alice.sayHello()

// arrow functions do not rebind `this`.
// `this` in this function still refers to the window. not very useful. 
const bob = {
    name: 'bob',
    sayHello: ()=>{
        console.log(`My name is ${this.name}`)
    }
}

bob.sayHello()

// `this` appears in a setTimeout callback, which is not attached to any object,
// so `this` will refer to the window. 
// It doesn't matter that `sayHello` is attached to the carol object, 
// because the actual function that contains `this` is not attached to any object
const carol = {
    name: 'carol',
    sayHello: function(){
        setTimeout(function(){
            console.log(`My name is ${this.name}`)
        })
    }
}

carol.sayHello()

// `this` appears in a ()=>{} function, which doesn't affect `this`.
// We'll have to look for the closest function(){} that contains `this`, which is `sayHello`
// `sayHello` is attached to the dan object, so `this.name` refers to dan.name. 
const dan = {
    name: 'dan',
    sayHello: function(){
        setTimeout(()=>{
            console.log(`My name is ${this.name}`)
       }, 100)
    }
}

dan.sayHello()

// the value of `this` depends on how the function is CALLED, not how it is DEFINED
const sayHello = function(){
    console.log(this.name)
}

// this.name is undefined or empty string, depending on the environment
sayHello()

const eve = {
    name: 'eve',
    sayHello: sayHello,
}

// in this case, `this` refers to the eve object, so `this.name` is 'eve', 
eve.sayHello()
```

### The `new` keyword
> In javascript, we don't use a `class` keyword for creating classes. Instead, we just use normal functions. However, if we want to use a function as a constructor for an OOP class, we call it differently, by adding the `new` keyword. This slightly changes how the function executes. 

```javascript
// we're pretending this is a class, so we named it with a capital letter
const Person = function(name){
    // const this = {}
    this.name = name

    // return this
}

const alice = new Person('Alice')
```

> When we use the `new` keyword, `this` inside the function refers to a new object, which is automatically returned at the end of the function. We can define instance attributes by adding properties to `this`. We can also add methods this way, but that's not the most efficient option. If we had 1,000 objects, they would all have a copy of the same method. Instead, we will use prototypes to define methods that all instances of this class have access to. If you try to call a method that does not exist on an object, javascript will check that object's constructor's prototype, and see if the method exists there. 

```javascript
Person.prototype.sayHello = function(){
    console.log(`Hello, my name is ${this.name}.`)
}

alice.sayHello()
```

### The `class` keyword
> In modern javascript, we have the option of using the `class` keyword to set up our prototypes. This is not an entirely new feature, it's simply a cleaner, more elegant way of writing constructor functions. This is sometimes called 'syntactic sugar' because it provides us a new syntax that is enjoyable to use, but doesn't actually provide any new features. Let's recreate the above `Person` class using javascript's class keyword. 

```javascript
class Person {
    constructor(name){
        this.name = name
    }
    sayHello(){
        console.log(`Hello, my name is ${this.name}.`)
    }
}

// using the class keyword has no effect on how we instantiate objects from our constructor,
// or how we call methods from our instances. 
const bob = new Person('Bob')
bob.sayHello()
```

> When writing modern javascript, there are several reasons to prefer using the `class` keyword instead of explicitly modifying prototypes. Using the `class` keyword lets you write OOP code that is similar to what you would write in most other languages, and it more accurately describes our intentions: we want a class, with a constructor function, and instance methods. Also, using prototypes is a little ugly because your class is defined across multiple code blocks, for the constructor function and also the prototype methods, whereas when you use the `class` keyword, the entire class is stored in the `class` block. HOWEVER, you still need to understand how prototypes work. If you write an object-oriented javascript program, and it's not behaving how you expect it to, there will be no classes for you to inspect. You'll need to debug your prototypes.

## Assignments
Start with School Interface II. Then, Pig Latin. Stock Picker is the stretch assignment for today.

- [School Interface II](https://github.com/sierraplatoon/oop-school-interface-ii) in Python
- [Pig Latin](https://github.com/sierraplatoon/algo-pig-latin) in JS/Python

Stretch:
- [Stock Picker](https://github.com/sierraplatoon/algo-stock-picker) in JS/Python


