# Django Associations and Validators


## Topics Covered / Goals
- Know how to write Django Validators
- Know how to create Django Associations (relationships between models)

## Lesson

**Connecting Django to Postgresql using Django ORM**

##### 1. Create your virtual environment
`python -m venv <envname>`

##### 2. Start your virtual environment
`source <envname>/bin/activate`

##### 3. Eject from your virtual environment (once you're done - this is run at the very end of development)
`deactivate`

**Starting a New Django Project**

Once we have our venv up and running, we can install Django and start a new project. We are going to call our project name `twitter_project`, but you can call it whatever you want.

```bash
$ pip install django
$ django-admin startproject <projectname>
$ cd <projectname>
```

**Create a database**

Today we're learning on how the Python classes we write will connect to our database using Django ORM. We'll be creating a `twitter` database with one table: `user`.

```bash
$ createdb twitter
```

**Pyscopg2**

Now we install the Python library that will help Django talk to Postgres.

```bash
$ pip install psycopg2
```

And then tell Django we want to use Postgres as our database instead of the default SQL adapter, SQLite3. We also tell it what database to attach to

```python
## twitter_project/settings.py
## our settings.py file is in the twitter_project directory, but if you named your app a different name then it look for that folder name.

DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql',
		'NAME': 'twitter',
	}
}
```

**Create our App**

Django projects are split into many apps (i.e., a project has many apps). Imagine a new _project_ at Amazon where they are selling lots of space on the Moon. That _project_ requires a bunch of different _apps_ in order to run. For example, there might be a billing _app_ to collect money from individuals, a searching _app_ for people to look up lots, a VIP _app_ where they target VIPs, etc. Today, our `twitter_project` project will just start with a `twitter_app` app.

```bash
$ python manage.py startapp twitter_app
```

Next, we need to add the `twitter_app` app to our `settings.py` file in our school folder.
```python
## school/settings.py

INSTALLED_APPS = [
	'django.contrib.admin',
	'django.contrib.auth',
	'django.contrib.contenttypes',
	'django.contrib.sessions',
	'django.contrib.messages',
	'django.contrib.staticfiles',
	'twitter_app',
]
```

**Creating Our Model**

Now we can create our `user` model:

```python
## attendance/models.py
from django.db import models

class User(models.Model):
	first_name = models.CharField(max_length=255)
	last_name = models.CharField(max_length=255)
	email = models.EmailField(max_length=100)
	age = models.IntegerField()
	account_type = models.CharField(max_length=4) # 'paid' or 'free'
```

We've created a Python class that directly correlates to a database table (i.e., a model). Next, let's tell Django to create the necessary code for us to get this table into the database:

```bash
$ python manage.py makemigrations twitter_app
```

A folder was just generated called `migrations`. Look inside there and take a look at the Django code that was generated for us to put our tables into the database. Next, let's `migrate` our data from the app into the database itself:

```bash
$ python manage.py migrate
```

We should have a `twitter_app_user` students table in our db. Check it out with `psql twitter`

**Database Validators**

Our User model has an `age` field but with our twitter app, we only want to allow users who are 13 years old or older to signup. However, our `age` field allows for any age to get entered and saved into the database. So as a developer maybe when a user signs up we have a select drop down that only has ages 13 and above to choose from, will that prevent someone signing up who is under the age of 13? Nope, what if a hacker somehow hacks our form and enters an age less than 13? That's why we need validators to validate all the data right before the data gets saved into our database. It's the last line of defense before the data gets saved.

We can create a **validator** which is a method to check if the user is 13 years of age or older.

```py
from django.core.exceptions import ValidationError # import ValidationError from Django
from django.utils.translation import gettext_lazy as text # import gettext_lazy

## Create minimum age validator
def validate_age(age):
	if age < 13:
		raise ValidationError(text(f"You need to be 13 years of age or older. You currently are {age} years old. Get older!"))

class User(models.Model):
	first_name = models.CharField(max_length=255)
	last_name = models.CharField(max_length=255)
	email = models.EmailField(max_length=100)
	age = models.IntegerField(validators=[validate_age]) # add this code
	account_type = models.CharField(max_length=4)
```

