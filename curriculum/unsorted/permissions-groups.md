# Permissions and Groups


## Topics Covered / Goals
- Authentication vs Authorization
- groups and permissions

## Lesson

> Yesterday's lesson was focused mostly on authentication, which is how a user proves to the server that they are who they say they are.

> Today, we'll be learning about authorization. Once a user has been authenticated, how do we determine what they're allowed to do? 

> The primary way that we can limit who can do what, is by using django's built-in permission system. Before we talk about the permissions themselves, let's create a model for something that some users can have permission over. I want to build a blog, so we'll have models for users, posts and comments. Editors should be able to delete posts, and moderators should be able to delete comments. Other users should not be able to delete anything. We also need to tell django about our custom user model.

```python
AUTH_USER_MODEL = 'blog.AppUser'
```

```python
from django.db import models
from django.contrib.auth.models import (AbstractUser)

class AppUser(AbstractUser):
    email = models.EmailField(
        verbose_name='email address',
        max_length=255,
        unique=True,
    )


    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = [] # Email & Password are required by default.

class Comment(models.Model):
    comment_text = models.CharField(max_length=255)

class BlogPost(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField()
```

> Now that we've created a couple of models, let's make migrations, run them, and then check out our database.

```bash
python manage.py makemigrations
python manage.py migrate
```

```sql
SELECT * FROM auth_permission;

 id |          name           | content_type_id |      codename      
----+-------------------------+-----------------+--------------------
  1 | Can add log entry       |               1 | add_logentry
  2 | Can change log entry    |               1 | change_logentry
  3 | Can delete log entry    |               1 | delete_logentry
  4 | Can view log entry      |               1 | view_logentry
  5 | Can add permission      |               2 | add_permission
  6 | Can change permission   |               2 | change_permission
  7 | Can delete permission   |               2 | delete_permission
  8 | Can view permission     |               2 | view_permission

...

 21 | Can add user            |               6 | add_appuser
 22 | Can change user         |               6 | change_appuser
 23 | Can delete user         |               6 | delete_appuser
 24 | Can view user           |               6 | view_appuser
 25 | Can add comment         |               7 | add_comment
 26 | Can change comment      |               7 | change_comment
 27 | Can delete comment      |               7 | delete_comment
 28 | Can view comment        |               7 | view_comment
 29 | Can add blog post       |               8 | add_blogpost
 30 | Can change blog post    |               8 | change_blogpost
 31 | Can delete blog post    |               8 | delete_blogpost
 32 | Can view blog post      |               8 | view_blogpost
```

> Notice that for every model in the database, there are 4 permission records, for add, view, change, and delete (CRUD). This applies to built-in models, and also ones that we define. Each permission record has both a human-readable name, and a codename that we'll use to refer to this permission from our code. Let's give our moderators permission to delete comments. 

```python
from django.contrib.auth.models import Group, Permission

# django provides the Permission model to us. We interact with it similarly to other models.
perm = Permission.objects.get(codename="delete_comment")
user.user_permissions.add(perm)
```

> From our route handlers, we can check if a user has permission to do something they're trying to do:

```python
    # check for user permissions. the format is: '<app name>.<action>_<model>' 
    if request.user.has_perm('blog.delete_comment'):
        pass

```

> Sometimes, it's more convenient to use a decorator to require a permission, instead of writing your own code to check for it.

```python

# you can redirect the client with a 302 status (good for regular pages)
@permission_required('blog.delete_comment', login_url="/login")

# or, you can rais a permission denied error with a 403 status (good for AJAX requests)
@permission_required('blog.delete_comment', raise_exception=True)
```

> Now we know how to give users permissions, and to check for those permissions before certain actions, but it's not very efficient to manage permissions on every individual user. More commonly, instead of assigning permissions directly to users, permissions are assigned to groups, and then users are added to groups.  

> Django provides a built-in `auth_groups` table, but it's empty. Let's create a group that we can add users to, add permissions to the group, and then add a user to that group.

```python
# get_or_create returns two values, so we declare two variables
# first is the group, second is True/False if a new group was created.
moderators_group, created = Group.objects.get_or_create(name='moderators')
perm = Permission.objects.get(codename="delete_comment")
moderators_group.permissions.add(perm)
user.groups.add(moderators_group)
```



> We can check for group membership (or check for almost anything on the user) with the `@user_passes_test` decorator.

```python
def is_moderator(user):
    return user.groups.filter(name='moderators').exists()

from django.contrib.auth.decorators import user_passes_test

@user_passes_test(is_moderator)
def delete_comment(request):
    pass
```

> Using groups in this way basically replaces our previous use of individual user permissions with a more efficient process. There's another way to use groups, to group multiple users together who belong to the same organization, so that users can only modify resources that belong to their organization, and so they can benefit from premium features their organization has purchased. This is someimes referred to as a 'multi-tenant application'. 

> Note that a single user can be in multiple groups. For example, a user might belong to a group for moderators that allows them to delete comments, and they might also belong to a group for an organization that limits them from accessing data that belongs to other organizations. With both of these groups together, they would be able to delete comments only if the comments are owned by the user's organization. 
 
## External Resources
- [Django Authentication](https://docs.djangoproject.com/en/4.0/topics/auth/default/)

## Assignments


