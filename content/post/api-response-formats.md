---
title: "API Response formats"
date: 2016-07-25T10:49:06+01:00
draft: false
---

Over the past week I've been chatting with quite a few Web API engineers at EuroPython. It's been great hearing everyone agreeing about the advantages of RESTful designs. I love listening to their eagerness to develop APIs that map closely to HTTP. I don't think this was the case a few years ago so it's nice to know we're getting the hang of using HTTP properly again!

One question that I keep hearing is how we can create good response formats. There is some disagreement or confusion about the best way to write them and this often means we write our own. To me this attitude feels a lot like the state of RESTful HTTP APIs a few years ago.

We _are_ all agreeing that the use of JSON encoding by default is a good idea. It strikes a perfect balance between structure and data size. But despite this the JSON content we supply isn't always the same. Just have a look at some of the most popular APIs on the web today and you'll see inconsistent styles. This makes it hard to build reusable integrations with different services, even if they have reasonably similar interfaces.

I recently had a conversation with one of my colleagues who was integrating one of my APIs and they wanted to know why they, as a human, hand to learn how to use it:

> _"Why can't the API describe to me how I should talk to it? Why do I need to read documentation?"_

This is a really interesting point. As an API developer, rather than a consumer, I forgot about this part of the experience.

The only common format I see that is fairly consistent is the _"paginated resource"_ format. It tends to look something like this:

```bash
{
    "count": 100,
    "next": "https://api.example.com/resource?page=3",
    "previous": "https://api.example.com/resource?page=1",
    "data": [
        ...
    ]
}
```

Even this isn't always identical. Sometimes the attributes are named slightly differently, but the general structure is the same.

## The Trailblazers

So who is trying to standardise response formats at the moment? It's definitely not an area that is completed ignored.

### JSON API

Currently, I think the most popular effort is [JSON API](http://jsonapi.org) (at least in the circles I frequent). As the name implies, they provide a [specification](http://jsonapi.org/format/) for formatting objects and related objects, as well as related links, in JSON. They encourage a RESTful-like design of the API layer but you're welcome to use your own URI formats. The response format is very flat, and doesn't try to nest sub resources too deeply. Sub resources is a good thing to support and it is [something I'm also an advocate for](http://phalt.co/relations-and-subresources-in-rest/). Here is the simplest example I can find:

```
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!",
      "body": "The shortest article. Ever.",
      "created": "2015-05-22T14:56:29.000Z",
      "updated": "2015-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "people"}
      }
    }
  }],
  "included": [
    {
      "type": "people",
      "id": "42",
      "attributes": {
        "name": "John",
        "age": "80",
        "gender": "male"
      }
    }
  ]
}
```

I like it and they're going in a good direction, but I still see a few problems with it.

#### Problems with JSON API

For a start, it's deeply tied to JSON. I know we're using JSON all the time now but it isn't always the best format. Sometimes we have to provide different encodings for different systems. This is even more relevant when we realise that not all API services use HTTP. Perhaps a project has a YAML-based web socket service. JSON API isn't going to help here. If we're going to standardise HTTP API response formats why not try and support other response formats for other APIs too?

They are also choosing to not support the HTTP `PUT` method. They give no [clear reason for this](http://jsonapi.org/faq/#wheres-put). I've seen this done before and the argument is usually because POST performs the same or similar action. In these situations, I personally believe the purpose of `POST` and `PUT` have been misinterpreted, but I shan't go into that today.

I've seen another attempt at standardising API response formats that appears to build upon the work started by JSON API. It's called Core API.

### Core API

Core API was started by [Tom Christie](https://github.com/tomchristie/), author of the stupidly popular [Django REST Framework](https://github.com/tomchristie/django-rest-framework) and [many other great things](https://github.com/mkdocs/mkdocs). Tom has definitely had his fair share of experiences with API services and I've regularly chatted to him about the problems that Core API is trying to solve.

The [main aim of Core API](http://www.coreapi.org) is that is tries to define agnosticism between various layers in an API service. It describes itself as:

> _"...a general purpose system for exposing service interfaces."_

The layers descrbied are the **Transport**, **Encoding** and **Document** layers.

The [transport layer](http://www.coreapi.org/specification/transport/) is HTTP, or web sockets, or even TCP. [Encoding](http://www.coreapi.org/specification/encoding) refers to the encoding format, like JSON or YAML. Finally, [Documents](http://www.coreapi.org/specification/document/) can be thought of as the resources or objects in your API service.

I think the adaptability and separation of concerns between these layers is a step onward from JSON API. Core API is trying to define a set of standard formats such that you can swap out any layer for an alternative and you know that the other two layers will work as the interaction has been clearly defined by Core API. This agnosticism is essential in a world where there are alternative ways to do everything.

Core API also attempts to provide a self-describing API in the Document layer with a concept called **Links**. A Link represents a transition that can occur on a Document, such as updating it's content, or following the Link to a related Document. A Document can have as many Links as you like. Take this example:

```
{
    "_type": "document",
    "_meta": {
        "url": "/",
        "title": "Notes"
    },
    "notes": [
        {
            "_type": "document",
            "_meta": {
                "url": "/1de153fe-6747-41d3-bc0e-d9d7d87e448a",
                "title": "Note"
            },
            "complete": false,
            "description": "Email venue about conference dates",
            "delete": {
                "_type": "link",
                "trans": "delete"
            },
            "edit": {
                "_type": "link",
                "trans": "update",
                "fields": [
                    "description",
                    "complete"
                ]
            }
        }
    ],
    "add_note": {
        "_type": "link",
        "trans": "action",
        "fields": [
            {
                "name": "description",
                "required": true
            }
        ]
    }
}
```
If you build your client correctly (by following the conventions laid out in Core API) you do not need to hard-code the actions yourself. In the above example, `add_note` is an action. Adding new actions to your API means clients can instantly interact with the new features too. This would make any API consumer very happy.

#### Problems with Core API
Core API is still very young, but I can see a lot of potential for it. But what problems do I have with it?

I think the biggest one is: it's not being talked about enough right now. It is a very new project, but I am surprised it hasn't caught the attention of more people already. Because of this, I think it has been slow to develop more than an HTML and JSON encoding format, and an HTTP transport format.

## What More Do We Need?

Despite both of these efforts (and potentially more that I've missed), I still think we need to do more to make response formats standardised. The biggest problem is to be able to be completely agnostic, whilst still being able to understand the standard from the point of view of any technology involved. I think Core API is a tiny bit outside of the scope of understanding if you're not very familiar with REST, but it's an exceptional attempt none the less.

### The Immediate Future

I want to see response formats being talked about more. I want to see some people throwing their weight around trying to encourage a standardisation. In order to make this happen I think we need to start doing the following things:

- JSON API and Core API are clearly the trailblazers and they should continue to be. We should start using them and suggesting ways to improve them.
- We should have a few radical ideas that really change up the landscape. If we stick with what we already know, we might not always find the best solution. A crazy idea might point us towards a solution that no one thought of before.
- I think JSON API and Core API should open up a channel of a communication between each other and see how they can help each other. They're definitely not rival implementations and I don't think they should treat each other like they are.

I've ranted long enough about this, and I might turn this blog post into a talk that I take around to help start this discussion. If you have opinions, then let me [know on twitter](https://twitter.com/phalt_). Together we can do awesome things 😁

Post-edit: I've been reliably informed of **HAL**, another proposed format that [also recently became an RFC](https://tools.ietf.org/html/draft-kelly-json-hal-07).
