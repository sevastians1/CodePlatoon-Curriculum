# Model Forms and Serializers

## Topics Covered / Goals
- Using Django as an API
- Using Serializers to convert data into a transmittable format
- Using Model Forms (high level only)

## Lesson


### Using Django As An API

Luckily, we already have most of the Django skills that we need in order set up an API application. The key change to our approach this time however will be returning JSON formatted data via an HTTP Response, instead of simply rending some front-end HTML templates inside our Django project. Our front-end will consume the data provided by our Django back-end server.


### Starting Django Project

We will focus on creating a Django project that will act as a back-end API end-point. There's a lot of steps we need to follow, but most of what we'll be today should be familiar to us already. 

#### Initial Set-Up
```sh
$> python -m venv .venv
$> source .venv/bin/activate

$> pip install django psycopg2 
$> pip freeze > requirements.txt

$> echo ".venv/" > .gitignore

$> django-admin startproject netflix_proj .
$> python manage.py startapp netflix_app

$> createdb netflix_db
```

#### Settings

**settings.py**
```python
# netflix_proj/settings.py

INSTALLED_APPS = [
    # ...
    "netflix_app", # add our Django app name
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql', # specify database server as "postgresql"
        'NAME': "netflix_db", # add our database name
    }
}
```

#### Models

**models.py**
```python
# netflix_app/models.py

class Category(models.Model):
    type = models.CharField(max_length=64)

    def __str__(self):
        return f"CATEGORY: {self.type}"

class Genre(models.Model):
    type = models.CharField(max_length=64)
    tagline = models.CharField(max_length=255)

    def __str__(self):
        return f"GENRE: {self.type}"

class Product(models.Model):
    title = models.CharField(max_length=64, unique=True)
    description = models.CharField(max_length=255)
    category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name="products")
    genres = models.ManyToManyField(Genre, related_name="products")
    image_url = models.URLField(blank=True, null=True)
    year_released = models.DateField(validators=[MaxValueValidator(limit_value=date.today)])
    is_original = models.BooleanField(default=False, verbose_name="Netflix original")

    def get_average_rating(self):
        product_reviews = Review.objects.filter(product=self)
        if product_reviews.count() == 0:
            return None

        avg_rating_dict = product_reviews.aggregate(models.Avg("rating"))
        return round(list(avg_rating_dict.values())[0], 2)

    def __str__(self):
        return f"PRODUCT: {self.title}"

class Review(models.Model):
    class Rating(models.IntegerChoices):
        AWFUL = 1
        DECENT = 2
        AVERAGE = 3
        GOOD = 4
        AMAZING = 5
   
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name="reviews")
    rating = models.IntegerField(choices=Rating.choices)
    username = models.CharField(max_length=64)
    comment = models.TextField(blank=True, null=True)

    def __str__(self):
        return f"REVIEW: {self.product.title} [{self.rating}]: {self.comment} --{self.username}"
```

Make sure we review all of the above code for `models.py`! All of the code should be familiar to us at this point. Once we're happy with our models, let's make sure we don't forget to `makemigrations` and `migrate` our changes into our database:

```sh
$> python manage.py makemigrations
$> python manage.py migrate
```

### Serializers

