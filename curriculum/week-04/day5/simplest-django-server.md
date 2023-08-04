# Simplest Django Server


## Topics Covered / Goals
- Learn to use virtual environments in python
- manage packages with pip
- create a new django project
- manage your application with `python manage.py`
- create simple route handlers 
- Understand how servers can receive data from clients (method type, headers, query string, url params, request body )
- Understand how servers send data back to clients (response body, headers, status code)

## Lesson

Django has a great tutorial, but it can be hard to understand all the pieces of it if you've never worked with servers before. 
In this demo, we'll build the simplest possible server with django that can accept requests from a browser and return HTML.

### Virtual Environments

Developers have a variety of projects they are working on at any given time, especially if you work for a consultancy. Given the nature of working on greenfield (i.e., brand new) projects and old projects at the same time, versions of software will be all over the place. Your greenfield project may use Python 3 and your client's older code base may use Python 2. How do you protect your computer against getting confused on which versions of software to use? How do you ensure that you have a clean environment to develop from each time? Enter *virtual environments*.

Virtual environments are containers that wrap around your project to ensure that whatever you install inside your virtual environment stays there / doesn't affect the rest of your computer. Think of it as a placemat at the dining table for babies. Those babies can drool and drop all the food they can on that placemat, but it doesn't affect the rest of your dining table. At the end of the meal, you can throw that placemat away if you wish.

> More specifically, when you set up python on your machine, it was installed to a particular location (check its location with `type python3`), and you also declared a location for that installation of python to store python modules that you install. Lastly, a variable in your system called the `PATH` was adjusted so that when you simply type `python` at the command line, it refers to that version of python you installed, wherever it was installed to. 

> When you create a virtual environment, you're installing another copy of python, with its own set of installed modules that are separate from other python modules you've installed previously. The virtual environment also includes an activation script, which adjusts your `PATH` so that when you type `python`, it refers to this new version of python instead. The activation script also defines a function called `deactivate()` that resets your `PATH` back to how it was before. As a convenience, the virtual environment also changes your shell prompt to include the name of the virtual environment, to help you avoid accidentally installing modules into the wrong virtual environment. 

Let's create a virtual environment whenever we work with Django so that it doesn't mess up the rest of our machine setups. The 3 commands that you need to remember:

```bash
# 1. Create your virtual environment
python -m venv <envname>

# 2. Start your virtual environment. Running the script with 'source' applies it to our current shell, instead of a subshell
# Mac:
source <envname>/bin/activate

# Windows:
<envname>/Source/activate

# 3. Eject from your virtual environment (once you're done - this is run at the very end of development)
deactivate
```

### Installing Django
> Up until now, you may have seen a few built-in python modules used, both in python scripts (re, random, math) and at the command line (http, venv). Now, we're going to use `pip` to install django, a new python module, that we will use both from the command line, and in our scripts. 

```bash
pip install django
```

### Starting a New Django Project

> Now that we've installed Django in our project, let's invoke it at the command line and see what it does. 

```bash
python -m django
```

> You should see a menu of options. You'll learn to use several of these options, but we won't need most of them. Don't worry about the warning about django-settings. We'll fix that in a second. Since we don't have a django project yet, let's start by creating one. Let's imagine we're creating an application for a school, so we'll name our project `school`. 


```bash
python -m django startproject school
```

> When we start our project, django will create a folder with the name of the project. Inside of this project folder, django will create a subfolder for the main module of the project, which has the same name.  Tomorrow, you'll learn about proper project organization and make other modules, but today we'll just be working with the main module. 

> In addition to creating our main module, starting our django project created a file called `manage.py`. Running this file is almost the same as running `python -m django`, except using the manage.py file also loads some settings specific to this project.

> Before we forget, it's important that we record all of the dependencies used in this project, starting with Django. We can do this by using the command `pip freeze`, and redirecting the output to a file.

```bash
pip freeze > requirements.txt
```

> Now when we push this project to github, if another developer pulls it down, they won't receive Django with it. However, they can use the requirements.txt file to install all of the modules used for this project with `pip install -r requirements.txt`.

### Start the dev server
> Django comes with its own built-in webserver. We can't use this to deploy our application, but it'll be useful while we develop on our local machines, because it automatically restarts every time we edit a python file. 

```bash
python manage.py runserver
```

> After running this command, your terminal will tell you what port the app is running on, so you can visit it in your browser. At first, you'll just see a generic welcome message, because we haven't defined any routes. So let's define some routes.  

### Handling requests in urls.py
> Routes for our application can be defined in urls.py. There's only one thing in here by default, a route handler for `/admin`. Since our server is running, we can actually check out the admin login page in our browser, but it won't be very useful to us yet, since we don't have a database set up. 

> In a well organized django project, this file would not have any actual routes defined. Instead, it would only contain references to other files which contain route definitions, like how the table of contents of a book contains only chapter titles and page numbers. To keep this demo as simple as possible, we'll just define a route handler for our homepage right in this file.

> In some ways, handling HTTP requests to our back end is similar to handling user events on the front end. On the front end, we define an event handler by choosing the type of event, like `click` or `keypress`, and writing a function that receives the event object and reacts to the user's action. To handle an HTTP request, we write a function that handles a certain type of HTTP request (GET in today's demo), and takes in a request object. One big difference is that front end event handlers do not need to return a value, whereas server side route handlers must always return a value for the response that is sent to the client. If you don't return anything, the client will be waiting until the request times out. 
```python
from django.http import HttpResponse

def rootRouteHandler(request):
    response = HttpResponse("<h1>Welcome to the internet</h1>") # every request must receive one response
    print('=-=-=-=-=-=-=-=')
    print(dir(request))
    print('--------------')
    print(dir(response))
    print('=-=-=-=-=-=-=-=')
    return response


def anotherRouteHandler(request):
    response = HttpResponse("<marquee>We can make as many routes as we want</marquee>")
    response.status_code = 418

    # django automatically puts query string variables into a dictionary
    print(request.GET.get('format'))

    return response 

def withParams(request, format='h1'):
    print(format)
    return HttpResponse(f"<{format}>Hello Django</{format}")

urlpatterns = [
    path('admin/', admin.site.urls), # default admin routes, provided for free by django
    path('', rootRouteHandler),
    path('another-route/', anotherRouteHandler),
    path('another-route/<str:format>', withParams),

]
```

- requests have a method, route, headers, and maybe a body
- responses have headers, a body, and a status code

## Assignments
- [Hello Django](https://github.com/sierraplatoon/hello-django)
- [Simple To-do](https://github.com/sierraplatoon/html-simple-to-do)  

