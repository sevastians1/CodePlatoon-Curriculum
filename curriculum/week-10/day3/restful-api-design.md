# Restful API design


## Topics Covered / Goals
- brief history of REST
- modern practices in RESTful design
- a case study with dropbox


## Lesson


### What is REST?
> REST (an acronym for REpresentational State Transfer), is a poorly understood concept that means different things to different people. Many newer developers understand REST as a system for mapping CRUD operations (create, read, update, delete) onto HTTP methods (POST, GET, PUT, DELETE). This is wrong, but it's a pretty useful way to understand REST if you just want to build an application that uses an API. 

> Other more experienced developers would say that you can't fully understand REST unless you read Roy T. Fielding's [doctoral dissertation](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), published in the year 2000. This is correct, but not incredibly useful advice more than 20 years later. The internet as we use it today is very different from how it was in the year 2000, and very different from how Roy Fielding imagined it would be. For example, consider this gem of wisdom from his thesis: "cookie-based applications on the Web will never be reliable". In hindsight, this statement might seem silly, because today we know that cookies are a common and essential part of the modern web. 

> In modern practice, REST is a software architecture style for creating scalable web services that make resources available to clients. You do not need to implement every aspect of this style in your own API for it to be considered a REST API. However, the more of these aspects you implement, the more 'RESTful' the API is. Try to find a balance between conforming to well known standards, and building an API that fits the specific quirks of your application. 
- **REST** is NOT a specific technology/tool/library, or a standardized protocol. However, there are tools like django-rest-framework that help you build APIs that conform to this style. 
- Most basic concept in **REST** is a resource.
- Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in Los Angeles"), a collection of other resources, a non-virtual object (e.g. a person), etc.
- Resources can be collections (e.g. `/api/v2/users`) or singletons (e.g. `/api/v2/users/:userId`).
- a collection might contain collections (e.g. `/api/v2/user/123/comments/321`)
- Resources can be queried or modified using **HTTP** verbs.
- **REST** APIs usually represent resources as **JSON** data.

### Statelessness
- Server does not keep track of clients from one request to the next.
- Clients must supply all relevant information that the server needs to respond to the request. This state information is typically stored as session or cookie data.
- In a public RESTful API, accessing sensitive resources requires the client to send authentication credentials with every request.
- The APIs we build for our applications may leverage user's sessions, so that users can easily access their own data without authenticating every request. This is not perfectly RESTful, but it's very convenient.

### Methods (Verbs) & CRUD
> Requests can be sent using different HTTP verbs (METHODS) to perform different actions. Often these actions map to one of the four basic functions of persistent storage (CRUD), although REST and CRUD are not exactly the same thing.

> The most important aspect of these HTTP verbs is **idempotence**. This is described in [RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616), which defines the HTTP standard. An RFC (request for comments) is a publication in a series, from the standards-setting bodies for the Internet, most prominently the Internet Engineering Task Force (IETF), an international volunteer organization that sets standards for the internet, such as TCP/IP.

### Safe/Idempotent
A **safe** request will have the same effect if run zero times (ie. GET)
An **idempotent** request will have the same effect if run one or more times (ie. PUT, DELETE)

Background: If you refresh a page with a GET/PUT/DELETE request, it will resubmit itself.
This is based on the assumption that those requests are safe/idempotent.
If a page with a POST request is refreshed, it will not resubmit without first confirming with the user (ie. "Do you wish to resubmit this form data?")
This is based on the assumption that those requests are not idempotent.

#### POST (Create)
- `POST` routes are not idempotent; each POST request may modify the server state.
- typically used for creating a new resource.

#### GET (Read)
- `GET` routes should be safe; they do not affect the server's state.
- typically used for retrieving a resource.

#### PUT (Update)
- `PUT` routes should be idempotent; sending duplicate requests affects the server once.
- typically used for creating a unique resource, or modifying a specific resource.

#### DELETE (Delete)
- `DELETE` routes should be idempotent; sending duplicate requests affects the server once.
- typically used for deleting a specific resource.

### Responses
> An API is considered RESTful when using meaningful HTTP response status codes

- `200`: successful
- `201`: created
- `204`: no content
- `304`: not modified
- `400`: malformed request
- `403`: forbidden
- `404`: not found
- `500`: Server error

## Example API

| VERB       | NOUN                         |
|------------|------------------------------|
| `POST`     | `/api/v2/users`              |
| `GET`      | `/api/v2/users/345`          |
| `GET`      | `/api/v2/users?city=Chicago` |
| `PUT`      | `/api/v2/users/345`          |
| `DELETE`   | `/api/v2/users/345`          |

