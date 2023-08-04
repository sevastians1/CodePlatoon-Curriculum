# Application Testing

## Topics Covered

## Goals
- Learn how to test a web application

## Lesson

### WHO writes tests?
- Developers test their own code
  - developers are more responsible for making sure their code actually works
  - developers understand the requirements better
- Dedicated testing team
  - allows efficient division of labor

> There are merits to both styles of testing, but for the rest of this lecture, we'll assume that we will be testing our own code. 

### WHY write tests?
> Testing code is one of the most important things you can learn to do in your software development career. Many employers will not hire you if you do not have at least `SOME` understanding of what testing is and how to begin doing it.

Testing your code helps you build confidence in your logic, assuring you that your code will work as expected in production and will `CONTINUE` to work in production over time as you add features to the application and expand your test suite (group of tests). 

- Catching bugs in development is cheaper/faster than catching them in production
- Gives confidence that you can refactor old code without breaking critical components
- Helps enable continuous delivery, so you can ship code more frequently
  - some dev shops with poor tests rely on manual testing to have confidence that a new app version is stable
- Prevents bugs from reoccurring
- serves as verifiable documentation of the code

### Types of Testing
> There are several types of testing, tons of testing frameworks, and tons of different opinions on what testing even is, how to do it, and what to call things. We could give an entire boot camp on testing! But for now, instead, we will just cover the basics.

#### Unit testing
> A software development process in which the smallest testable parts of an application, called units, are individually and independently scrutinized for proper operation.

- is often automated but it can also be done manually.
- usually testing the inputs and outputs of a single function in a application
- should be very fast
- for **JavaScript**, you will likely use
    - `jasmine` (front-end/back-end assertion library)
    - `mocha` (opinionated testing framework oriented toward express)
    - `chai` (front-end/back-end assertion library)
    - `jest` (a library from facebook that is commonly used with react)

##### Steps
1. Determine the inputs of each function of an application
    - define the inputs in `optimistic` cases (good user input, expected API inputs, etc.)
    - define the inputs in `pessimistic` cases (bad user input, `undefined`, etc.)
2. Create assertions (expectations) for your test cases
3. Execute units tests
4. Verify assertions satisfy expectations



> Let's write a couple unit tests using vitest. First, we'll need to install it with `npm install vitest`. Then, we'll want to add a script to our `package.json` that invokes vitest: `"test": "vitest run"`. This will cause vitest to search through our project for any file that ends with `.test.js`, and test that file.


> Let's create a basic test suite, containing a single test. We'll make a folder called `tests`, and create a file inside called `random.test.js`. 
```javascript
import { expect, it, describe} from 'vitest'

describe('Math.max', ()=>{
	it('should return the largest number', ()=>{
		const biggerNumber = Math.max(1,5)
    	expect(biggerNumber).toBe(5) 
	})
})
```
> Even though this test is about as simple can be, there are probably a few unfamiliar concepts here. First, notice that our entire test suite is contained inside of the `describe()` call. `describe` is simply used for grouping and labeling tests. The first argument is a string that describes the contents of that `describe` block, and the second argument is a callback function that contains all the tests that are grouped under that `describe` block.

> Inside of describe, we have an `it` block. `it` is similar to `describe` in that its first argument is a string describing the test, and then the second argument is a callback function that contains a specific test. Unlike `describe`, `it` is more specific. Whereas `describe` is for describing/grouping multiple tests, `it` is supposed to contain a single test. 

> Inside of the test in the `it` block, there may be multiple expectations, but the example above has only one. `expect` is a function that takes an expression, and lets us make an assertion about its value. The above example has a simple assertion, testing that it is equal to some specific value, `5`. If that's not true, `expect` will throw an error. After running your tests, the test runner will show you all the tests that failed, with their labels printed out. For a failed test, we might see output like this: 

```
 FAIL  tests/unit/math.test.js > Math.max > should return the largest number
AssertionError: expected 5 to be 1
```

> Notice how the strings from our `describe` and `it` blocks were concatenated together in the error message. If you are thoughtful about how you write these strings, then the error output from your tests will be a grammatical english sentence that describes exactly what went wrong. 

> Like we mentioned before, a good test suite should contain both optimistic and pessimistic tests. Let's add some more tests to our suite.

```javascript
import { expect, it, describe} from 'vitest'

describe('Math.max', ()=>{
    describe('with valid inputs', ()=>{
        it('should return the largest number', ()=>{
            const biggerNumber = Math.max(1,5)
            expect(biggerNumber).toBe(5) 
        })
        it('should handle negative numbers', ()=>{
            const biggerNumber = Math.max(1,-5)
            expect(biggerNumber).toBe(1) 
        })
        it('should handle any number of inputs', ()=>{
            const biggerNumber = Math.max(1,2,3,4,5,6)
            expect(biggerNumber).toBe(6) 
        })
    })
    describe('with invalid inputs', ()=>{
        it('cannot return the largest value from an array', ()=>{
            expect(Math.max([1,2])).toBe(NaN)
        })
        it('cannot return the largest value from a list of strings', ()=>{
            expect(Math.max('a', 'b')).toBe(NaN)
        })
    })
})
```


> In order to unit test our code, we might need to refactor it to make it testable. You want as much of your application as possible to consist of small, pure functions, that only take input and return output, without causing side effects. These functions should be exported from the files they're written in, so that they can be imported into the test file. 

