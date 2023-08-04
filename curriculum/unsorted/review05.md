# Review Day

Today we're going to review the Django topics from the week. We'll aim to go over:
- Setting up a Django Project
- Setting up view handlers/routing
- Creating templates / base templates
- Working with forms to create new data
- Working with APIs

## Creating new data

We're going to be extending our [Django School Roster](https://github.com/quebecplatoon/django-school-roster) challenge, to incorporate the ability to add a new student to our class roster. Our initial challenge only dealt with reading in data. Now we'll try to create new data with our application.

We need to create a new page that uses an HTML form to capture user input:

```html
<!-- pages/student_new.html -->

{% extends 'base/html' %}

{% block main-content %}
  <h2>Add Student</h2>
  <form method="POST">
    {% csrf_token %} <!-- don't forget your csrf token here inside your form! -->
    <label for="name">Student Name</label>
    <input type="text" name="name" placeholder="name" />
    <br />
    <label for="age">Age</label>
    <input type="text" name="age" placeholder="age" />
    <br />
    <label for="id">School Id</label>
    <input type="text" name="id" placeholder="#id#" />
    <br />
    <label for="password">Password</label>
    <input type="password" name="password" placeholder="password" />
    <br />
    <button type="submit">Add Student</button>
  </form>
{% endblock %}
```

Then we need to process the form (POST request to the same view), create our new data, and redirect to a proper page. 

```python
# views.py

from django.shortcuts import render, redirect, reverse

# ...

def student_new(request):
    ## POST request
    if request.method == "POST":
        # extract the fields that we want from that post data
        form_data = {
          "name": request.POST["name"],
          "age": request.POST["age"],
          "school_id": request.POST["id"],
          "password": request.POST["password"],
          "role": "Student"
        }
        
        # create new internal data object
        new_student = Student(**form_data)
        
        # add to internal data collection and write out new data to csv
        my_school.add_student(new_student) 
        
        # redirect to a new page
        return redirect(reverse('student_detail', args=(new_student.school_id,)))
        
    ## GET request
    return render(request, "pages/student_new.html")

```

## Assignments
- Reading Assignment: [Databases/SQL](https://learn.coderslang.com/0118-introduction-to-relational-databases-and-sql/)
- [Install Postgres](https://github.com/sierraplatoon/install-postgres)
