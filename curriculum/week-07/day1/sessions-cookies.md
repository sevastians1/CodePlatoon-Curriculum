# Sessions and cookies

## Topics Covered / Goals
- use sessions so users don't have to authenticate every request
- server uses the `set-cookie` response header to start the session
- client automatically uses `cookie` request header to continue the session
- automated cookie behavior is convenient, but potentially creates security vulnerabilities (CSRF, XSS)
- Password Management
  - use one-way hashing to avoid storing plain text
  - use salt to ensure unique passwords
- Sign up, Log in
    - Authentication (logging in and out) is an important feature for any web app. It allows users to store data private to them and blocks anonymous users from viewing protected routes


## Lesson

> Up to this point, we haven't had the ability to log in or out of our application. We've been able to save data in the database, but we haven't been able to securely, reliably associate that data with its owner. Most modern web applications require users to log in and start a session, so that they can only modify their own data. Today, we'll be learning about related concepts and technologies that are used for logging in to a web application, such as cookies, sessions, and password hashing. Later this week, we'll be learning about some of the tools that django provides that make it easier for us to implement authentication in our applications. 

### Cookies

> We learned previously that HTTP is a stateless protocol, meaning that by default, we don't automatically know if multiple requests are coming from the same user. The way that modern applications are able to remember who you are across different pages is through `cookies`. There are two commonly used definition for cookie:
- a small piece of data that is stored in a user's browser, when it is instructed to do so by the server via the `set-cookie` HTTP response header. 
- any web technology that can be used to track users between pages, similarly to using the `set-cookie` header.

> We'll be working with the first definition, but you should be aware that this term is sometimes used vaguely to refer to other technologies. 

> The basic way that cookies work is: when a server responds to an HTTP request, it can include the header `set-cookie`, with any number of key-value pairs as its value. When that same user sends another HTTP request to that same server, the user's browser will automatically send all the cookies it was given in the `set-cookie` response header, by including them in a `cookie` request header. 

> Even though cookies are just set and read in an HTTP header, they have their own syntax, so it's more complicated than a simple string. Fortunately, django has some built-in functionality that will let us focus on the concepts of cookies, without worrying about the specific syntax too much. We can use `response.set_cookie` to set a new key value pair in a cookie, as well as set some options for the cookie, such as:
- max_age. The amount of time, in seconds, the cookie should be valid for. After this much time passes after the client receives the `set-cookie` header, the cookie becomes 'stale' and is no longer automatically included in future request headers. Setting this value to `None` makes the cookie last as long as the user's browser session. Your bank's website probably uses a relatively small value for max_age in its cookies, compared to a social network that wants you to never log out.
- httponly. Normally, cookies can be accessed with javascript via `document.cookie`, but this is generally not necessary since browsers handle cookies automatically. Setting a cookie as `httponly` makes it inaccessible from javascript, which can potentially prevent certain security vulnerabilities. 
- secure. Secure cookies will only be used over HTTPS. If the page was requested with plain HTTP (not HTTPS), the cookies are ignored.

> When the client sends the cookies back up to the server, we can read them in django on a dictionary at `request.COOKIES`.

```python
def index(request):
    print(request.COOKIES)
    print(request.COOKIES.get('foo'))
    response = render(request, 'blog/index.html')
    return response

def set_cookies(request):
    response = render(request, 'blog/index.html')
    response.set_cookie('foo', 'bar', httponly=False, secure=False, max_age=5)
    response.set_cookie('more_secure', 'not_for_js', httponly=True, secure=False, max_age=5)
    return response
```

> Even if we go to other pages that aren't setting cookies, our browser will still continue sending cookies until they expire, as long as we're in the same domain.

### Sessions

> Often when users interact with a website, they expect their actions and preferences to be remembered. For example, if you mute a video that auto-played in a news article, you might hope that the website remembers that preference and also mutes the videos for other articles you read afterwards, even if you're not logged in. Many shopping websites allow users to add items to their cart, which persists across pages, without logging in. These are both examples of sessions, in which we use cookies to create a consistent user experience across different pages of our site. 

