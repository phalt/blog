---
title: "REST digested"
date: 2015-07-25T10:49:06+01:00
draft: false
---

There is a lot of confusion about RESTful APIs on the web. Some of the most popular web APIs claim to be RESfulT, but instead are the product of the developer's own interpretation of what they think it actually should be.

You'll be surprised how many popular web APIs fail to meet some of the most basic RESTful precepts.

I've been reading through dissertations, published books and various online resources in order to provide a digested and clear understanding of what REST should be. This is by no means an extensive guide, but is should help clear up a lot of confusion and should help you avoid the most common mistakes.

## REST is NOT a standard

This is the first thing you should know: unlike [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) and [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), REST does not have a formal, published, or defined standard that is regulated by anybody like the IFFF. The concept was outlined in [Chapter 5 of Roy Fielding's 2002 dissertation](https://www.ics.uci.edu/~taylor/documents/2002-REST-TOIT.pdf). Since then developers have expanded upon the details in the paper to adapt RESTful standards for web services.

I'll be making an article naming and shaming a few of these APIs later on, but for now remember this - there is *no* REST standard, just guidelines. However, they are really good guidelines.

## What SOAP is

It's worth briefly describing what a [SOAP](https://en.wikipedia.org/wiki/SOAP) web service is, for comparison, as a lot of people compare it to REST.

SOAP services often have one URI end point, one HTTP method, and with method information encapsulated in the HTTP body, usually in XML format (we'll cover these things in a bit, don't worry if you're lost). Nearly all SOAP services define their own methods, much like a 3rd party library for a programming language defines it's own methods.

```
POST /soap_uri HTTP/1.1
  <SoapBody>
    <GetPokemon>
      <Query>Bulbasaur< /Query>
    </GetPokemon>
  </SoapBody>
```

The method, **GetPokemon**, is defined in the HTML body, regardless of the HTTP method used. One ambiguous URI is used: soap_uri

This isn't resource-oriented because it doesn't capitalise on the use of the web and explicitly the use of HTTP. What's the issue with this? The benefit of RESTful web services is that they use the HTTP standard for defining the method of interaction with a resource, which means any programmer familiar with RESTful web architecture can look at a resource URI and already know the methods they need to get, update, delete or create new instances of that resource. There is little need to delve into documentation except to learn the URI endpoints they need to talk to. (Though that's not an excuse to avoid writing any documentation!).

## HTTP methods

Determining the method of interaction with the web service should be done solely through the use of the HTTP methods in HTTP requests. When GETting results, make sure you use a HTTP GET request. When creating a new resource item, use a PUT method. HTTP POST can be used to edit or update a resource, or to create a resource when it's URI isn't known prior to its creation. HTTP DELETE is self explanatory and shouldn't need to be explained.

SOAP does not rely solely on the use of HTTP methods for determining the actions on resources, for example the old Google SOAP API (as described in [chapter 1 of O'Reilly's RESTful web services](http://shop.oreilly.com/product/9780596529260.do)) used a HTTP POST on all requests, especially the search request, which was actually GETting data, not POSTing it! Silly Google! That's not how HTTP is supposed to be used.

## HTTP response codes

REST also capitalises on the well documented HTTP response codes to provide developers with feedback on their interaction with a web service. Sending a HTTP GET request to a web service? Expect the status code to be 200 OK if the resource is found, and 404 NOT FOUND if it is not. Creating a new resource item with a HTTP POST request? Expect a 201 CREATED response if the item is successfully created.

**Request**

    GET /pokemon/1/ HTTP 1.1

**Response**

    HTTP/1.1 200 OK

The use of HTTP standards are another brilliant example of REST using the already-defined HTTP infrastructure that the web runs on. No need to recreate a series of bespoke responses for your service!

## Scoping

When you need to scope, or filter a request, RESTful web services uses URI parameters. For example: when filtering on Pokemon that are fire type in the PokéAPI API:

    /pokemon?type=fire

This scoping, combined with the use of HTTP methods to define the interaction, makes REST very powerful and diverse. In the first line of the HTTP request you see:

    GET /pokemon?type=fire

Which pretty much tells the server already what the request is doing, regardless of any later filtering of scoping.

## Statelessness

**Statelessness means that each HTTP request made to and from the web service happens in complete isolation**. No other request needs to be made in order to manipulate content in a web service. Any computation, authentication or persistence is handled on either end of the service. No state, such as cookies or sessions, are used to manipulate the processing of a request. It's a fairly simple concept, but the name can be misleading to some.

To help clear up the confusion; a good example of something that isn't stateless: FTP. When you cd into a directory with FTP and get a file you're still in that directory afterwards. You can get another file from the same directory without having to go to the same directory again. This state is saved by the FTP program. If this were a REST service, you'd have to get those files with the full path, or URI, each time you wanted them. You have to pass the authentication credentials with each request that is made. That is statelessness.

## HATEOAS

You know when you request a web page in your browser and it delivers back some html, with links to other pages in it, and you use those links to make another request to navigate to another page? That's HATEOS, or **Hypermedia As The Engine Of Application State**. In reference to RESTful web services it means providing the URI to other resources in the HTTP body of responses.

This is especially relevant when resources are related to other resources and you need to provide a way of jumping between them. The Pokemon resource in PokéAPI is related to many different resources (such as Move, Type and other Pokemon) and HATEOAS is used to provide URI links (named resource_uri) to those resources.
```
"evolutions": {
        "level": 16,
        "method": "level up",
        "resource_uri": "/api/v1/pokemon/2/",
        "to": "Ivysaur"
    }
```
It means the developer isn't left in the dark when presented with a relation to another resource, but with no way to access it. It is also extremely helpful to provide the URI of the requested resource in all responses. You'll be surprised how many web services don't do this.

## Why all this confusion then?

Chapter 1 of RESTful web services goes into more detail about this, but to put it simply:

**Developers are designing web services like traditional computer programs without fully understanding REST.**

The assume that use of HTTP to interact with a service qualifies it as RESTful, when they're just using one HTTP method for all interactions, or one URI. They're designing their own get / update / delete / edit methods just like they would in a computer program, without realising the RESTful web services should explicitly use HTTP methods for this. It's an easy, forgivable mistake. Especially when athe main evidence and guidance for building a RESTful service is other poor examples of misguided attempts. The fact that REST isn't a standard doesn't help either because there is no one to regulate it.

## Resoure-Oriented Architecture

A final reason for most current attempts at REST being failures: REST wasn't ever designed solely for web services. However in Chapter 4 of RESTful web services (you should read it), Richardson and Ruby outline a set of web-oriented rules for designing services that conform properly to the web and still meet RESTful standards. Richardson and Ruby call this "Resource-Oriented Architecture" and it is a very pragmatic set of rules that we all should understand if we ever want to properly solve the What is REST? issue.

## Here is an idea...

Maybe we should create a board of REST experts who give out awards or badges of approval to web services that provide truly RESTful web services! Let me know if something like this already exists.
