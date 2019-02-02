---
title: "Graphene Django Sentry"
date: 2018-12-21T16:24:00+00:00
draft: false
---

My team takes full advantage of [Graphene-django](https://github.com/graphql-python/graphene-django) - a Django plugin that allows you to construct graphQL queries and mutations for your HTTP API. We also use [Sentry](https://sentry.io/welcome/) heavily in our project to track exceptions that occur in production.

There's a pretty big problem for these two plugins though - graphene-django will _eat_ the exception raised during an API call and it won't be reported to Sentry! This is particularly annoying when most of your application logic is provided through the graphene interface.

To solve this we had to wrap the standard `GraphQLView` provided by graphene-django ([this one here](https://github.com/graphql-python/graphene-django/blob/f76f38ef30b88d921df243b09c4970528b1a4007/graphene_django/views.py#L53)) with our own mini-view that just inspects the responses. If the response is an error - we send it to Sentry, and then let the response continue on it's way.

We've tried and tested it and we have decided to package it up into _pip-installable_ package so you can run Sentry and graphene together too!

It's an incredibly small package (only 23 lines long, and most of that is to make it easy to read) and dead simple to set up.

## Use it

First, install the package:

<hr>
```bash
pip install graphene-django-sentry
```
<hr>

Second, in your `urls.py` just replace your normal `GraphQLView` with the one in the package:

<hr>
```python
from django.conf.urls import url
from django.views.decorators.csrf import csrf_exempt

from graphene_django_sentry import SentryGraphQLView # Our package

from .schema import schema  # Your own graphQL schema

urlpatterns = [
    url(
      r'^graphql',
      csrf_exempt(SentryGraphQLView.as_view(schema=schema)),
      name='graphql',
    ),
]
```
<hr>

That's it!

So to recap, this enables:

1) Exceptions raised in graphene Queries and Mutations to appear in Sentry.

2) Your graphQL API to propagate exceptions back to the consumer like normal.

It's the best of both worlds!

## Credits

A big shout-out to [Ioanna Kokkini](https://github.com/ioannakok) who investigated and built the original version for us internally.

We used [cookicutter](https://github.com/audreyr/cookiecutter) to generate the pypi package.

See the project here: https://github.com/phalt/graphene-django-sentry