The first new item that we'll cover today is the idea of serializers. The idea of serializing is simply taking data from within an application and packaging it up in a transmittable format. For our purposes, we'll rely on using the trusty [JSON format](https://www.w3schools.com/whatis/whatis_json.asp). Today we'll be creating serializers manually in our Django project, just so we understand the process of serializing data.

One thing worth mentioning is that Django does have built-in serializers that can be used for serializing data. 

```python
# built-in serializer example
from django.core import serializers

data = serializers.serialize("json", SomeModel.objects.all())
```

While we theoretically could rely on using the built-in serializer module capabilities for creating our API end-point, this module is not really meant for that. Django uses the built-in serialization module to create an *internal* data representation of our data (for example, when we want to convert our data into fixture files using the `dumpdata` Django command). The serialization module isn't really meant to be used for *external* data representation. Additionally, the built-in serialization isn't easily customizable in cases where we may want to serialize our data a bit differently. 

Python does provide a `json` module that can be used to convert *basic* Python data types into a JSON format, however our models are far from "basic" data types that can be automatically converted into JSON, so we'll have to do this work manually.

With all that said, let's create some serializers for our models:

**serializers.py (new file)**
```python
# netflix_app/serializers.py

## base serializer

class SerializerBase:    
    def serialize_all(self, item_list):
        serialized_data = []
        for item in item_list:
            serialized_data.append(self.serialize(item))
        return serialized_data


## Category serializers

class CategorySerializer(SerializerBase):
    def serialize(self, category):
        return {
            "id": category.id,
            "type": category.type,
            "products": self.serialize_products(category.products.all())
        }

    @staticmethod
    def serialize_products(products): 
        return [ p.id for p in products ]

class CategoryNestedSerializer(CategorySerializer):
    @staticmethod
    def serialize_products(products): # this method is overriding the method in CategorySerializer
        return ProductSerializer().serialize_all(products)


## Genre serializers

class GenreSerializer(SerializerBase):
    def serialize(self, genre):
        return {
            "id": genre.id,
            "type": genre.type,
            "tagline": genre.tagline,
            "products": self.serializer_products(genre.products.all())
        }

    @staticmethod
    def serialize_products(products):
        return [ p.id for p in products ]

class GenreNestedSerializer(GenreSerializer):
    @staticmethod
    def serialize_products(products):
        return ProductSerializer().serialize_all(products)


## Product serializers

class ProductSerializer(SerializerBase):
    def serialize(self, product):
        return {
            "id": product.id,
            "title": product.title,
            "description": product.description,
            "category": self.serialize_category(product.category),
            "genres": self.serialize_genres(product.genres.all()),
            "image_url": product.image_url,
            "year_released": product.year_released,
            "is_original": product.is_original,
            "average_rating": product.get_average_rating(),
            "reviews": self.serialize_reviews(product.reviews.all())
        }

    @staticmethod
    def serialize_category(category):
        return category.id

    @staticmethod
    def serialize_genres(genres):
        return [ g.id for g in genres ]

    @staticmethod
    def serialize_reviews(reviews):
        return [ r.id for r in reviews ]

class ProductNestedSerializer(ProductSerializer):
    @staticmethod
    def serialize_category(category):
        return CategorySerializer().serialize(category)

    @staticmethod
    def serialize_genres(genres):
        return GenreSerializer().serialize_all(genres)

    @staticmethod
    def serialize_reviews(reviews):
        return ReviewSerializer().serialize_all(reviews)


## Review serializers

class ReviewSerializer(SerializerBase):
    def serialize(self, review):
        return {
            "id": review.id,
            "product": self.serialize_product(review.product),
            "rating": review.rating,
            "username": review.username,
            "comment": review.comment or "This user did not provide a comment."
        }

    @staticmethod
    def serialize_product(product):
        return product.id

class ReviewNestedSerializer(ReviewSerializer):
    @staticmethod
    def serialize_product(product):
        return ProductSerializer().serialize(product)
```

Let's make sure we take some time to review the code above for our newly added `serializers.py`. While it is a fair amount of code, it's really not too complex. Our process is pretty straight-forward: Take data that's represented in our Python application and convert it into a JSON format. Our end goal will be to send this serialized data out in a JSON response.

We created a base class `SerializerBase` to reduce how much code we needed to write, since all bulk serialization operations follow the same logic. We need to create four derived serializer classes because each will serialize individual data records in different ways depending on if we're working with a `Category`, `Genre`, `Product`, or `Review` model object.

We also have a two versions of our each of our serializer classes: A basic version and "nested" version. The basic serializers will simply serialize any nested objects as id's, while the nested versions will fully serialize nested objects using their own serializer. This makes our code a bit more complicated, however we need to use this design if we want to achieve **bi-directional nested serialized data**. Without it, we'll get stuck in an infinite nested loop of one serialization class calling another serialization class, back and forth, over and over again. A couple things to note about this design: 
- This design is not needed if we don't care about nesting serialized data at all, or if we only want our nested serialized data to act in "one direction" (i.e. we guarantee that a serializer won't call another serializer that will call back into the original serializer). 
- This design only allows for one nested level of serialization. We could employ more complicated design to get the full depth of nested serialization, but the added code complexity is not worth the added feature for us right now.

