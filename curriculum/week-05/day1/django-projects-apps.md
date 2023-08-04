# Django Projects and Apps

## Topics Covered / Goals
- For the reminder of this course, and possibly in future projects, you will be using Django to handle the backend (and sometimes the frontend) of any web apps you build.
- Respond to clients by rendering templates instead of sending raw HTML
- Learn how to handle static files using Django, to add CSS, JS, and images

## Lesson

> Today, we're going to get started with using Django as a full stack framework, handling both the front and back end of our application. Later, we'll learn how to run Django just as an API (backend) layer and use it in conjunction with React on the frontend.

### Start Our Project

> This tutorial is largely based off of [the official docs](https://docs.djangoproject.com/en/3.1/intro/tutorial01/). A few things have been removed to account for our current level of knowledge. Notably, the app we build today won't have a proper database. 

> First things first for any new Django project, we need a new virtual environment to keep our dependencies isolated to this project and the versions we build it with.

> **NOTE**: This will become a repetive workflow for setting up a new project.


1. Create a Python Virtual Environment: `python -m venv ~/venvs/polls_env`
2. Activate your `venv`:
  On Mac/Linux 
  > `source ~/venvs/polls_env/bin/activate`

  On Windows 
  > `~\venvs\polls_venv\Scripts\activate`

3. Install `django` into your environment: `pip install django`
4. Create a Django project called `polls_project`: `python -m django startproject polls_project `

> We've got the beginnings of our Django app. This creates a bunch of files in our project directory, but we didn't talk about most of them yesterday. Let's take a moment to check them out and try to understand them better.  Here's the breakdown of each file with explanations from the Django documentation and our explanation from us on how we interpret the Django documentation:

- `manage.py`
  - *Docs say*: A command-line utility that lets you interact with this Django project in various ways
  - *In our words*: It's code that allows you to use your terminal to interact with your app. This includes running migrations, interacting with the console, and starting the server. 
- `mysite/__init__.py`
  - *Docs say*: An empty file that tells Python that this directory should be considered a Python package
  - *In our words*: It's a file with dunder (double underscores) in the filename that Python needs in order to run this properly
- `mysite/settings.py`
  - *Docs say*: Settings/configuration for this Django project
  - *In our words*: This is the file that will tell Django things like which database to use, what apps are installed, etc.
- `mysite/urls.py`
  - *Docs say*: The URL declarations for this Django project; a "table of contents" of your Django-powered site. You can read more about URLs in URL dispatcher.
  - *In our words*: This is the file where you declare (write) all your routes. Think of this as the phone operator of an organization. You call the operator and tell them what you want. Then, the operator directs to you to where you need to go
- `mysite/wsgi.py`
  - *Docs say*: An entry-point for WSGI-compatible web servers to serve your project
  - *In our words*: It's what we need to fire the app up on different types of servers. You won't have to edit this file. 

*Let's fire up the server:* `python manage.py runserver`. Next, visit http://localhost:8000 and see what you get!

*Don't worry about any unapplied migrations yet. We're not using our database just yet.*

### Projects and Apps
> Django projects are split into many apps (i.e., a project has many apps). Imagine a new _project_ at Amazon where they are selling lots of space on the Moon. That _project_ requires a bunch of different _apps_ in order to run. For example, there might be a billing _app_ to collect money from individuals, a searching _app_ for people to look up lots, a VIP _app_ where they target VIPs, etc. Today, our project will just start with a `polls_app` app.

```bash
$ python manage.py startapp polls_app
```

> A quick sidebar - we ran `startproject` earlier and we are now running `startapp`. The difference between these two is that a `project` consists of many `apps`. An `app` can belong to many `projects`.

Next, we need to add the `polls_app` app to our `settings.py` file.
```python
## mysite/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls_app',
]
```

In `polls_app/views.py`, let's put the following code inside:
```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

We created a request method called `index` inside the `views` file. Next, we need to register this page in `polls_app/urls.py` *(you need to create this file)*. Create that file and paste the following code in there:

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index),
]
```

Finally, we will connect our recently created `urls.py` to `polls_project/urls.py`. *Delete the code that is already in the `urls.py` file and replace it with the code below*:

```python
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls_app.urls')),
]
```

Whoa, a lot of code just now - let's break it down. We started off earlier with creating a project called `polls_project`. Projects can consist of many apps. We then created a `polls_app` app which is at the same level as `polls_project`. Whenever anyone hits any route (aka endpoint) with `/polls`, the `polls_project/urls.py` file will direct them to the `polls_app` app, specifically the `urls` file. From there, it'll send the user to `index` method in `views.py`.

In `polls_app/urls.py`, let's break down the `path` method that we imported. `path` takes in 4 arguments. 2 of them are required and 2 of them are optional. In order, they are `route`, `view`, and `kwargs` / `name`. `route` is the path that you enter in the URL. `view` is the file that handles the logic behind what shows up on your screen. `views.index` means "Look at the index method in the `views.py` file."

Visit http://localhost:8000/polls to see what you get!

### Additional Views

We're going to add 3 new routes:
- `/polls/:question_id` (view a particular question)
- `/polls/:question_id/results` (view the results of that particular question)
- `/polls/:question_id/vote` (vote on the choices on that question)

In `polls_app/urls.py`, let's register these routes and their corresponding methods:
```python
from django.urls import path
from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote),
]
```

Next, create the following methods in `polls_app/views.py`:
```python
from django.http import HttpResponse
from django.shortcuts import render

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

def detail(request, question_id):
    return HttpResponse(f"You're looking at question {question_id}.")

def results(request, question_id):
    return HttpResponse(f"You're looking at the results of question {question_id}.")

def vote(request, question_id):
    return HttpResponse(f"You're voting on question {question_id}.")
```
Visit those routes with `question_id` as 1 and see what you get!

### Data

We haven't learned about databases yet, so for today's demo we'll simulate having a database by using a list of dictionaries. Add the following data to `views.py`:

```python
latest_question_list = [
    {
        'id' : 1,
        'question_text': 'whats up',
        'pub_date':'2022-01-04',
        'choices': [
            {
                'id':1,
                'choice_text':'not much',
                'votes': 0,
            },
            {
                'id':2,
                'choice_text':'the sky',
                'votes': 0,
            },
        ]
    },
    {
        'id' : 2,
        'question_text': 'whats new',
        'pub_date':'2022-02-09',
        'choices': [
            {
                'id':1,
                'choice_text':'not much',
                'votes': 0,
            },
            {
                'id':2,
                'choice_text':'the sky',
                'votes': 0,
            },
        ]
    },
]
```

### Server-side rendering 
> Most web applications need to render dynamic content. The web wouldn't be very interesting if every page was the same for every visitor, every time they visited. Generally, when a user sends a request to a web application, the application needs to get necessary data from various sources such as APIs or a database, and then insert that data into a template, which gets rendered into HTML. However, there is an important decision to be made about when and where HTML gets rendered.
- One option, which we'll be exploring today, is server-side rendering.
    - This is the most traditional and simplest way to make a dynamic webpage, since you don't need a front-end framework like React.
    - Server-side rendering is generally faster for the initial page load, since the client only has to download the initial HTML document before they can start seeing content. It might still take some time to load scripts and below-the-fold images, but at least the user can read your content as soon as the page loads. First impressions are important. Subsequent page loads may be a little faster, if the user has cached files that are used on all pages, but the page still needs to unload before new content can be rendered.
- Another option, which we'll be exploring later with React, is client-side rendering. 
- You don't have to choose one or the other! An application can use server-side and client-side rendering on different pages. For example, you might choose server-side rendering for parts of your application that don't require a login, like the login/signup pages, home page, about, contact, etc. Then, once a user logs in, all content that requires authentication is delivered in a SPA. 
- Some applications perform both server-side rendering and client-side rendering at the same time. This combines the benefits of a fast initial load with fast page transitions, as well as interactive front-end content. The downside is that this is the most complex way to render content. However, there are frameworks that can make this easier, if this is what you need to do (e.g. Next.js, Nuxt.js)


We want to create templates with Django's templating system, where we can pass in Python objects of data to be rendered into HTML. Under your `polls_app` directory, create a `templates` folder, and under that directory, create another `polls_app` folder, and under that directory create an `index.html` file:

```html
<!-- polls/templates/polls/index.html -->
<h1> All Polls </h1>
{% if latest_question_list %}
  <ul>
    {% for question in latest_question_list %}
      <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
  </ul>
{% else %}
  <p>No polls are available.</p>
{% endif %}
```

Django templates have their own syntax, and it lets us insert Python into our HTML. Here are three of the basic uses:

```html
{% code blocks %}
{{ variable interpolation }}
{# comments #}
```

Next, we'll update the def `index` view in `polls_app/views.py` to send a dictionary to our new template. The method `render()` requires a dictionary and is used to send `latest_question_list` to be displayed by our markup at `polls_app/index.html`:

```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    data = { 'latest_question_list': latest_question_list }
    return render(request, 'polls/index.html', data)

def detail(request, question_id):
    return HttpResponse(f"You're looking at question {question_id}.")

def results(request, question_id):
    return HttpResponse(f"You're looking at the results of question {question_id}.")

def vote(request, question_id):
    return HttpResponse(f"You're voting on question {question_id}.")
```


If you were to visit http://localhost:8000/polls, you'd see all of the questions we've written thus far. Let's move onto the detail function in our `polls_app/views.py` file which will be `/polls/1` page:

```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    data = { 'latest_question_list': latest_question_list }
    return render(request, 'polls/index.html', data)

def detail(request, question_id):
    question = latest_question_list[question_id-1]
    data = { 'question': question }
    return render(request, 'polls_app/detail.html', data)

def results(request, question_id):
    return HttpResponse(f"You're looking at the results of question {question_id}.")

def vote(request, question_id):
    return HttpResponse(f"You're voting on question {question_id}.")
```

In our **`polls_app/templates/polls_app`** directory create a file called `detail.html`:

```html
<h1> Details about Question {{ question.id }} </h1>
<h2> {{ question.question_text }} </h2>
<ul>
  {% for choice in question.choices %}
    <li>{{ choice.choice_text }}</li>
  {% endfor %}
</ul>
```


**Forms in Django**

Let's create a form so that people can vote on a question. In `polls/templates/polls/detail.html`, put the following code:

```html
<h1> Details about Question {{ question.id }} </h1>
<h2> {{ question.question_text }} </h2>

<form action="/polls/{{ question.id }}/vote/" method="POST">
  {% csrf_token %}
  {% for choice in question.choices %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
  {% endfor %}
  <input type="submit" value="Vote">
</form>
```
There are a few things to note with this form:
1. We are explicitly declaring that the method for our form is `POST` because we are _sending_ information
2. We're creating a bunch of radio button options in one loop to account for each `choice` for our `question`. Each radio button has an unique `id` using `forloop.counter` (comes for free with Python)
3. The `<label>` has a `for` attribute that refers to the `id` of the `<input>`. This lets a user select a radio button by clicking on its associated label. This is very important for accessibility, especially on mobile devices. 

If you refresh your page (`/polls/1`), you'll see your choices come on the screen. Submit it and you'll be directed to a page that simply says "You are voting on question 1". That's because we haven't accounted for the `POST` request in our code yet. If you look in your `urls.py`, you'll see that we defined the route but the action in `views.py` isn't really doing anything yet. We need to fix that up!

```python
## polls/views.py
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import render


def index(request):
    data = { 'latest_question_list': latest_question_list }
    return render(request, 'polls_app/index.html', data)

def detail(request, question_id):
    question = latest_question_list[question_id-1]
    return render(request, 'polls_app/detail.html', {'question': question})

def results(request, question_id):
    return HttpResponse(f"You're looking at the results of question {question_id}.")

def vote(request, question_id):
    question = latest_question_list[question_id-1]
    selected_choice = question['choices'][int(request.POST['choice'])-1]
    selected_choice['votes'] += 1
    
    # redirect the user to a GET route, so they don't resubmit their vote if they refresh the page
    return HttpResponseRedirect(f'/polls/{question_id}/results')
```


When we vote, we get redirected to the results page which just has some text in it. Let's alter that:
```python
## polls/views.py
def results(request, question_id):
    question = latest_question_list[question_id-1]
    return render(request, 'polls_app/results.html', {'question': question})
```
`results` is telling us to create a `results.html` file in our `polls_app/templates/polls_app` directory:
```html
<h1> Details about Question {{ question.id }} </h1>
<h2> {{ question.question_text }} </h2>

<ul>
  {% for choice in question.choices %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
  {% endfor %}
</ul>

<a href="/polls/{{ question.id }}/">Vote again?</a>
```



### Serving Static Files
> Our app is functional, but not very pretty. In order to style our site with CSS, we need to configure django to serve static files.

> Create a folder called `static` in the root of the project, right next to the `manage.py`. Inside of that, create a folder called `css`, and create a `main.css` inside of there.

> In your `settings.py`, tell django what URL prefix clients should use to request static files, and also specify where in your server the static files are located.

```python
STATIC_URL = 'static/'

STATICFILES_DIRS = [
    BASE_DIR / "static",
]
```

```html
<html>
  <head>
    <title>Polls</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
    <link rel="stylesheet" href="/static/css/main.css">
  </head>
```

### Intermediate Templating

#### Includes
> `include` lets us insert one template file into another. The included file would be only a part of an HTML file, called a partial. The file it is included into may or may not be a complete HTML file. This is useful if you have a component that is repeated in many different pages, or in different places in the same page. Including a file can also be useful if the included file is very large or complex, so that you don't have to look at the whole thing when you're reading the template that contains it. 

```html
<!-- subscribe.html -->
<button type="button" onclick="alert('thanks for subscribing!')">Please Subscribe!</button>
```

```html
<div>
	{% include 'blog/subscribe.html' %}
</div>
```

#### Extends
> Sometimes you'll have elements that are repeated on every page, in the same location. These repeated elements together can be considered your 'layout'. In the layout, we define places where dynamic content will be inserted, called 'blocks'. We can define parts of an HTML page with matching 'blocks' and insert them into the layout using `extends`. This is useful for navbars and footers.

```html
<!-- layout.html -->
<html>
    <head>
        {% block title %}
        <title>Welcome to my blog!</title>
        {% endblock %}
        <link rel="stylesheet" href="/static/css/main.css">
    </head>
    <body>
        <h1>Polls</h1>
        {% block content %}{% endblock %}
    </body>
</html>
```

```html
{% extends "layout.html" %}

{% block title %}
<title>Polls</title>
{% endblock %}

{% block content %}
<h1> All Polls </h1>
{% if latest_question_list %}
  <ul>
    {% for question in latest_question_list %}
      <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
  </ul>
{% else %}
  <p>No polls are available.</p>
{% endif %}
{% endblock %}
```


## Assignments
- [Django School Roster](https://github.com/sierraplatoon/django-school-roster)


