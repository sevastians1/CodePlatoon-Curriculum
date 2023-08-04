# Django Associations and Validators

## Topics Covered / Goals

- Know how to write Django Validators
- Know how to create Django Associations (relationships between models)
- Know how to test your models and create instances for testing

## Lesson

**Connecting Django to Postgresql using Django ORM**

##### 1. Create your virtual environment

`python -m venv <envname>`

##### 2. Start your virtual environment

`source <envname>/bin/activate`

##### 3. Eject from your virtual environment (once you're done - this is run at the very end of development)

`deactivate`

**Starting a New Django Project**

Once we have our venv up and running, we can install Django and start a new project. We are going to call our project name `school_project`, but you can call it whatever you want.

```bash
$ pip install django
$ django-admin startproject <projectname>
$ cd <projectname>
```

**Create a database**

Today we're learning on how the Python classes we write will connect to our database using Django ORM. We'll be creating a `school_db` database with a few tables.

```bash
$ createdb school_db
```

**Pyscopg2**

Now we install the Python library that will help Django talk to Postgres.

```bash
$ pip install psycopg2
```

And then tell Django we want to use Postgres as our database instead of the default SQL adapter, SQLite3. We also tell it what database to attach to

```python
## school_project/settings.py
## our settings.py file is in the school_project directory, but if you named your app a different name then it look for that folder name.

DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql',
		'NAME': 'school_db',
	}
}
```

**Create our App**

Django projects are split into many apps (i.e., a project has many apps). Imagine a new _project_ at Amazon where they are selling lots of space on the Moon. That _project_ requires a bunch of different _apps_ in order to run. For example, there might be a billing _app_ to collect money from individuals, a searching _app_ for people to look up lots, a VIP _app_ where they target VIPs, etc. Today, our `school_project` project will just start with a `school_app` app.

```bash
$ python manage.py startapp twitter_app
```

Next, we need to add the `school_app` app to our `settings.py` file in our school folder.

```python
## school_project/settings.py

INSTALLED_APPS = [
	'django.contrib.admin',
	'django.contrib.auth',
	'django.contrib.contenttypes',
	'django.contrib.sessions',
	'django.contrib.messages',
	'django.contrib.staticfiles',
	'school_app',
]
```

**General steps for creating database models**
- create models
- add validators
- makemigrations
- migrate
- test models

## Creating Our Models

```python
# models.py

from django.core.validators import MinValueValidator, MaxValueValidator, RegexValidator
from django.db import models
from .validators import *

class Locker(models.Model):
	locker_number = models.IntegerField(unique=True)
	combination = models.CharField(max_length=10, validators=[validate_locker_combination])

	def __str__(self):
		return f"LOCKER: {self.locker_number}"

class Student(models.Model):
	first_name = models.CharField(max_length=32)
	last_name = models.CharField(max_length=32)
	birth_date = models.DateField()
	locker = models.OneToOneField(Locker, null=True, blank=True, on_delete=models.CASCADE, related_name="student")

	def __str__(self):
		return f"STUDENT: {self.first_name} {self.last_name}"

class Professor(models.Model):
	first_name = models.CharField(max_length=32)
	last_name = models.CharField(max_length=32)
	start_date = models.DateField()

	def __str__(self):
		return f"PROFESSOR: {self.first_name} {self.last_name}"

class Course(models.Model):
	name = models.CharField(max_length=64)
	description = models.TextField()
	credits = models.IntegerField(validators=[MinValueValidator(1), MaxValueValidator(6)])
	professor = models.ForeignKey(Professor, on_delete=models.CASCADE, related_name="courses")
	students = models.ManyToManyField(Student, through="Enrollment")

	def __str__(self):
		return f"COURSE: {self.name}"

class Enrollment(models.Model):
	student = models.ForeignKey(Student, on_delete=models.CASCADE, related_name="enrollments")
	course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="enrollments")
	grade = models.CharField(max_length=2, validators=[RegexValidator(regex=r"[ABCDF][+-]?")])

	class Meta:
		unique_together = (("student", "course"))

	def __str__(self):
		return f"ENR: {self.student} in {self.course}"
```

### Django Associations

As with database schema design, we can create relationships between our Django models to reflect certain requirements. Django provides some model fields to make achived this task much simpler:

- models.OneToOneField()
- models.ForiengKeyField()
- models.ManyToManyField()

Let's take a look at how we could create a foreign key relationship:

```python
class Professor(models.Model):
	# ...other fields
	pass

class Course(models.Model):
	# ...other fields
	professor = models.ForeignKey(Professor, on_delete=models.CASCADE, related_name="courses")
```

