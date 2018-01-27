APIs are an integral part of the way web applications share data and functionality in the modern age of software development.

Companies such as [Twitter](https://twitter.com) are arguably successful because of their API, allowing third-party developers to build interesting new applications around their service that they couldn't even dream of.

[Twilio](https://twilio.com) and [Stripe](https://stripe.com) have built entire companies around an API that provides a fundamental service such as telephony or card payments.

Quite a few companies and developers are building fantastic applications without providing an API, which could drastically improve their user traction, their attractiveness to developers, and their popularity.

The thing is: building APIs is not hard, but a lot of companies neglect to include them in their service because they don't think anyone will use it and it'll be a waste of time.

A friend of mine recently said that _"If you expose data, people will consume it"_ and I have seen that first hand with [PokeAPI](http://pokeapi.co).

I believe that APIs are so fundamentally beneficial, and provide so much extra value, that __I'm making it my personal goal to promote the development and integration of APIs into as many applications as possible.__

## Exposing data sets

APIs don't just have to provide a service, they are also fantastic for exposing extremely large data sets that would otherwise cause a massive barrier to entry to potential developers.

A brilliantly novel example of this is something I built recently: [PokéAPI](http://pokeapi.co). PokéAPI exposes a gigantic amount of data about the [Pokémon](https://pokemon.com) video games. Seems completely ridiculous right?

Despite looking like a completely niche data set to expose through an API, PokéAPI has processed over 90,000 requests in only thirty days of being online (and **12 million** as of October 2014). This is an amazing figure for something which on the surface seems pointless to implement, because most people's first reaction is "But who would use that?"

## What this means

It turns out, there is always someone who can benefit from an API, so it's always worth implementing one. Just like developing products that benefit people, building an API service that makes life easier for other developers is hugely rewarding.

With Django is it trivially easy to expose your existing data models through an API or build an API service from the ground up. I'm going to quickly show you how to expose a standard data model with [django-tastypie](http://tastypieapi.org), using PokeAPI as our example.

## Django and APIs

Take your standard Django model:

```
# pokemon/models.py
class Pokemon(models.Model):

    def __unicode__(self):
        return self.name

    name = models.Charfield(max_length=40)
    type = models.Charfield(choice=TYPE_CHOICE)
    ....
```

If you're familiar with Django, you'll easily recognise this class as a definition of a data model stored in the database of your Django application. We want to expose that data model because in this example we're just providing access to a large data set. We'll need to install django-tastypie next:

	$ pip install django-tastypie

Include it in your settings.py and migrate it:

```
# settings.py
INSTALLED_APPS = (
    ...,
    'tastypie',
)
```

	$ python manage.py migrate

Next, we'll add a new file called api.py into our django app:
```
# pokemon/api.py

from .models import Pokemon
from tastypie.resources import ModelResource

class PokemonResource(ModelResource):

    class Meta:
        queryset = Pokemon.objects.all()
        resource_name = 'pokemon'
        allowed_methods = ['get']
```
The last step is to link up these resources to a URI, because we want to provide this externally after all:

```
 # urls.py

from pokemon.api import PokemonResource
from tastypie.api import Api

api_resources = Api(api_name='v1')
api_resources.register(PokemonResource())

urlpatterns = patterns(
    ...,
    url(r'^api/', include(api_resources.urls)),
)
```

Then we're done! If you start up your Django project and visit localhost:8000/api/v1/pokemon/1/ you'll get all that data from the model exposed as json content:


![Rotom](http://i.imgur.com/sQMal79.png)
*All the data for the Pokémon Rotom, try it for yourself.*

## What's next?

It is important you follow best practices when providing your API, these steps are not hard but are often neglected.

- **Document your API** - All great APIs have documentation, check out PokeAPI documentation for an example.
- **Follow RESTful standards** - This is a tricky one because the term "standards" is used quite loosely here - there are no official standards, but there are guidelines. Luckily, Django-tastypie already does most of this for you.

On my next blog post, I'll be defining the best guidelines for ensuring your API is truly RESTful. Even some of the big names in APIs are making mistakes here, so we've all got something to learn!