```javascript
const getCSRFToken = (cookieString)=>{
    let csrfToken
  
    // the browser's cookies for this page are all in one string, separated by semi-colons
    const cookies = cookieString.split(';')
    for ( let cookie of cookies ) {
        // individual cookies have their key and value separated by an equal sign
        const crumbs = cookie.split('=')
        if ( crumbs[0].trim() === 'csrftoken') {
            csrfToken = crumbs[1]
        }
    }
    return csrfToken
  }

export {
    getCSRFToken,
}
```

> The code above is the function we use for getting the CSRF token from the cookie string, but we've made some changes to it to make it testable. First, instead of letting this function directly access the cookie string from the document, it is passed in as an argument. Also, we defined this function in its own file and exported it, instead of defining it directly in a component's file. 

##### Mocking
> It's very important that our unit tests are fast, but sometimes we want to test functions that perform slow or expensive operations. We can use a mock function that simply returns some hardcoded value instead of actually performing the slow operation. For example, you might create a mock to generate API responses, or database query sets, because sending API requests and querying the database are both slower than a unit test should be. 

#### Integration testing
> Tests the connection between two or more components. These are slower than unit tests, but still relatively fast. An individual integration test is generally more valuable than an individual unit test, because it simultaneously tests many parts of the system. 

- Usually tests a `process` within an application, but does not usually test an entire `feature`.
- Common examples:
  - testing functions that query the database
  - testing API routes




#### Functional/End-to-end testing
> Test an entire feature, from the client to the server and back. These are the slowest kinds of tests you can write, because they require automating a web browser, but they also give you the most reliable information about whether your application as a whole "works" from a user's perspective. You can drive a browser to click links, submit forms, read text off the screen, and most other actions that a human user could do with a browser. 



- Verifies that each `feature` of a piece of software satisfies the `application requirements`
- Mainly involves `black box` testing, meaning the tests are not concerned about the `source code` of the application
  - Functional tests can be done by a dedicated testing team that doesn't even know the language the app is written in
- Each and every `feature` of the system is tested by providing appropriate input (optimistic), verifying the output, and comparing the actual results with the expected results
- Involves testing the UI, APIs, Database, security, and the client & server side of applications
- Can be `automated` or driven `manually`
- Use tools such as Selenium or puppeteer. 

> Traditionally, Selenium was by far the most popular choice for browser automation. More recently, many other similar tools have been created that are simpler and more convenient. Today, we'll be using Puppeteer.

```bash
npm install puppeteer --save-dev
```

```javascript
describe('the application', ()=>{
    let browser
    let page
    beforeAll( async ()=>{
        browser = await puppeteer.launch({headless:false, slowMo: 100})
        page = await browser.newPage()
    })
    afterAll( async ()=>{
        await browser.close()
    })
    describe('homepage', ()=>{
        it('should welcome the user', async ()=>{
            await page.goto('http://localhost:8000')
            await page.waitForSelector('#welcome')
            const welcomeEl = await page.$('#welcome')
            const welcomeText = await welcomeEl.evaluate((el)=> el.innerText)
            console.log(welcomeText)
            expect(welcomeText.includes('Welcome')).toBe(true)
        })
	})
})
      
```

##### Steps
1. Understand the feature requirements
2. Establish sensible input
3. Establish expected output
4. Execute test cases
5. Compare actual results with expected results

### HOW MANY tests to write?
> Writing tests is expensive. 100% test coverage is not necessarily your goal.

- It takes time to write tests initially, and they need to be maintained as your application changes over time.
- Tests themselves can have bugs
- Each test raises the possibility of false-positive, which waste time
- Each test increases the length of your test results, making them harder to read

Make sure you get more value from your tests than they cost you to create. Only test 'important' features of your application.

- The CSS on the admin dashboard of your app will probably never break, and if it does, barely anyone will notice. If they do, they might not even care. Don't Go crazy testing that every CSS rule in your app applies correctly on every page.
- Signup/Login is probably critical to your application. Many applications are worthless if users can't log in. Do test the signup and login features of your app. Test all of the common failure cases (wrong username, wrong password, etc.) to be sure that your login/signup functionality behaves correctly in all cases.

### WHEN to WRITE tests
- **basic**: Writes tests after you complete a feature to make sure that it works, and won't break in the future.
- **reactive**: When a bug happens in production, fix the bug and write a test to make sure it doesn't happen again.
- **TDD**: Write tests before you even start writing the code for a feature and watch it fail. 
  - This helps define the requirements for that feature. 
  - This style of programming tends to slow down development while reducing bugs, which is useful depending on what you're writing code for, e.g. medical devices, security systems, etc. 

### WHEN to RUN tests
- **basic**: Run tests manually after making major changes, and before you deploy your app.
- **continuous integration**: Run your tests automatically, so you can deploy automatically.
- **before deploying to a different environment**: Run selective tests in Development before releasing to Staging, run all tests in Staging before releasing to Production.
- If your tests are fast enough to run in seconds, you can configure your editor to run them when you save a file.
- If your tests are fast enough to run in minutes, you can configure git to run the tests every time you commit.

## External Resources
- [Jest](https://jestjs.io/docs/getting-started)
- [Puppeteer](https://github.com/puppeteer/puppeteer)

## Assignments


