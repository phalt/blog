I'm releasing a new open source project.

**Beckett** is a convention-based framework for building Python interfaces around HTTP APIs.

It's the first proper framework project that I've ever worked on, and the first project I've released in about two years. In that time I've learned a lot about what it takes to ship robust and sustainable code, so it's my best attempt yet at creating something useful.

If you want you can dive straight in and read the [documentation](https://phalt.github.io/beckett), but if you want to learn more about it then read on.

![leap](https://phalt.github.io/beckett/media/leap.gif)

# Goals

Beckett aims to solve a lot of the problems I've seen when I've tried to integrate API services into other projects.

I created [PokéAPI](https://pokeapi.co) and [SWAPI](https://swapi.co) to provide free educational APIs, and I've seen common problems from the students using them. Further to this, the work I've done at [Lyst](https://developers.lyst.com) has been mostly with service architecture and "micro services" where integration is essential.

This led me to create Beckett. But before we talk about what it does, let's talk about the problems I've been encountering.


# Problems integrating API services

### Dealing with JSON objects

There is a tendency to integrate API services and then just carry around the raw JSON objects from the responses it serves. This becomes a problem when you're handling many different responses from many different services and you need to figure out what is what.

Is this a `Person`? Is this a `Product`? Type-checking isn't helping here, because they're all the same, they're all `Dict`! It becomes even more of a problem when a developer decides she can inspect the JSON object to determine what it is, and your resources share similar attributes.

```python
# Data is a mysterious Dict from an API service
if data.get('shared-attribute'): # Falsely assuming this must be a designer
    # Do something wrong
```

### Repeating yourself

I've found that as people get better at good HTTP practices, their URI and integrations start to become consistent. Because of this, a lot of the work done to make those integrations is a copy/paste job. Particularly when it comes to URI structures and methods.

For example, at Lyst, one of our APIs has resources called Products and Designers, these resources have these URL structures similar to this:

```http
api/products/:id
api/designers/:id
```

and our client for talking to them has these python methods:

```python
get_product(id)
get_designer(id)
```

These methods basically do the same thing, but we've written out each one separately. It would be more practical and productive to build around this convention instead.

However, despite the conventions we all use, a lot of APIs still have domain-specific characteristics that a convention-based approach wouldn't help. An API might have a URL structure like so:

```http
/api/products?id=1234&id=6789&country=GB
```

The typical convention isn't going to help us here, but that shouldn't mean applying some conventions to aid in productivity is useless.

### Integrating hypermedia

I've [written about hypermedia](/what-is-hateoas/) and it's usefulness, but I rarely see it used in practice. This is one of the most exciting features of API services, and is being completely ignored. I think we should start using it properly in our API Clients.

### Serialisation languages are not flexible

All the existing client frameworks that I've tried to use often define themselves in yaml, JSON or another proprietary serialisation language that offers absolutely zero customisation and flexibility.

# What Beckett is

With these problems in mind, and enough experience writing and rewriting tools for creating API integrations, I started to work on a project that would become Beckett.

As I mentioned before: **Beckett is a convention-based framework for building Python interfaces around HTTP APIs**.

Because it is convention-based, it requires no magic to make it work with HTTP APIs that follow REST-like URI and resource styles. Resources are declared as Python classes, like so:

```python
# resources.py
from beckett.resources import BaseResource


class Product(BaseResource):
    class Meta:
        name = 'Product'
        identifier = 'slug'
        attributes = (
            'slug',
            'brand',
            'price',
            'images',
        )
        valid_status_codes = (
            200,
            201,
        )
        methods = (
            'get',
            'post',
        )
```

You then take your resources and register them with a Client:

```python
# client.py
from beckett.clients import BaseClient
from .resources import Product

class MyClient(BaseClient):
    class Meta:
        name = 'My API Client'
        base_url = 'https://myapi.com/api/'
        resources = (
            Product,
        )

```

That's it. That's all you need to do.

Beckett will generate python methods using [reflection](https://en.wikipedia.org/wiki/Reflection_%28computer_programming%29) on the Client, for talking to the registered resources:

```bash
from .clients import MyClient
client = MyClient()
client.get_product(uid='product-slug')
>>> <Product | product-slug>
```

These methods generate the URI structure to match RESTful style URIs:

![uri-structures](http://i.imgur.com/XnpJQuG.png)

The responses from the client are typed instances of the Resource class, and all the attributes you defined will be taken from the JSON response and added as attributes on the instance:

```bash
resource = clients.get_product(uid='some-slug')[0]
type(resource)
>>> <class 'resources.Product'>
resource.name
>>> 'Handbag'
```

### Flexibility

Because not all APIs are the same, Beckett is designed to be flexible, allowing you to modify various aspects of how it works:

- URI generation
- Paginated response rendering
- Request headers and request sessions
- Security and authentication

For example, if we needed to modify our Product resource to work with the unique URI I mentioned earlier, we can subclass the `get_url` method in our resource:

```python
class Product(BaseResource):
    class Meta:
        ...

    @classmethod
    def get_url(cls, url, uid, **kwargs):
        return '{}/products/?id={}&country={}'.format(
            url,
            uid,
            kwargs.get('country')
        )
```

This approach takes advantage of inheritance to provide much more flexibility than what data serialisation languages are able to provide.

### Hypermedia

Beckett understands hypermedia and helps you to traverse your API through related resources. If our `Product` resource has related `Designers`, we can declare it in our code using the `HypermediaResource` base class, which will inspect and understand Hypermedia links:

```python

class Designer(HypermediaResource):
    ...

class Product(HypermediaResource):
    class Meta:
        ...
        base_url = 'http://myapi.com/api'
        related_resources = (
            Designer,
        )
```

Beckett will then inspect the JSON response data, matching URL patterns for the `Designer` resource, and generates methods for talking to it:

```bash
product = client.get_product(uid='some-slug')[0]
# Will raise an error if uid is not related
product.get_designers(uid=4)
<Designer | Some Designer>
```

This is a very powerful feature that I'm quite happy about, as I want it to help more API Client frameworks adopt hypermedia.

## Who is already using Beckett?

I could not have released Beckett without road testing it first. Beckett is already powering the Python clients for [PokéAPI](https://github.com/PokeAPI/pykemon) and [SWAPI](https://github.com/phalt/swapi-python). It has also been tested at Lyst, providing the integration of one of our busiest API services. This internal API fires over one hundred thousand requests a day and helped to iron out some bugs in the first few versions of Beckett.

## Future plans

My plan now is to maintain and grow Beckett as an open source project. I would like it to serve as a good example of how API Client integrations can be provided, especially ones that work with the concepts of hypermedia properly.

Currently Beckett provides flexible control over the HTTP Request and HTTP Response objects it deals with. I'd like to provide more flexibility in the future, but to do that I need more domain-specific use cases.

I'd also like to add Resource backends that understand JSONAPI, HAL, and other JSON formats. The `BaseResource` and `HypermediaResource` classes already demonstrate the flexibility and ease of integrating new ones.

Finally, I'd like you to download it and try integrate it into your projects, and then provide me some feedback on how to improve it!

## Where is the name from?

Beckett's name is from [Dr. Sam Beckett](https://en.wikipedia.org/wiki/Sam_Beckett), the main character of Quantum leap. He leaps through time and inhabits other people's bodies, solving their problems, before moving on to another person in another time. This is kind of how I imagined the framework operates: it becomes your API, solving your problems. Beckett is also the name of a famous [avant-garde novelist](https://en.wikipedia.org/wiki/Samuel_Beckett), which is pretty cool.

Any questions, feel free to leave them below, or get in touch with me [on twitter](https://twitter.com/phalt_).
