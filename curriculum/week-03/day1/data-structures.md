# Data Structures


## Topics Covered / Goals
- Know what a data structure is and understand their purposes
- Learn the different types of data structures and the main operations/methods associated with each
    - Queues and Stacks
    - Binary Search Trees
    - Hash Tables

**Stretch**
- Understand how to construct each type of data structure

## Lesson


> in computer science, a data structure is a data organization, management, and storage format that enable efficient access and modification of data. in other words, a data structure is a collection of values, the relationships between them, and the functions or operations that can be performed on them. 

## Why does this matter? Why is there more than one data structure?

> Different data structures can perform different operations at different speeds. Depending on which operations you need to perform most often, you might choose a data structure that prioritizes those operations over others. 

```javascript
const data = ['a', 'x', 'y', 'g', 'n','b']
data.unshift('q') // adds 'q' to the start of the array - O(n)

data.push('L') // adds 'L' to the end of the array - O(1)

locationOfG = data.indexOf('g') // searching for a value in an array - O(n)
```

```javascript
// a sorted array of letters
const data = ['a', 'c', 'l', 'm', 'r', 't', 'x']
data.addButKeepSorted('n') //  O(n)

// searching a sorted list is O(log n), because we can use binary search

```


> "Bad programmers worry about the code. Good programmers worry about the data structures and their relationships" - Linus Torvalds


## Arrays
> javascript has a data structure called arrays. that's not what I'm talking about today.  
> All the data for your array is in adjacent slots in memory. this makes searching fast. if you try to push to an array that is full, your computer needs to create a new, larger array, copy the old array to the new one, and delete the old one. 

## Linked list
> a linked list is a non-contiguous data structure, meaning the elements of a linked list are not necessarily next to each other in memory.
- a singly linked list - each node has a reference to the next node
- a doubly linked list - each node has a reference to the next node and also the previous node

## stack
> a stack is like a list in python, if we could only modify it with `push()` and `pop()`. 
processes items in LIFO order (last in, first out)


## queue
> a queue is a like a list, if we could only modify the first item with `enqueue()`, and remove the last item `dequeue()`. 
A customer service help line would be a good example of a queue


## Hash table
hash tables allow you to create a list of key-value pairs. after creating a pair, you can retrieve a value using the key for that value.

### hashing
- a hashing function takes some value as input, and returns a hash, an integer in this case. 
- hashing is a one-way operation. while it's generally fast to hash a value, there is no straightforward way to reverse a hash
- hashing the same input always produces the same output (no randomness)
- it is possible, but unlikely, that multiple values will have the same hash (hash collision) 

```javascript
class HashTable {
    constructor(){
        this.table = new Array(64).fill(0)
        this.table = this.table.map((el)=>{
            return []
        })
    }

    // given a key, this function should return a numerical index, which we can use to access the table above
    _hash(key){
        let hash = 0
        for ( let i = 0; i < key.length; i++ ) {
            hash += key.charCodeAt(i)
        }
        return hash % this.table.length
    }

    set(key, value){
        const index = this._hash(key)
        this.table[index].push([key,value])
    }

    get(key){
        const index = this._hash(key)
        for ( let data of this.table[index] ) {
            if ( data[0] == key ) {
                return data[1]
            }
        }
    }
}

myHash = new HashTable()

myHash.set('name', 'alice')
myHash.set('age', 34)
myHash.set('mane', 'luxurious') // this key will cause a hash collision with 'name', because they are anagrams

console.log(myHash.table)
console.log(myHash.get('name'))
console.log(myHash.get('age'))
console.log(myHash.get('mane'))
```

## Other data structures
A tree is a data structure with a root node, which can have many child nodes, which each can have many child nodes.
A binary tree is a tree where each node has at most two children. 
A binary search tree is a binary tree in which each left node is smaller than each right node. This structure can be searched efficiently using binary search. 

a B-Tree is NOT a binary tree. It's similar, but can have more than 2 children per node, depending on the order of the tree (a 4th order tree has at most 4 children per node). These are commonly used in popular databases. 


## Resources
- [Data structure visualization](https://cmps-people.ok.ubc.ca/ylucet/DS/Algorithms.html)
- [Data structures in the indunstry](https://blog.pragmaticengineer.com/data-structures-and-algorithms-i-actually-used-day-to-day/amp/)

## Assignments
Start with the Data Structures exercise. That is our focus for today. After you are done with that, move on to Bank Accounts, whhich focuses more on writing programs in an object-oriented (OOP) style. 
- [Data Structures](https://github.com/sierraplatoon/algo-data-structures)
- [Bank Accounts](https://github.com/sierraplatoon/oop-bank-accounts)