> It's important to note that sessions are distinct from logins. Many sites will create a session for you as soon as you visit, before you log in. It's also possible to have logins without a session. For the front end of a website, this would create a bad user experience, since you'd have to log in again every time you load a new page. For an API, this is pretty normal, since you often need to use an API key to authenticate every request. 

> We could potentially try storing session data on the cookie itself, but cookies have a very limited size. Instead, let's just leave a session ID in the cookie, and store the rest of the user's data in the server. 

```python
sessions = {}
def increment_count(request):
    print(sessions)
    session_id_number = request.COOKIES.get('session_id_number')
    session = sessions.get(session_id_number)
    if not session:
        session_id_number = str(random.randint(100000,999999))
        
        sessions[session_id_number] = {
            'count': 1,
            'start_time': datetime.datetime.now()
        }
    else:
        sessions[session_id_number]['count'] += 1
    response = render(request, 'blog/count.html', sessions[session_id_number])
    response.set_cookie('session_id_number', session_id_number)
    return response
```

```html
    <p>Youve visited this page {{count}} times, since {{start_time}}. </p>
```

> This is a very simple implementation of sessions. However, this only works for a single route, and the whole point of sessions is to persist the state across different pages. We could repeat similar logic across all the routes of our site, but in order to keep our route handler DRY, we need to use something called middleware. In this context, middleware is a function that a server applies to all requests it receives, or at least all requests to some group of routes. It's good to know how to write custom middleware, but in this case we can rely on django's built-in session middleware. Django stores the session data in the database instead of in memory, so we need to run the initial migrations first.

```bash
`python manage.py migrate`
```

```python
def increment_count(request):
    if not request.session.get('count'):
        request.session['count'] = 1
        request.session['start_time'] = datetime.datetime.now().__str__()
    else:
        request.session['count'] += 1
    
    return render(request, 'blog/count.html', {
        'count': request.session.get('count'),
        'start_time': request.session.get('start_time'),
    })
```

### Cookie vulnerabilities (CSRF)

> It's very convenient that cookies are automatically stored on the client and sent to the server, but sometimes this automatic behavior can be exploited by hackers. If a hacker tricks a user into clicking on a link that does something destructive, like resetting their password, the request will be performed with the user's cookies. Depending on other factors, a hacker might be able to delete your data or take control of your account. This kind of vulnerability is called Cross-Site-Request-Forgery, or CSRF. It's pronounced "sea surf".

> To prevent CSRF exploits, django will not process certain types of 'unsafe' requests unless you also provide a CSRF token, a small piece of data that must be MANUALLY sent to the server, unlike cookies, just to prove that the request was a legitimate request from a user from within our application. In the near future, we'll learn how to properly include a CSRF token in ajax requests to satisfy django's security requirements. For today, however, we'll just be temporarily disabling this security feature.

```python
@csrf_exempt
```

### Signup 

> Now that we can use cookies to create sessions, the next thing we want to do is register a user, and log them in to start a session. However, before we create a user, we need to know how to properly handle user passwords. It's important that our users' passwords aren't stored in plain text, just in case a hacker gets access to our database. Django has a lot of built-in functionality that helps us manage users and their passwords, but today we're going to implement sign-up/login manually, so we can understand the process better. The general process of registering a user is:
- user sends a POST request to the server with their email/username and password
- server generates a random `salt`, which is appended to the user's password
- the salt+password are hashed
- a user object is created, and stored in the database. salt+hash are stored in the database in a single column
- (optional) start a session for the user if they don't have one, or associate their existing session with their new account. 

> We'll need to save users in our database, so let's create a model for them in `models.py`.

```python
from django.db import models

class AppUser(models.Model):
    user_name = models.CharField(max_length=30, unique=True)
    password = models.CharField(max_length=99)
```



### Hashing

> It's dangerous to store plain-text passwords in our database. We want to limit how much sensitive data we are responsible for, in case a hacker gets access to our database. It's important to `hash` a password first, and only store the hash in the database. Remember, hashing is different from encryption. Anything that can be ENcrypted can be DEcrypted, if you have the encryption keys, because encryption is a 2-way process. However, once a value is hashed, there is no straightforward way to reverse that hash, and recover the original value. 