The models.ForeignKey() field takes in the model you want to reference, the deletion behavior, and the alias field we want _on the the side_ of the relationship.
The `related_name` field is optional... if we do not provide a value for it, Django provides some default naming for the field.

Creating a foreign key field relationship here does the same thing that we would expect for in a database: We reference another model and there must be a matching entry in the corresponding model's table. Additional, using models.ForeignKey() essentially creates a new field on the other side of the relationship, for convenience:

```python

c = Course.objects.get(pk=3)
print(c.professor) # accessing the professor field on the course object

p = Professor.objects.get(pk=c.professor.id)
print(p.courses.all()) # accessing ALL of the courses that this professor teaches (the relate_name value is used in this manner)
```


## Adding Validators

Django has very common validators built-in to the Django Framework. [Built in validators](https://docs.djangoproject.com/en/4.1/ref/validators/#built-in-validators). 

For example, if we want to validate a minimum (or maximum) integer we can use the built-in validator `MinValueValidator()`. 

```py
## models.py
from django.core import validators as v # import built-in Django Validators

class User(models.Model):
	first_name = models.CharField(max_length=255)
	last_name = models.CharField(max_length=255)
	email = models.EmailField(max_length=100)
	age = models.IntegerField(validators=[v.MinValueValidator(13)]) 
	account_type = models.CharField(max_length=4)
```


>Validators will run when we run `model_instance.full_clean()`
>[object validation docs](https://docs.djangoproject.com/en/4.1/ref/models/instances/#validating-objects)

We can also create our own validators...

Our Locker model has a `combination` field but with our school app, we only want to allow combinations with the a format like `'10-20-30' `. However, our `combination` field allows for any combination to get entered and saved into the database.

We can create a **validator** which is a method to check for a valid locker combination. 

Let's create a new file `validators.py` inside our `school_app` folder

```python
# validator.py

from django.core.exceptions import ValidationError
import re

def is_locker_digit_valid(digit):
	num = int(digit)
	return num >= 0 and num < 60

def validate_locker_combination(combination):
	pattern = r"(\d{1,2})-(\d{1,2})-(\d{1,2})"
	match = re.search(pattern, combination)
	error = ""

	if not match:
		error = "Invalid combination format!!!"

	elif not is_locker_digit_valid(match.group(1)) or \
		not is_locker_digit_valid(match.group(2)) or \
		not is_locker_digit_valid(match.group(3)):
		error = "Invalid combination digit!!!"

	if error:
		raise ValidationError(
			error,
			params={'combination': combination},
		)

	return combination
```

## Making Migrations
We've created Python classes that directly correlates to a database table (i.e., a model). Next, let's tell Django to create the necessary code for us to get this table into the database:

```bash
$ python manage.py makemigrations
```

## Migrating Changes

A folder was just generated called `migrations`. Look inside there and take a look at the Django code that was generated for us to put our tables into the database. Next, let's `migrate` our data from the app into the database itself:

```bash
$ python manage.py migrate
```

We should have tables named `school_app_<model_name>` in our db for each one of our models. Check it out with `psql school_db`



## Testing Our Models

- **Using Tests**

	In our `school_app` directory, inside the `tests.py` file we can write our unit tests.

```py
from django.test import TestCase
from django.core.exceptions import ValidationError
from .models import Locker # import User model

# Create your tests here.
class school_tests(TestCase):

    def test_01_create_locker_incorrect(self):
        new_locker = Locker(locker_number='102', combination='12-433')

        try:
            new_locker.full_clean()
            self.fail()

        except ValidationError as e:
            self.assertFalse('Invalid combination format!!!' in e.message_dict['combination'])

```

To run your tests execute the command `python manage.py test` in the terminal.

- **Using the shell**

You can create any python file with queries to the database and run it in the shell provided by django

Let's say we created a file named `test_data.py` inside of our `school_app` folder, we could run that file against the shell using 

```sh
python manage.py shell < school_app/test_data.py
```

- **Using the admin portal**

In your `school_app/admin.py` file you can register your models in the admin site to be able to manage your models and data.

```python
# school_app/admin.py
from django.contrib import admin
from .models import *

# Register your models here.
admin.site.register([Locker, Student, Professor, Course, Enrollment])
```

Then you can log into the admin site `http://localhost:8000/admin` with credentials `admin`, `admin` you should be able to see your model tables

## External Resources

- [Django Docs](https://docs.djangoproject.com/en/2.2/)
- [Django Queries Cheat Sheet](https://github.com/chrisdl/Django-QuerySet-Cheatsheet)
- [Django Validators Resource](https://docs.djangoproject.com/en/2.2/ref/validators/)

## Assignments

- [Django Validations](https://github.com/sierraplatoon/django-validations)
- [Django Associations](https://github.com/sierraplatoon/django-associations)
