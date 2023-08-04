# Backend APIs

## Topics Covered / Goals
- Using APIs from the backend
    - Many APIs cannot be used easily like we did in week 4 (front end -> api -> front end), for security reasons
    - Send requests from front end -> back end -> api > back end -> front end

## Lesson

> One reason why we might need to use an API from the back end is that many APIs require users to be authenticated to use the API, so you need to send the request from the back end, where you can keep your credentials secret.
    - secret management
        - Never put keys or other secrets in github!
        - use environment variables to supply credentials to your app

> Another reason is because some APIs are inaccessible from the front end, due to the Same Origin Policy (SOP) and lack of Cross Origin Resource Sharing (CORS). This is intended to be a security feature, but it can sometimes be frustrating to work around. 

> by default, when you send an AJAX request, browsers only will use responses from servers in the same origin

> example origin, including protocol (https), subdomain, and tld: https://developer.mozilla.org/

> a server that is expecting to receive cross-origin requests can set headers on the response (`access-control-allow-origin: *`), meaning that any client is allowed to use the response

> The process for acquiring and using an API key may be different for different APIs, so be sure to read the documentation. The API Key is a unique key associated with your developer account for billing/usage purposes. You will often have public and/or private API keys - the public key can be used on the front end, since it's not sensitive or private. The private key must be stored securely on the server (using env variables, not in a git repo). If you are getting charged money for using an API, make sure you protect your private key. If anyone gets a hold of your private key, they can impersonate you and you can get charged boatloads of money. Public keys are public - don't worry about protecting these.

> Today, we'll be using the noun project API, but every API is different. [Read the documentation](http://api.thenounproject.com/getting_started.html) in order to know how you're supposed to authenticate and send requests.

> After reading the docs, I want to get an API key. After creating an account, we need to create an 'app', and then we can create a set of keys associated with that app. Before we write any code, we can test our keys in the [api explorer](https://api.thenounproject.com/explorer)

> Now that we have some idea how the API works, let's build a django project that uses it. The first new thing we'll need for this project is a library that will help us send requests, called `requests`. It's similar to axios, except it's used on the back end with python.

```bash
pip install requests
pip install requests_oauthlib
```

```python
import requests
from requests_oauthlib import OAuth1

# public key and private key
auth = OAuth1("*******************", "**********************")
endpoint = "http://api.thenounproject.com/icon/1"

response = requests.get(endpoint, auth=auth)
responseJSON = response.json()
```



> There's a lot of content here! In the browser, it was easier to dig into large data structures, but it's not quite as easy to read large data structures in python due to how they print in the terminal. I'm going to use a built-in python module, pprint (pretty print) that'll help me read the responses from the API. 

```python
import pprint

# only go 2 levels deep, so we get a general idea of the response without having to look at the whole thing
pp = pprint.PrettyPrinter(indent=2, depth=2)

response = requests.get(endpoint, auth=auth)
responseJSON = response.json()
pp.pprint(responseJSON)
```

> Now that we can get a response from the API, let's use that data in a template that we send to the client. 

> There's one last problem we need to solve before I commit this in github. Currently, my private key is visible in the code, so if I pushed it up to github, other people might steal my credentials. We need to use environment variables, which are not committed in git, to supply credentials to our app. We could use actual env variables in BASH, but it's common practice to use a .env file, which looks a little like this:

```bash
# my .env file
env=prod
apikey=******
secretkey=*****
```

 To help us read it, we'll use a python package called `python-dotenv`.

```bash
pip install python-dotenv
```

```python
from dotenv import load_dotenv
import os

load_dotenv()  # take environment variables from .env.
print(os.environ['apikey'])

```

## Assignments
- [Django Pokemon Theme Team](https://github.com/sierraplatoon/django-pokemon-theme-team)
- [API Show and Tell](https://github.com/sierraplatoon/API-show-and-tell)