> There are a couple features that are important in a hashing algorithm:
- a hashing algorithm should be fast enough that a user can register/log-in relatively quickly, but slow enough that a hacker can't hash a billion passwords in an hour.
- a hashing algorithm should produce hashes that look similar for all inputs, so a hacker can't get clues about the password based on the hash

> Today we'll be using the built-in python module `hashlib`, which contains a variety of hashing algorithms. The exact hashing algorithm that we use today might not actually be the perfect choice for a web application, but it should be good enough to demonstrate the concepts we're learning about today. 


### Salting

> If we hash our passwords, a potential hacker would need to guess passwords by hashing them first, which is a little slow. However, a clever hacker might have a list of precomputed hashes for common passwords, called a rainbow table, which saves them the time of hashing on the fly. Also, they might notice that many users have identical hashes, which means they can crack multiple accounts at once, and those users are probably all using a very common, insecure password. 

> The solution to the above problems is to add a `salt` to the password. A salt is a random string that is added to the users' password before it is hashed. Then, the salt+hash is stored in a single column in the database. This guarantees that every user has a unique hash, and none of the hashes can be found in a rainbow table. Salting your passwords doesn't actually make it much harder for a determined hacker to crack a single, secure password. It mostly prevents opportunistic attacks, and increases the amount of time/effort required to crack many weaker passwords.  It just adds another roadblock to potential hackers. 

> Next, let's create a route and a view to handle sign-up requests.

```python
@csrf_exempt
def sign_up(request):
    # the body is a JSON formatted string. let's convert it to a dictionary
    body = json.loads(request.body)
    raw_password = body['password']

    # a simple helper function that generates a random string
    salt = generate_salt()

    # convert our string to bytes, hash the bytes, then display the hash as hexadecimal
    salted_hashed_password = hashlib.sha256((salt + raw_password).encode()).hexdigest()

    # the salted hash is saved in the database, NOT the plain-text password.
    new_user = AppUser(user_name=body['username'], password=f"{salt}${salted_hashed_password}")
    new_user.save()

    # tell the client their request was successful
    return JsonResponse({
        'success':True
    })
```

> We'll need to write some javascript code to send requests to this route.

```javascript
axios.post('/sign-up', {
    username: 'jeffbezos',
    password: 'dragons',
}).then((response)=>{
    console.log('response', response)
})
```

### Login

> Now that we've registered a user, we need to be able to log them in. The general process for this is:
- user sends a POST request with their username and password
- server looks up the user with that username from the database
- the server takes the salt from the user's `password` field, concatenates it to the password in the POST request body, and hashes them together
- the hash generated in the previous step is compared with the hash from the user's `password` field in the database.
- if the hashes match, then the password is correct, and we can log the user in. Set a cookie to start a session. 
- if the hashes don't match, send a 401 error to let the client know what went wrong. 

```python
@csrf_exempt
def log_in(request):
    # convert the body from a JSON string to a python dictionary
    body = json.loads(request.body)

    # look up the user from the database that this client is claiming to be
    user = AppUser.objects.get(user_name=body['username'])

    # split the 'password' field in the db into its components: the salt and the hash
    split_password = user.password.split('$')
    salt = split_password[0]
    hashed_password = split_password[1]

    # use the salt from the db to hash the password in the request body
    challenge_hash = hashlib.sha256((salt + body['password']).encode()).hexdigest()

    # if the hashes match, set a cookie to start a session
    if challenge_hash == hashed_password:
        response = JsonResponse({ 'success':True })
        response.set_cookie('user_id', user.id)
        return response
```


## Assignments
- [Django view-counter](https://github.com/sierraplatoon/django-view-counter)


## Preview Tutorials: Django Authentication & Login
If you want, do these tutorials for a preview of tomorrow's lesson and to go deeper into Django's built-in tools for handling user accounts, login, and authentication.
- [WS Vincent Authentication Tutorial](https://wsvincent.com/django-user-authentication-tutorial-login-and-logout/)
- [WS Vincent Password Reset Tutorial](https://wsvincent.com/django-user-authentication-tutorial-password-reset/)