If you're still confused about the use of "nested" serializers, the key takeaway is that it allows for more detailed serialized data while preventing an infinite loop for our logic.

Notice that we are free to represent that data however we want in our serialized data. We are also able to add new fields to the serialized data, if we choose to do so. In the `serializer.py` code above, we added a new field in the serialized data of `Product`, "average_rating", which isn't apart of our Product model data. We also chose to fill in a default comment value (*"This user didn't provide a comment."*) if no comment was provided by the user, for the serialized data of `Review` (...We could have just allowed the front-end to do this for us, but whatever.)

It is a bit tedious to have to create our Models, and then also create Serializers that are basically some mapped versions of our Models. We may learn of a faster way to accomplish this serialization functionality in the future!

#### Using Serializers with Views

Now that we've created our serializers, we can proceed to create our view handlers which will employ our serializers to package up our data and send it out to the requesting destination. This part of our job is a bit easier this time around than what we did with Django in the past, because we don't have to worry about creating HTML templates and rendering them. All we need to do is send out our packaged data (using `JsonResponse`). Let's take a look at what our view handler code might look like for just CRUD-ing `Product` data:

**views.py**
```python
# netflix_app/views.py

import json
from django.views.decorators.csrf import csrf_exempt
from django.http import JsonResponse
from .models import *
from .serializers import *

## error helper methods

def error_on_request(error):
    return JsonResponse(data={"error": error}, status=400)

def bad_request():
    return error_on_request("Bad Request.")


## Product view handlers

def product_list(request):
    try: 
        if request.method == "GET":
            products = Product.objects.all()
            serialized_data = ProductNestedSerializer().serialize_all(products)
            return JsonResponse(serialized_data, status=200, safe=False) # must set safe to 'False' if transmitting a list/array
    
    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()

@csrf_exempt
def product_create(request):
    try: 
        if request.method == "POST":
            data = json.load(request)

            new_product = Product()
            new_product.title = data.get("title")
            new_product.description = data.get("description")
            new_product.category = Category.objects.get(id=data.get("category"))
            new_product.image_url = data.get("image_url")
            new_product.year_released = data.get("year_released")
            new_product.is_original = data.get("is_original")
            
            new_product.full_clean()
            new_product.save()

            new_product.genres.set(genres) # save many-to-many related data after saving new product

            serialized_data = ProductNestedSerializer().serialize(new_product)
            return JsonResponse(data=serialized_data, status=200)

    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()


def product_detail(request, product_id):    
    try: 
        if request.method == "GET":
            product = Product.objects.get(id=product_id)
            serialized_data = ProductNestedSerializer().serialize(product)
            return JsonResponse(data=serialized_data, status=200) 
    
    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()

@csrf_exempt
def product_update(request, product_id):
    try:
        if request.method == "POST":
            data = json.load(request)

            product = Product.objects.get(id=product_id)
            product.title = data.get("title")
            product.description = data.get("description")
            product.category = Category.objects.get(id=data.get("category"))
            product.genres.set(data.get("genres"))
            product.image_url = data.get("image_url")
            product.year_released = data.get("year_released")
            product.is_original = data.get("is_original")

            product.full_clean()
            product.save()

            serialized_data = ProductNestedSerializer().serialize(product)
            return JsonResponse(data=serialized_data, status=200)

    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()

@csrf_exempt
def product_delete(request, product_id):
    try:
        if request.method == "POST":
            product = Product.objects.get(id=product_id)
            product.delete()
            return JsonResponse(data={"result": f"Successfully Deleted {type(product).__name__}."}, status=203)

    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()
```