Luckily Django has very common validators built-in to the Django Framework. If we want to validate a minimum (or maximum) integer we can use the built-in validator `MinValueValidator()`. Let's replace our `validate_age` validator with the built-in ``MinValueValidator()`.

```py
from django.core.exceptions import ValidationError # import ValidationError from Django
from django.utils.translation import gettext_lazy as text # import gettext_lazy
from django.core.validators import * # import built-in Django Validators

class User(models.Model):
	first_name = models.CharField(max_length=255)
	last_name = models.CharField(max_length=255)
	email = models.EmailField(max_length=100)
	age = models.IntegerField(validators=[MinValueValidator(13)]) # replaced with MinValueValidator(13)
	account_type = models.CharField(max_length=4)
```

Also, what if we only want user's to have a `paid` or `free` account and we only want the string `'paid'` or `'free'` to be saved into the database. We can create a Django Validator to check to see if they enter `paid` or `free`.

```py
from django.db import models
from django.core.exceptions import ValidationError # import ValidationError from Django
from django.utils.translation import gettext_lazy as text # import gettext_lazy

## Add this validator to validate the account type field
def validate_account_type(account_type):
  	valid_account_types = ['free', 'paid']
  	if account_type not in valid_account_types:
		raise ValidationError(text(f"{account_type} is not valid. Please select a valid account type {valid_account_types}"))

class User(models.Model):
	first_name = models.CharField(max_length=255)
	last_name = models.CharField(max_length=255)
	email = models.EmailField(max_length=100)
	age = models.IntegerField(validators=[MinValueValidator(13)])
	account_type = models.CharField(max_length=4, validators=[validate_account_type])
```

**Testing**

In our `twitter_app` directory, inside the `tests.py` file we can write our unit tests.

```py
from django.test import TestCase
from django.core.exceptions import ValidationError
from .models import User # import User model

## Create your tests here.
class AppUserTest(TestCase):

  	def test_01_create_user(self):
		new_user = User(first_name='Tom', last_name='Prete', email='tom@email.com', age=12, account_type='free')
		try:
			new_user.full_clean()
			self.fail() # should fail if it reaches here
		except ValidationError as e:
			self.assertFalse('user cannot be under the age of 13' in e.message_dict['age'])

	def test_02_create_user(self):
		new_user = User(first_name='Tom', last_name='Prete', email='tom@email.com', age=13, account_type='free')
		try:
			new_user.full_clean()
			self.fail()
		except ValidationError as e:
			self.assertTrue('user cannot be under the age of 13' in e.message_dict['age'])

  	def test_03_account_type(self):
		new_user = User(first_name='Tom', last_name='Prete', email='tom@email.com', age=15, account_type='free')
		try:
			new_user.full_clean()
			self.fail()
		except ValidationError as e:
			self.assertTrue('Please enter a valid account type' in e.message_dict['account_type'])
```

To run your tests execute the command `python manage.py test` in the terminal.

## Django Associations

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

The models.ForeignKey() field takes in the model you want to reference, the deletion behavior, and the alias field we want *on the the side* of the relationship.
The `related_name` field is optional... if we do not provide a value for it, Django provides some default naming for the field.

Creating a foreign key field relationship here does the same thing that we would expect for in a database: We reference another model and there must be a matching entry in the corresponding model's table. Additional, using models.ForeignKey() essentially creates a new field on the other side of the relationship, for convenience:

```python

c = Course.objects.get(pk=3)
print(c.professor) # accessing the professor field on the course object

p = Professor.objects.get(pk=c.professor.id)
print(p.courses.all()) # accessing ALL of the courses that this professor teaches (the relate_name value is used in this manner)
```

## Another Example using Validator and Associations

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

## External Resources
- [Django Docs](https://docs.djangoproject.com/en/2.2/)
- [Django Queries Cheat Sheet](https://github.com/chrisdl/Django-QuerySet-Cheatsheet)
- [Django Validators Resource](https://docs.djangoproject.com/en/2.2/ref/validators/)

## Assignments
- [Django Validations](https://github.com/sierraplatoon/django-validations)
- [Django Associations](https://github.com/sierraplatoon/django-associations)


