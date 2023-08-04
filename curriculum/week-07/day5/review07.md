# Review Day

## Topics Covered / Goals
- Review Day


## Django overview
Think of django more as a tool to help you build a full website than a concept. Conceptually, you are creating a website that has a user experience, a data model, and logic that allows interaction between the two.

In django,

- Templates deal with what the user sees in the browser,
- Models define how the data we are going to use in our site should be stored,
- Controller Methods in `views.py` are the methods that can:
  1. receive requests (http requests) from browser
  2. create, read, update, and delete data through our model class
  3. do some logic and stuff
  4. send responses back to the sender, render a page, or do a page redirect
- Routing via `urls.py` deals with defining what method in the views will handle what url route

In **templates** you can write html and css that will be displayed to a user. Django gives you some extra functionality that allows to simple logic like for loops, if statements, and variables.

In **models** you create class objects that are tied to tables in a database. you set attributes (column names) and give them types (ex: char field, integer field)

You can make 3 types of relationships between 2 models:

 A---|---B

one-to-one: add an attribute of type models.OneToOneField  on one of the models (doesn’t matter which one) A or B

one-to-many: add an attribute of type  models.ForeignKey  on model B (the one that there will be many of)

many-to-many: add an attribute models.ManyToManyField attribute on either model (A or B)

For each of these relationships, you can set a related name attribute that you can use on the other side of the connection (that name will be used by the model you did not add the attribute to
Ex.
```py
class Question(models.Model):
    text = models.CharField(max_length=100)

class Answer(models.Model):
    text = models.CharField(max_length=100)   
    question = models.OneToOneField(Question, on_delete=models.CASCADE, related_name="answer")
```
`Class Question`   now has an attribute called answer because of that last line

In **views** you import the models, access the data through them, do what that function needed to do (grab data, change data, call an api, etc..) and return a response.