Yeeesh, that's a lot of code we wrote there in our `views.py`!! And this was for only *one* of our resources (`Products`). Imagine how much code we'll need add when we have to add another separate collection of view handlers for fully CRUD-ing `Categories`, `Genres`, and `Reviews`! This is where the idea of *pre*factoring (i.e. refactoring smartly beforehand) our code will be beneficial. 

##### Refactoring

A lot of our code logic will be the same regardless of what resource we are CRUD-ing. Additionally, instead of having 5 individual view handlers for our main CRUD operations... 
1. List All
2. Create
3. Detail
4. Update
5. Delete

...What if condensed the view handlers down to just two main cases: 
A. Operations independent of a data instance: (1) List All, and (2) Create
B. Operations dependent on a data instance: (3) Detail, (4) Update, and (5) Delete

This is also where we'll want to leverage some additional HTTP methods instead of always using "POST" for data writing. The request method type will let our server know what operation is being requested. Our new mapping of HTTP methods to our CRUD operations will be:

| Requires Existing Instance? | CRUD Operation | HTTP Method |
| --------------------------- | -------------- | ----------- |
| No | List All | GET |
| No | Create | POST |
| Yes | Detail | GET |
| Yes | Update | PUT |
| Yes | Delete | DELETE |

There's a specific reason why we are choosing these particular HTTP methods (https://www.restapitutorial.com/lessons/httpmethods.html). With this plan in place, we can now simply create 2 view handlers (instead of the original 5) and still implement our prior functionality for CRUD-ing `Product` data. Let's take a look at what our refactored code structure might look like:

**view.py template**
```python
# netflix_app/views.py

## error helper methods

def error_on_request(error):
    return JsonResponse(data={"error": error}, status=400)

def bad_request():
    return error_on_request("Bad Request.")


## Product view handlers

@csrf_exempt
def product_list_view(request):
    try:
        if request.method == "GET":
            pass # LIST ALL products logic
        
        if request.method == "POST":
            pass # CREATE new product logic   

    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()

@csrf_exempt
def product_detail_view(request):   
    try:  
        if request.method == "GET":
            pass # product DETAIL logic

        if request.method == "PUT":
            pass # UPDATE product logic

        if request.method == "DELETE":
            pass # DELETE product logic
    
    except Exception as e:
        return error_on_request(str(e))
        
    return bad_request()
```

That's a little better. But this is just the start of our refactoring. We need to make this code generic enough to use with ***ANY*** model object, not just `Product`. To save us from seeing a bunch of progressively refactored code blocks as we go through our refactoring process, let's jump ahead and just look at what our final refactored code might look like (warning: this is a large refactor from what we started with):

**views_helpers.py (new file)**
```python
# netflix_app/views_helpers.py

from django.http import JsonResponse
import json


## error helper methods

def error_on_request(error):
    return JsonResponse(data={"error": error}, status=400)

def bad_request():
    return error_on_request("Bad Request.")


## request helper methods

def get_items(ModelType, SerializerType):
    try:
        items = ModelType.objects.all()
        serialized_data = SerializerType().serialize_all(items)
        return JsonResponse(data=serialized_data, status=200, safe=False)
    
    except Exception as e:
        return error_on_request(str(e))

def get_item(ModelType, SerializerType, item_id):
    try: 
        item = ModelType.objects.get(id=item_id)
        serialized_data = SerializerType().serialize(item)
        return JsonResponse(data=serialized_data, status=200) 
    
    except Exception as e:
        return error_on_request(str(e))

def save_create_item(form_data, ModelType, FormType, SerializerType, item_id=None, disabled_fields_for_update=None):
    try: 
        item = ModelType.objects.get(id=item_id) if item_id else None
        form = FormType(form_data, instance=item) # handles data extraction and validation!

        # silently (i.e. don't raise an error) ignore data updates for specified fields
        if disabled_fields_for_update:
            for field_name in disabled_fields_for_update:
                form.fields[field_name].disabled = True

        if form.is_valid():
            item = form.save()
            serialized_data = SerializerType().serialize(item)
            return JsonResponse(data=serialized_data, status=200)
        else:
            errors = json.dumps(form.errors)
            raise Exception(errors)
    
    except Exception as e:
        return error_on_request(str(e))

def delete_item(ModelType, item_id):
    try: 
        item = ModelType.objects.get(id=item_id)
        item.delete()
        return JsonResponse(data={"result": f"Successfully Deleted {type(item).__name__}."}, status=203)
    
    except Exception as e:
        return error_on_request(str(e))


## view helper methods

def list_view(request, ModelType, FormType, SerializeType):
    if request.method == "GET":
        return get_items(ModelType, SerializeType)
    
    if request.method == "POST":
        return save_create_item(json.load(request), ModelType, FormType, SerializeType)

    return bad_request()


def detail_view(request, ModelType, FormType, SerializerType, item_id, disabled_fields_for_update=None):    
    if request.method == "GET":
        return get_item(ModelType, SerializerType, item_id)

    if request.method == "PUT":
        return save_create_item(json.load(request), ModelType, FormType, SerializerType, item_id, disabled_fields_for_update)

    if request.method == "DELETE":
        return delete_item(ModelType, item_id)

    return bad_request()
```

