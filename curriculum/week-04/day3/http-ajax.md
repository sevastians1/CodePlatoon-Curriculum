# HTTP and AJAX


## Topics Covered / Goals
**Core**
- Understand the basics of HTTP
  - web protocol built on top of TCP (guarantees packets are delivered in order)
  - request/response cycle
  - What does the structure of an HTTP request look like, and what is the purpose of the `body` and `header`?
  - What action verbs does HTTP use, and when should you use them? `GET`, `POST`, etc.
- Understand what an API is
- Learn to send AJAX requests using `axios`, a popular, modern HTTP client for javascript
- Learn some tools (other than the browser) for testing APIs

## Lesson
> There are strict rules about how applications can communicate with each other over the internet, known as a protocol. However, communication on the web is not defined by a single protocol, but by a stack of protocols, known as the OSI model.

### IP - Internet Protocol
> The first protocol you should be aware of is IP, Internet Protocol. IP is concerned with WHERE a server is located on the internet. This location is described with a number called an IP address, which is similar to a physical address. Most of the world currently uses the fourth version of the IP protocol (IPv4), in which an IP address is a series of 4 numbers, separated by periods, like '192.0.2.1'. Whenever you type a domain name into your browser's url bar, your browser has to convert that into an IP address to know where to send the request. This is done using the Domain Name System (DNS).

### TCP - Transmission Control Protocol
> IP is only concerned with WHERE a server is, but not HOW to get there. TCP (transmission control protocol) provides reliable, ordered, and error-checked delivery of a stream of bytes between applications running on hosts communicating via an IP network. The reliability makes this protocol a little bit slower, since every single TCP connection actually requires multiple requests to initiate, known as a 3-way handshake. TCP was designed alongside IP, so they're often referred to together as TCP/IP. 

### HTTP - Hypertext Transfer Protocol
> TCP is only concerned with HOW data is transferred, but not WHAT data is transferred, or how the data is formatted. The format of our application data is determined by the HTTP protocol. 
- [HTTP Slides](https://docs.google.com/presentation/d/15Mq7xn5nQVDjShPOZd4cwVi807oQvNfLE3irJDrlgwU/edit#slide=id.p)

### APIs - Application Programing Interface
> An API is an Application Programming Interface. An API is essentially a contract between two pieces of software that determines how they can interact with each other. For example, every library or framework you'll use has an API that describes what methods you can call from the library/framework, and what you can expect them to do. Today, we'll be using a JSON API that lets our front end request data from someone else's database. There are [many free, public APIs](https://github.com/public-apis/public-apis) that you might want to use.

### AJAX - Asynchronous Javascript and XML
> There are many ways to send an HTTP request, such as clicking a link, submitting a form, or just typing a url into your browser. However, most ways of sending HTTP requests from your browser will unload the page, and replace it entirely with the contents of the HTTP response. To build a more elegant, modern application, we need a way to send an HTTP request in the background without interrupting the user's experience. This technique is known as AJAX: 'asynchronous javascript and xml'. 'asynchronous javascript' just means that we're doing work with javascript in the background, while other processes continue. XML is a data type that can potentially be returned by an AJAX request. In modern applications, XML is not commonly used, but JSON (javascript object notation) is used instead. However, even though we typically get JSON, not XML, from AJAX requests, we still call this technique 'AJAX', because that sounds cool, and 'AJAJ' sounds weird. 

> This technique was first made possible in 1999 in Internet explorer, through an object that is now known as `XMLHttpRequest`, or XHR. XHR enabled developers to build many kinds of applications that wouldn't have been possible otherwise, although it was pretty awkward to work with. More recently, modern browsers have started supporting `fetch()`, which provides more convenient access to the same functionality. However, neither of these options is as powerful and convenient as some 3rd party AJAX tools. 

### Axios
> Axios is a popular, modern HTTP client for javascript. Let's try using Axios to make some requests to the pokemon API. 

### Promises from Axios
> When Axios performs a request, it takes a few hundred milliseconds to complete, but we wouldn't want our application to just do nothing while it waits. Instead, axios immediately returns a `promise` object, which lets us specify what we want to do once the request finishes. The promise object has two important methods: `.then()` accepts a callback function to specify what happens if the request succeeded, and the promise is 'resolved'. The promise also has a method called `.catch()`, which accepts a callback function to specify what happens if the request fails, and the promise is 'rejected'. It's also important to understand that `.then()` and `.catch()` also return promises, so these methods can be chained off of each other.

```javascript
axios.get('https://pokeapi.co/api/v2/pokemon/12/').then((response)=>{
    console.log(response)
}).catch((error)=>{
    console.log('no good: ', error)
})
```

### async/await
> Handling promises is an essential skill for an intermediate javascript developer. However, one downside to using promises is that if you're performing a series of asynchronous actions, your code will keep getting indented deeper and deeper. One way to work around this problem is by using async/await, which lets us write promise-based async code in a similar way to how we would write synchronous code. Any time you could call `.then()` on a promise, you can instead `await` that promise, so that your program will wait until the promise resolves, and then returns that value. You can only use `await` inside functions that are marked as `async`. 

```javascript
const getMon = async ()=>{
    const response = await axios.get("https://pokeapi.co/api/v2/pokemon/1/")
	const typeUrl = response.data.types[0].type.url
	console.log(typeUrl)
    const typeResponse = await axios.get(typeUrl)
    console.log(typeResponse)
}
getMon()
```

### Making our own promises
> Generally, when you need to perform an asynchronous action, whatever library you are using will create promises for you to use. However, sometimes we'll have to create our own. For example, `setTimeout` is an asynchronous function, but it doesn't return a promise. We can create our promise if we want to handle `setTimeout` using `.then()` and `.catch()`. 

```javascript
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
		resolve('foo');
    }, 300);
});
```


## External Resources
- [Axios docs](https://axios-http.com/docs/intro)
- [Postman docs](https://learning.postman.com/docs/getting-started/sending-the-first-request/)

## Assignments
- [Pokemon Theme Team](https://github.com/sierraplatoon/pokemon-theme-team)
- Find an api that interests you and build a simple web page that utilizes axios to call the api and then displays the data on the page. Get creative and have fun with it! :) 


