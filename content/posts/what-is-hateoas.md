---
title: "What Is Hateoas"
date: 2017-07-25T10:49:06+01:00
draft: false
---

HATEOAS, or _Hypermedia As The Engine Of Application State_, is a fun acronym. It's a term used regularly in HTTP APIs, and it's actually a fundamental concept for the web.

But like all API-related concepts, it's often poorly understood and implemented incorrectly. This post isn't going to focus on the _right_ way to implement HATEOAS. Instead, let's look at the history of it and understand why it's useful, and why you _should_ be using it in your API. We'll also talk about the term _Hypermedia_, which is integral to HATEOAS.

## How HATEOAS Came To Be

In order to understand HATEOAS and Hypermedia we need to travel back to the 1970s, to the birth of [HyperText](https://en.wikipedia.org/wiki/Hypertext).

The concept for hypertext was first described loosely in 1945 in [Vannevar Bush](https://en.wikipedia.org/wiki/Vannevar_Bush)'s hypothetical project [Memex](https://en.wikipedia.org/wiki/Memex), in which a proto-hypertext concept described a way for machines to do the following:

> develop and read a large self-contained research library, create and follow associative trails of links and personal annotations.

Bush's concept was never turned into an actual prototype, but highly detailed descriptions of how it would work were produced, much like Babbage's descriptions and diagrams for the difference engine.

It wasn't until 1963 that we actually saw the introduction of the words "hypertext" and "hypermedia" were created.

![Ted Nelson](http://i.imgur.com/ICaEyLF.jpg)
_By Dgies - Own work, CC BY-SA 3.0, [https://commons.wikimedia.org/w/index.php?curid=13691666](https://commons.wikimedia.org/w/index.php?curid=13691666)_

[Ted Nelson](https://en.wikipedia.org/wiki/Ted_Nelson), one of the many computer visionaries, coined the terms when working on an early version of something similar to the internet. The project was named [Project Xanadu](https://en.wikipedia.org/wiki/Project_Xanadu), which described the first true hypertext-type information system, far more ambitious than the world wide web project, and notorious for being the world's longest running vaporware project.

The concepts behind hypertext, which is:

> text displayed on computer or information systems, with references (hyperlinks) to other related text which can be access immediately by the user

was described best in Ted's book, [Computer Lib/Dream Machines](https://en.wikipedia.org/wiki/Computer_Lib/Dream_Machines). Hypertext and hypermedia are used in this book as very abstract philosophical concepts. Given the era (1970's) and the name of the project (Xanadu), I can almost guarantee Ted came up with these concepts with tripping balls on some new-age drug.

Regardless of the state of how they were created, the concept of connecting information resources together, whilst an obvious idea today, was revolutionary at the time. The very first example of a hypertext project was the [Aspen Movie Map](https://en.wikipedia.org/wiki/Aspen_Movie_Map), which allowed users to access content in a database, with a series of links on the user interface that would point to related things such as images and text. Even [The Mother Of All Demos](https://en.wikipedia.org/wiki/The_Mother_of_All_Demos), the most famous demonstration of technology of all time, demonstrated a successful hypertext concept.

The World Wide Web project, started by Sir Tim-Berners Lee, used hypertext and hyperlinking to connect documents on the web together. Before long, hyperlinking became an essential part of the modern web, and we see it in almost all information-heavy systems these days.

## Enough With The History, What Is HATEOAS Exactly?

HATEOAS is the concept where hyperlinks are used to change the _state_ of the application. Let's look at the most common example: a web browser.

The state of a web browser can be based around the current web page it is on. In the modern web, this web page could be doing many things, such as listening to web sockets or drawing UI components on a screen with JavaScript. But in order to change the _state_ of the application, that is, the web page it is currently on, we can point the mouse at a hyperlink and click it. This makes a new HTTP call on the web and requests a new document, changing the _state_ of the application, using hypertext. That's HATEOAS.

I know what you're thinking, that's really obvious right? Everyone knows that. Yeah, we do, it's just one of those concepts that have become so common place we forget what it means to perform a state change that way.

## HATEOAS And APIs

So how does this all relate to APIs? How should I be building APIs that use HATEOAS?

Well, for a start, it's not just the API that needs to be built around the concept of HATEOAS, you also need to adapt the client interface that consumes the API.

An HTTP API that is hypermedia-driven or uses HATEOAS provides URL links to related documents:

```json
{
    "name" : "Paul Hallett",
    "images" : "http://phalt.co/api/users/1/images/"
}
```

In the example above we see a response from an API about a specific user, with a hypermedia link to the images for that user. This API is hypermedia driven because it's providing a link to a related resources (the images). This feels very RESTful, and to some extent it is. It's also completely stateless: it requires zero knowledge by the client to know where to go to get images for this user, it's already described in the API response.

A client built around HATEOAS that consumes this response, will understand that `images` is a hypermedia link it can follow to a related resource. If you build your client right, this can provide infinite flexibility when you want to add in new related resources to the user without making any changes to your client.

Let's imagine our client uses the response example above to navigate to that user's images resource, we might see a response like this:

```json
{
    "user": "http://phalt.co/api/users/1/",
    "images": [
        {"image_url": "http://phalt.co/image_one.jpeg"}
    ]
}
```

There are some API response formats that focus on hypermedia like [JSONAPI](http://jsonapi.org) and [HAL](http://stateless.co/hal_specification.html). These types of response formats even extend the use of hypermedia for other CRUD actions, such as create, update, and delete.

So for example, a user response like:

```json
{
    "self": "http://phalt.co/api/users/1/",
    "name": "Paul Hallett"
}
```

Can be understood by the client in a way that it knows that sending a PATCH, PUT or DELETE HTTP request to this hyperlink will result in the desired CRUD actions. All of these actions are again, _changing the application state through hypermedia_.

A good example of an API that uses hypermedia is [PokéAPI](https://pokeapi.co) (shameless plug).

## That's Really It?

Yeah, it's really that simple. It's funny how a concept like this has become misused and poorly understood. The key things to remember when building hypermedia-driven APIs is:

* Provide full URL links to related resources.
* Encourage clients to learn how to use those links.
* Remove state or application-knowledge from the client where possible.

If you have any more questions on HATEOAS, or hypermedia, feel free to [get in touch on twitter](https://twitter.com/phalt_) or leave a comment below!