**views.py**
```python
# netflix_app/views.py

from django.forms import modelform_factory
from django.views.decorators.csrf import csrf_exempt
from .models import *
from .serializers import *
from .views_helpers import *


## Category view handlers

CategoryForm = modelform_factory(Category, fields="__all__") # Model Form class

@csrf_exempt
def category_list_view(request):
    if request.method != "GET": # don't allow CUD'ing for categories
        return bad_request() 
    return list_view(request, Category, CategoryForm, CategoryNestedSerializer)

@csrf_exempt
def category_detail_view(request, category_id):
    if request.method != "GET": # don't allow CUD'ing for categories
        return bad_request()
    return detail_view(request, Category, CategoryForm, CategoryNestedSerializer, category_id)


## Genre view handlers

GenreForm = modelform_factory(Genre, fields="__all__")

@csrf_exempt
def genre_list_view(request):
    if request.method != "GET": # don't allow CUD'ing for genres
        return bad_request() 
    return list_view(request, Genre, GenreForm, GenreNestedSerializer)

@csrf_exempt
def genre_detail_view(request, genre_id):
    if request.method != "GET": # don't allow CUD'ing for genres
        return bad_request()
    return detail_view(request, Genre, GenreForm, GenreNestedSerializer, genre_id)


## Product view handlers

ProductForm = modelform_factory(Product, fields="__all__")

@csrf_exempt
def product_list_view(request):
    return list_view(request, Product, ProductForm, ProductNestedSerializer)

@csrf_exempt
def product_detail_view(request, product_id):
    return detail_view(request, Product, ProductForm, ProductNestedSerializer, product_id)


## Review view handlers

ReviewForm = modelform_factory(Review, fields="__all__")

@csrf_exempt
def review_list_view(request):
    return list_view(request, Review, ReviewForm, ReviewNestedSerializer)

@csrf_exempt
def review_detail_view(request, review_id):
    disabled_fields_for_update = ["product", "username"] # prevent updating these fields
    return detail_view(request, Review, ReviewForm, ReviewNestedSerializer, review_id, disabled_fields_for_update)
```

