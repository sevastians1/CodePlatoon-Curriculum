# Django Auth

## Topics Covered / Goals
- The user model
- sign up, log in, log out
- accessing the user from the request object

## Lesson
> Yesterday, we learned how to manually sign up and log in users. This was a good learning experience, but it took a lot of effort, and probably wasn't perfectly secure. Today, we'll be learning about some of the tools that django provides that will help us conveniently, securely manage our users. 

### Database setup
> We'll be using postgres for our app today. Let's declare our database in our `settings.py`, and then run the migrations.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'django_auth',
    }
}
```

```bash
python manage.py migrate
```


### The User Model
> Yesterday, we manually created our own model for users, called `AppUser`. Fortunately, django provides a built-in `User` model that provides much of the functionality that we built yesterday. Unfortunately, the default fields on the user model are very generic, and probably don't meet all of our application's needs. We'll need to extend the built-in `User` model to accommodate custom attributes for our application. There are a few options for doing this:
- One option is to use the built-in user model with all of the default settings, and then create another model, called a 'profile model', that has a OneToOne relationship with the `User` model. 
    ```python
    from django.contrib.auth.models import User

    class Employee(models.Model):
        user = models.OneToOneField(User, on_delete=models.CASCADE)
        department = models.CharField(max_length=100)
    ```
- Another option is to create a subclass of django's built-in `AbstractBaseUser` class. This lets us use django's authentication features, but requires us to define all the fields our user needs.
- The third option is to create a subclass of django's built-in `AbstractUser` class. This is a full, functional user model, that we can also extend with custom properties. We'll use this option today to get a balance between convenience and flexibility.

    ```python
    from django.db import models
    from django.contrib.auth.models import (AbstractUser)

    # Inheriting from 'AbstractUser' lets us use all the fields of the default User,
    # and overwrite the fields we need to change
    # This is different from 'AbstractBaseUser', which only gets the password management features from the default User,
    # and needs the developer to define other relevant fields.
    class AppUser(AbstractUser):
        email = models.EmailField(
            verbose_name='email address',
            max_length=255,
            unique=True,
        )
        is_active = models.BooleanField(default=True)

        # notice the absence of a "Password field", that is built in.

        # django uses the 'username' to identify users by default, but many modern applications use 'email' instead
        USERNAME_FIELD = 'email'
        REQUIRED_FIELDS = [] # Email & Password are required by default.
    ```

> Since we're not using the default, built-in User model, we have to tell django which model we're using for our users in the settings.py file.

```python
AUTH_USER_MODEL = 'blog.AppUser'
```

### Sign up, Log in, Log out
> Now that we've defined our user model by extending `AbstractUser`, we can define views that leverage django's built-in authentication functionality. First, let's define some URLs for the views we'll create.

```python
from django.urls import path 
from . import views


urlpatterns = [
    path('signup', views.sign_up),
    path('login', views.log_in),
    path('logout', views.log_out),
    path('whoami', views.who_am_i),
]
```

> Now let's create a view for our sign-up route

```python
from django.views.decorators.csrf import csrf_exempt

from django.contrib.auth import authenticate, login, logout
from .models import AppUser as User


@csrf_exempt
def sign_up(request):
    try:
        body = json.loads(request.body)
        User.objects.create_user(username=body['username'], password=body['password'], email=body['username'])
    except Exception as e:
        print('oops!')
        print(str(e))
    return HttpResponse('hi')
```

> And we'll add a little javascript on the front-end to send the sign-up request.

```javascript
axios.post('/sign-up', {
    username: 'jeffbezos',
    password: 'dragons',
}).then((response)=>{
    console.log('response', response)
})
```

> Django handles all the password hashing in one line of code, which is very convenient. If we want to see what django just created for us, let's look in the database.
> Notice that their plain-text password is not included in their record. The password field contains their password hash, which also contains the hashing algorithm and the salt, separated by `$`. Some of the other fields are empty, because they exist by default, but we never set them. Some of the fields will be automatically set by django, however. The fields `date_joined` and `last_login` are updated automatically, like you'd expect. The field `is_active` defaults to True. If you need to delete a user, you should set this to False instead of actually deleting the user. Modern applications rarely permanently delete data, but instead mark items as 'deleted' so that they can be ignored by other queries. Especially for users, it's important to never delete their database record, in case they want to reactivate their account, or if they had connections to other users. 

```sql
full_stack_app=# select * from blog_appuser\gx
-[ RECORD 1 ]+-----------------------------------------------------------------------------------------
id           | 1
password     | pbkdf2_sha256$320000$vL9X7Gu4p2DMqgXkS02N55$ekvH4pqodQhbgbW55mCFHHLd1jr/p+mKqw3NOcO6bdQ=
last_login   | 
is_superuser | f
username     | jeffbezos
first_name   | 
last_name    | 
is_staff     | f
date_joined  | 2022-03-16 11:28:07.292569-05
email        | jeffbezos
is_active    | t
```

> If we need to log in an existing user, django provides some functions, `authenticate` and `login`, that help us do that. 

```python
@csrf_exempt
def log_in(request):
    body = json.loads(request.body)
    email = body['email']
    password = body['password']
    
    # remember, we told django that our email field is serving as the 'username' 
    # this doesn't start a login session, it just tells us which user from the db belongs to these credentials
    user = authenticate(username=email, password=password)
    if user is not None:
        if user.is_active:
            try:
                login(request, user)
            except Exception as e:
                print('oops!')
                print(str(e))
            return HttpResponse('success!')
            # Redirect to a success page.
        else:
            return HttpResponse('not active!')
            # Return a 'disabled account' error message
    else:
        return HttpResponse('no user!')
        # Return an 'invalid login' error message.
```

> Logging out is even simpler.

```python
@csrf_exempt
def log_out(request):
    logout(request)
    return HttpResponse('Logged you out!')
```

> If a logged in user sends a request to the server, you can access information about the user at `request.user`. This is much more convenient than manually looking them up from the database.

```python
@csrf_exempt
def who_am_i(request):
    print(dir(request.user))
    if request.user.is_authenticated:
        return JsonRespnse({
            'email': request.user.email
        })
    else:
        return JsonResponse({'user':None})
```

## External Resources
- [django auth docs](https://docs.djangoproject.com/en/4.0/topics/auth/default/)

## Assignments
- Add sign up, login, and log out functionality to these exercises:
  - [To-do List](https://github.com/sierraplatoon/django-to-do)
  - [Event Calendar](https://github.com/sierraplatoon/django-event-calendar)