> The URL should contain the resource we want to act upon. The URL should have NOUNS, not VERBS. The HTTP method (aka HTTP verb) describes what we want to do with that resource. However, there can be exceptions to this rule. Sometimes, you need to perform more than four different actions on the same resource, or maybe none of the existing HTTP methods accurately describe the action you want to perform. In REST, you can have an `action` as a nested resource. With django-rest-framework, you can declare [custom actions](https://www.django-rest-framework.org/api-guide/viewsets/#marking-extra-actions-for-routing).

| VERB       | NOUN                               |
|------------|------------------------------------|
| `POST`     | `/api/v2/users/345/set-password`   |

```python
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=True, methods=['post'])
    def set_password(self, request, pk=None):
        user = self.get_object()
        serializer = PasswordSerializer(data=request.data)
        if serializer.is_valid():
            user.set_password(serializer.validated_data['password'])
            user.save()
            return Response({'status': 'password set'})
        else:
            return Response(serializer.errors,
                            status=status.HTTP_400_BAD_REQUEST)

```

## Caching.
> A RESTful api should use cache control headers to instruct the client what to cache, and for how long.
> If a client requests current weather data, the API should instruct them not to cache it at all, since it will likely be different the next time they check the weather. If a client requests data about a historical event, the API should instruct them to cache that data indefinitely, since it will never change. These instructions are sent with cache-control headers on the response from the server.
> Setting cache-control headers is not hard, but since it's such a common need, [django](https://docs.djangoproject.com/en/4.0/topics/cache/#controlling-cache-using-other-headers) and [DRF](https://www.django-rest-framework.org/api-guide/caching/) both include decorators that make it a little easier to set these headers.

```python
from django.views.decorators.cache import cache_control

@cache_control(max_age=3600)
def my_view(request):
    ...
```


## HATEOAS (Hypermedia as the engine of application state)
> Every response from the server should contain links to related actions or resources so that the client can continue exploring the api.
> As much as possible, developers should not need to read our documentation to know how to use our api. the api responses themselves should serve as documentation.
> HATEOAS makes more sense if you're talking about an HTML API, where each resource is represented by an HTML page, with `<a>` links to other resources, and a user can interactively navigate from one resource to the next, ie 'the web'. More commonly, you need to know the API structure up front so you can programmatically use it from your own application. 
> Implementing HATEOAS in your API manually is probably not worth the effort. However, some REST API frameworks can generate these links for you automatically. 

> A good example of HATEOAS can be seen in the github API. For example, go to `https://api.github.com/repos/rserota/wad` in your browser, and you'll see how the github API represents a repository. Much of the information here is about the repo itself, such as the name, description, topics, and license. However, about half of the response is just other links, related to the response, such as forks, issues, and tags for this repository. 
> Another good example of an API that implements HATEOAS is the [pokemon API](https://pokeapi.co/), which we used earlier in this course. 




### Scenarios
> Imagine we're building an API for a bank. Let's think of some Restful routes we might write to handle common actions with accounts, transactions, or credit cards. 

- view current account balance
    * `GET    :: /api/accounts/123?fields=balance`
- view all transactions
    * `GET    :: /api/accounts/123/transactions`
- view a specific transaction
    * `GET    :: /api/accounts/transactions/55421`
- add a new credit card
    * `POST   :: /api/accounts/123/card`
- change your home address
    * `PUT    :: /api/accounts/123`
- make a deposit
    * `POST   :: /api/accounts/123/deposit`
- delete an expired credit card
    * `DELETE :: /api/accounts/123/card/456`

## When to use/not use REST
- Use REST to make resources on your server available to clients.
- Not all **HTTP** verbs make sense for every resource. Only use the ones you need.

### Case Study: Dropbox
- `/delta` route returns data, is idempotent, used to be a `GET` route.
- Querying `/delta` became too complex, exceeding limitations of `GET`, so Dropbox switched to `POST`.
- `REPORT` **HTTP** verb would be more **RESTful** (idempotent, can have a body), but it’s very obscure.
- `POST` is less **RESTful** in this context, but is easier for developers to understand and work with (not all **REST** clients support all methods).


## Assignments
- [RESTful API design](https://github.com/sierraplatoon/restful-api-design)
- [To Do List](https://github.com/sierraplatoon/app-to-do-lists)