Yes, we just changed a lot in the refactored code above. While most of it shouldn't be too difficult to understand (given time to read and process the code), the one thing we should discuss is the use of Django's magic [modelform_factory() function](https://docs.djangoproject.com/en/dev/ref/forms/models/#django.forms.models.modelform_factory). 

We have not covered the use of Model Forms in Django, but they are a powerful tool in making our lives as developers a bit easier, as they abstract away a lot of the data parsing and validation process (as well as providing convenient HTML form template functionality) that we would normally have to do ourselves. We're not going to cover Model Forms in much detail today, but you can read more about them in Django's [Model Form Reference](https://docs.djangoproject.com/en/dev/topics/forms/modelforms/#modelform) page. 

To summarize how Model Forms work, we simply need to specify the Model that the Form is meant to reference, and which fields from the Model that we want to be accessible by the Form... and Django handles the required mapping of incoming data to model fields (IMPORTANT: so long as we ensure that the incoming data field names **exactly match** our Model field names). 

The `modelform_factory()` is a handy function that let's us create Model Forms on the fly... it's a factory that makes Model Forms for us!

#### Urls

And with our view handlers now completed, the last and final step is to implement all of our routing. Thankfully, this step is pretty straight-forward:

**project urls.py**
```python
# netflix_proj/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("netflix_api/", include("netflix_app.urls"))
]
```

**app urls.py (new file)**
```python
# netflix_app/urls.py

from django.urls import path
from .views import *

urlpatterns = [
    path("categories/", category_list_view, name="category-list"),
    path("categories/<int:category_id>/", category_detail_view, name="category-detail"),

    path("genres/", genre_list_view, name="genre-list"),
    path("genres/<int:genre_id>/", genre_detail_view, name="genre-detail"),

    path("products/", product_list_view, name="product-list"),
    path("products/<int:product_id>/", product_detail_view, name="product-detail"),

    path("reviews/", review_list_view, name="review-list"),
    path("reviews/<int:review_id>/", review_detail_view, name="review-detail"),
]
```

### Testing End-Points with Postman

Now that we have our server logic all implemented, it's time to fire up our server:

```sh
$> python manage.py runserver 
```

We don't have a front-end coded up just yet. While it would be great to have a React app to wire in with our Django API, we should first make sure our API end-points are all functional. We're going to use a simple yet nifty application called [Postman](https://www.postman.com/downloads/) to test out our API. This application will allow us to easily send various types of HTTP requests to our API end-points, and show us the response returned from our server. 

<img src="../page-resources/postman2.png" width=640>

Here are what our requests should look like, based on the routing set up in our Django project:

Products:
- `GET` : `http://localhost:8000/netflix_api/products/`
- `POST`: `http://localhost:8000/netflix_api/products/`
    - JSON body: 
    ```javascript
    {
        "title": "New Show 1",
        "description": "Some description",
        "category": 1, // assuming some categories were already created
        "genres": [ 5, 7 ], // assuming some genres were already created
        "image_url": null,
        "year_released": "2022-01-01",
        "is_original": false
    }
    ```
- `GET` : `http://localhost:8000/netflix_api/products/1/`
- `PUT` : `http://localhost:8000/netflix_api/products/1/`
    - JSON body: 
    ```javascript
    {
        "title": "New Show 1",
        "description": "Some updated description",
        "category": 1, // assuming some categories were already created
        "genres": [ 5, 7 ], // assuming some genres were already created
        "image_url": null,
        "year_released": "2022-04-04",
        "is_original": false
    }
    ```
- `DELETE` : `http://localhost:8000/netflix_api/products/1/`

Reviews:
- `GET` : `http://localhost:8000/netflix_api/reviews/`
- `POST`: `http://localhost:8000/netflix_api/reviews/`
    - JSON body: 
    ```javascript
    {
        "product": 1,
        "rating": 4,
        "username": "donutlover22",
        "comment": "Just started watching this. It's a really good show! Highly recommend!!"
    }
    ```
- `GET` : `http://localhost:8000/netflix_api/reviews/1/`
- `PUT` : `http://localhost:8000/netflix_api/reviews/1/`
    - JSON body: 
    ```javascript
    {
        "product": 1,
        "rating": 3,
        "username": "donutlover22",
        "comment": "This show started off really good, but the season finale was pretty pathetic!"
    }
    ```
- `DELETE` : `http://localhost:8000/netflix_api/reviews/1/`


Once we have verified that all of our end points are working, we can move on to developing our front-end application for this project using React.

## External Resources
- [Working With JSON Data in Python](https://realpython.com/python-json/)
- [JSON python module](https://docs.python.org/3/library/json.html)
- [Model Form Reference](https://docs.djangoproject.com/en/dev/topics/forms/modelforms/#modelform)
- [Postman](https://www.postman.com/downloads/)

## Assignments
- Go over today's tutorial yourself!
- [School API](https://github.com/romeoplatoon/django-school-api)

