For the past few years I have advocated best practices for building [REST apis](http://phalt.co/rest-digested/) and I spent a lot of time [building](http://pokeapi.co) reasonably well designed [examples](https://swapi.co) to help demonstrate it. I learned that building REST apis from the ground up isn't hard at all because you have no legacy or technical debt to work with, so _of course_ everything is going to work well and be praised for being RESTful.

Building RESTful apis for well established projects is a lot trickier but also a lot more exciting. In January I joined [Lyst](https://lyst.com) and my first task was to start replacing their old JSON RPC-style API with a brand new REST-like API. _"Great!"_ I said, _"This is exactly the type of job I want!"_

During this (still on-going) rebuild I encountered a few interesting design problems that I had to overcome. I hadn't read much about these design problems in REST apis before so sharing my experience may be helpful to you or provide you with the opportunity to lecture me on how I should be doing it right.

# 1 to 1 Relationships
Lyst has a logical 1 to 1 relationship between a _User_ and a _Cart_. The _Cart_ is where you store all the items you want to purchase. A _User_ is the person logged into the website or using the REST API. It is pretty easy to represent this relationship in software. Here is a Python example (very simplified example):

```python
me = User.objects.get(username='Paul')
my_cart = me.cart
```
I needed to recreate this logical relationship in the REST api so our mobile applications and other future services can see the cart, add to the cart, or check it out.
### Round One
REST encourages us to seperate each resource as much as possible because it's stateless and it doesn't necessarily know that a relationship exists beyond a hypermedia link. This was my initial URI design for the _Cart_:

```
# Get this user's cart!
GET /api/user/<user_id>/cart/<cart_id>
# Update the details of this cart!
POST /api/user/<user_id>/cart/<cart_id>
# Delete this cart!
DELETE /api/user/<user_id>/cart/<cart_id>
# YAY REST!!
```
After designing these URIs and running it through some tests I discovered that it was:

RESTful ✔️
Actually usable? ✔️
But does it actually make sense? **X**

### Round Two
Bear with me for a second here and let me explain why this URI design doesn't make sense for these resources.

It's all to do with the relationship between a _User_ and a _Cart_. There is always one _Cart_ for each _User_. When a _Cart_ is "checked out" and a new one is made, we still just use the same code to get the new one:

```python
me = User.objects.get(username='Paul')
my_cart = me.cart
first_cart.checkout()
# This is a new empty cart, but the 1 to 1 relation is the same
me.cart == first_cart
>>> False
```

So when designing the URI for this, do I need use IDs to declare which resource I want? It doesn't make sense as we only ever want to get the _User_'s _Cart_ and do things with it (previous Carts that are checked out are stored elsewhere as "_Orders_").

The new URI design then looks like this:

```
GET /api/user/<user_id>/cart
POST /api/user/<user_id>/cart
DELETE /api/user/<user_id>/cart
```

Awesome! It's looking much tidier now. We now see an explicit 1 to 1 relationship with a _User_ and a _Cart_. The ID on the cart is redundant and we never need it. It implies a _User_ can have more than one _Cart_, which they can't.

### Round Three
But I still had issues with the URI.

Do I really need to be able to delete the _Cart_? No — it's always present, so we can throw that away.

Do I really need to be able to update the Cart? Sure, but what do I need to update? Because I need to be able to add new items to it. However, that should be under the Item resource surely? Great! Now we can get rid of that too.

The URI after my third design looked like this:

```
GET /api/user/<user_id>/cart
```

Now a _Cart_ always exists, it is always linked to a _User_, and even if our web application updates, replaces, or changes it then we always get back the same representation. Hey look at that: __Idempotency?__

This final URI may work well for 1 to 1 relationships such as a _Driver_ for a _Vehicle_, or an _Author_ for a _Book_, but the API I was working on could be simplified a bit more.

### Potentially Controversial Round Four
The URI `/api/user/<user_id>/cart` implies that I can access or see the _Cart_ of any User, even if it isn't my own. This isn't true, so how do we get around this? `/api/user/cart` perhaps? It was at this point that I decided to be radical and define the endpoint as this:

```
GET /api/cart
```

Allow me to explain: I neglected to mention that this entire api is behind an authentication gate (OAuth to be exact.) When an api user calls this endpoint they require authentication credentials which are linked to a user account on the website. This user account is the same _User_ we've been talking about previously. [Django REST Framework](https://django-rest-framework.org) does this nifty little thing which sets the `request.user` as that _User_ and with that we can then get the _Cart_ and return it. So we don't need to explicitly scope the _User_ or the `user_id` in the URI.

We've eliminated any ambiguity about seeing other _User_'s _Carts_, and we've simplified and abstracted the need for discovering the user as it is already there in the request.

### Representation is not just Attributes
So one thing you're probably wondering what exactly do I need this endpoint for? If the Cart is just a container for the items it has, what attributes on the _Cart_ model do I need to represent with this resource?

Don't forget that a representation of a resource doesn't always have to mirror the attributes of a model. [Jacob Kaplan-Moss's great article on REST worst practices](http://jacobian.org/writing/rest-worst-practices/) reminds us this isn't always true. We might want to include hypermedia links to other resources, or provide a `count` attribute.

In our _Cart_ resource we provide:

- Item count
- Item links (a list of hypermedia URLs)
- The total cost of the items currently in this cart
- The shipping costs for items in the cart
- If shipping information is complete (true/false)
- The date/time this cart was last updated

and a bunch of other stuff. So this resource endpoint is actually _very_ useful as we can get a lot of summary information with a single http request.

# Subresources
The second problem is mostly a tip on how to split up gigantic resources. Despite what I said about respresentation not always just being attributes, our _Cart_ model does have a tonne of attributes that we'd like to represent such as:

- shipping address details
- billing address details
- payment details

All of these attributes total to around about 40 things. Requesting these and POSTing these attributes to one endpoint each time is very wasteful and the payload will be quite big. So how do you split it up and make it easier to use? Using HTTP PATCH is a quick solution for updating but not for GETing.

Another way to do it is to logically separate the resource into subresources with a URI scheme like so:

```
/api/cart/shipping
/api/cart/billing
/api/cart/payment
```
Internally you can grab the exact same object or model and serialize a different set of attributes for each endpoint.

### GET /questions

Without a doubt I'll have other things to blog about soon. Designing and building web apis for a living means I encounter lots of interesting nuances that I'll be sharing.

Got an opinion on this? Shoot me a tweet on [Twitter](https://twitter.com/phalt_), I'd love to hear what your think about API designs with 1 to 1 relationships and subresources.
