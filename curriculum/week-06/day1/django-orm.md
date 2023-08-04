# Django-ORM


## Topics Covered / Goals
- Be able to create a new Django project within your virtual enviornment
- Understand how to create a database and models
- Know how to communicate with your database using Python and Django ORM

## Lesson


**Connecting Django to Postgresql using Django ORM**

> Recently, we learned how to query postgreSQL, which stores data in relational tables. Before that, we learned about Django servers, which store much of their data in python objects. It can be tricky to write a python application that queries a SQL database, since these are different ways of storing data. To help us translate between these formats, developers often use an Object-Relational Mapping library (ORM). Fortunately, Django includes a very useful ORM, creatively called Django-ORM. 

> When using Django-ORM, we won't write raw SQL anymore. Instead, we'll define Classes, called `models`, from which Django-ORM can automatically create tables in postgres. Instances of these classes will be used to represent individual rows of our tables. Django-ORM provides other useful features too, such as helping us manage changes to our database schema, known as `migrations`.


**Starting a New Django Project**

> Once we have our venv up and running, we can install Django and start a new project. We are going to call our project `school_project`, but you can call it whatever you want.

```bash
$ pip install django
$ python -m django startproject school_project
$ cd school_project
```

**Create a database**

> Today we're learning on how the Python classes we write will connect to our database using Django ORM. We'll be creating a `school` database with one table: `students`.

```bash
$ createdb school
```

**Pyscopg2**

> Now we install psycopg2, the Python library that will help Django talk to Postgres. We won't actually be calling this library directly, it's just a depency of Django-ORM that we need to install.

```bash
$ pip install psycopg2
```

> And then tell Django we want to use Postgres as our database instead of the default, SQLite3. We also tell it what database to attach to

```python
## school/settings.py
## our settings.py file is in the school directory, but if you named your app a different name then it look for that folder name.

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'school',
    }
}
```

**Create our App**

> Django projects are split into many apps (i.e., a project has many apps). Imagine a new _project_ at Amazon where they are selling lots of space on the Moon. That _project_ requires a bunch of different _apps_ in order to run. For example, there might be a billing _app_ to collect money from individuals, a searching _app_ for people to look up lots, a VIP _app_ where they target VIPs, etc. Today, our `school` project will just start with an `attendance_app` app.

```bash
$ python manage.py startapp attendance_app
```

> Next, we need to add the `attendance_app` app to our `settings.py` file in our school folder.

```python
## school/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'attendance_app',
]
```

**Creating Our Model**

> Now we can create our `student` model:

```python
## attendance/models.py
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=200)
    email = models.CharField(max_length=200)
```

> We've created a Python class that directly maps to a database table (i.e., a model). Next, let's tell Django to create the necessary code for us to get this table into the database:

```bash
$ python manage.py makemigrations attendance
```

> A folder was just generated called `migrations`. Look inside there and take a look at the Django code that was generated for us to put our tables into the database. If we were not using an ORM, we would have to write these migrations ourselves, by hand, so let's take a moment to appreciate all the time and effort that Django-ORM is saving us. Next, let's `migrate` our database, so that it reflects the current state of our models.  

```bash
$ python manage.py migrate
```

> We should have a `attendance_app_student` students table in our db. Check it out with `psql school`

**Django Console**

> While we can interact with our data using Postgres, more often we want to interact with our data using Python. We're going to use a console for our project that will pull in all our Python classes and allow us to query the database directly using Django's ORM.

```bash
python manage.py shell
from school.models import Student
```

> The shell will allow us to load in our models from Django. Once in the shell, we can create a new student.

```bash
In [1]: student = Student(name="Jon", email="jon@jon.com")
In [2]: student.save()
```

> This should look familiar - the object oriented lessons from weeks 2 and 3 were in preparation for this. Just by writing those two lines of code, we are able to instantiate a Student object for us and save it into the database. Under the hood, it's just running:

```sql
INSERT into students (name, email) VALUES ('Jon', 'jon@jon.com')
```

> Now we can query our database using Python and see our new record.

```bash
In [3]: Student.objects.all()
## SELECT * from students;
```
> You should get back a query object. Exit the shell by typing `exit`. Let's confirm that our new record got saved in our Postgres db.

```bash
$ psql school
psql (11.1, server 9.6.3)
Type "help" for help.

school= \d
                          List of relations
 Schema |               Name                |   Type   |     Owner
--------+-----------------------------------+----------+---------------
 public | attendance_app_student            | table    | joshuaalletto
 public | attendance_app_student_id_seq     | sequence | joshuaalletto
 public | auth_group                        | table    | joshuaalletto
 ...

```
> Django creates our database tables with the app name first, followed by the app name. Before we even wrote our own models, there were many built-in tables here, named after the built-in apps in Django, such as `admin`, `auth`, and `sessions`. Also, take note of the `django-migrations` table, which django manages automatically in order to keep track of which migrations have already been run. Our app's table is `attendance_app_student`. Let's query for the records in that table and see what we get.

```bash
## select * from attendance_app_student;
school=
 id | name | email
----+------+-----+
  1 | Jon | jon@jon.com
(1 row)
```


**Communicating with the Front-end**

> So far, we've interacted with our database using PSQL or the django shell. For a real application, queries to the database will be handled in our views, initiated by user requests. There are many ways that this might work, but for our first project using Django-ORM, let's simply display all of our students on the home page, and then create a form that allows the user to add more students. 

```python
def index(request):

    students = Student.objects.all() # get list of all students

    # the home page should show a roster of all the students, so we pass the whole list into the template
    return render(request, 'attendance_app/index.html', {'students': students} )
```

> Then we'll need a template that can render this data.

```html
<h1>Class Roster</h1>

<ul>
    {% for student in students %}
    <li>{{student.name}} - {{student.email}}</li> 
    {% endfor %}
</ul>
```

> We'll also need a route that allows us to add new students.

```python
@csrf_exempt
# this function creates a cartItem / updates a cartItem quantity
def add_student(request):
    body = json.loads(request.body) 

    new_student = Student(name = body['name'], email = body['email'])
    new_student.save()

    return  JsonResponse({'success':True})
```

> Finally, we'll build a form that users can use to specify a new student's name and email.

```js
function add_student(event)  {
    event.preventDefault() 
    // 'name' is already globally defined in js, so I'm using 'username' here just to be extra safe.
    const username = document.getElementById('username').value
    const email    = document.getElementById('email').value

    axios({
        method: 'POST',
        url: '/student/',
        data: {
            // when our django server receives this data, it will be called 'name' instead of 'username'
            name: username,
            email: email,
        }
    }).then(function (response){
        console.log(response.data)
    })
}
```



## External Resources
- [Django Docs](https://docs.djangoproject.com/en/2.2/)
- [Django Models Intro Docs](https://docs.djangoproject.com/en/2.2/topics/db/models/)
- [Django Queries Cheat Sheet](https://github.com/chrisdl/Django-QuerySet-Cheatsheet)
- [Django Validators Resource](https://docs.djangoproject.com/en/2.2/ref/validators/)
- [Database Diagramer](https://www.quickdatabasediagrams.com/)

## Assignments
- [Django Queries](https://github.com/sierraplatoon/django-queries)
