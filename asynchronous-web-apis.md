One of the problems we've faced with adapting some of our services at [Lyst](http://www.lyst.com) to use Web APIs is how to handle asynchronous tasks and processes. The modern web is heavily reliant on tasks that can take an indeterminate amount of time to complete. For a lot of these tasks the end result only needs to travel between software and the database, or fire an email when it is finished.

One of Lyst's services that works like this is the **Integrated CheckOut Network** (ICON), which allows users to purchase items from multiple fashion retailers through one checkout. We can't predict how long those purchases will take or how many checkouts are waiting in the queue before it. So when we began building the Checkout API for our mobile application we needed to figure out a way to provide this asynchronous service over HTTP. The biggest limiting factor was HTTP itself.

### The Limitations of HTTP

HTTP wasn't really designed with asynchronicity in mind. Requests and Responses are stateless. HTTP connections can only stay open for so long before timing out. Even with the advent of HTTP 2.0, keeping HTTP connections open for a long time is bad practice and expensive. You end up with a lot of overhead managing them. Not to mention applications that aren't on HTTP 2.0 yet can't be supported.

So how do we adapt HTTP Web APIs to handle asynchronous services whilst still sticking to the standards of HTTP?

We can use **webhooks**.

I think a lot of people would have started reading this blog post thinking *"just use webhooks!"* and I've deliberately avoided mentioning them until now because the name is ambiguous. It's just a buzzword to a lot of people. I'm going to explain this from a different point of view without using that word and hopefully demonstrate some good practices for asynchronous communication with HTTP.

### Request

Let's start by constructing the HTTP request we need to begin the Checkout process:

```
HTTP POST /cart/checkout/
{
    'cart_id': 1234,
    ...
}

```
This very simple example is POSTing our cart id to the checkout service to begin the checkout process. The service is aware of our Cart state, so we don't need to pass any more information than the cart id. We've also got OAuth2 authentication happening here, but I've removed that for the sake of simplicity.

The first change we're going to need to add to start making this asynchronous is to include a new attribute in the body that we're sending to this endpoint. We're going to call that attribute the **callback_url**.

```
HTTP POST /cart/checkout/
{
    'cart_id': 1234,
    'callback_url': 'https://mywebsite.com/checkout_complete',
    ...
}

```
This new attribute is a fully qualified URL on *our service*, not on the web service we're using. At Lyst, the primary user of this Checkout API is our mobile applications, so we've also adapted this service to support push notifications. We can declare the push notification callback using a separate parameter:
```
HTTP POST /cart/checkout/
{
    'cart_id': 1234,
    'push_notification': true,
    ...
}

```
We store the push notification token (if they have one) of a user on our service, so we don't need to pass this around in the body of our request.

### Response
Before we talk about what happens to the **callback\_url** or the **push\_notification** value on the server, let's talk about what type of response we should be sending back when we receive this type of request.

The HTTP status code specification makes mention of a [202 Accepted](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) response status:

> "The request has been accepted for processing, but the processing has not been completed."

That sounds perfect for what we need! The response we send back immediately to the user, once they make the request above, looks like this:

```
HTTP 1.1 202 ACCEPTED
{
    'resource_url': 'https://api.lyst.com/cart/checkout'
}
```
Alongside proper documentation, this informs our user that:

* We've accepted the request and there are no problems with any of the data they've given to us.
* We have queued the task and it is currently waiting to be ran, or it is currently running.

Now we can look at the next step.

### Closing the loop
Our process is now running on the server, or it may be in a queue waiting to run. The HTTP connection has been closed with the client and we've stored the **callback\_url** or **push\_notification** value.

All we have to do now is wait for our task to finish. Then we can begin the next phase: closing our asynchronous loop.

At Lyst, we have a service that spews out emails when checkout processes have finished. It was quite easy for us to integrate an extra step that looked for a **callback\_url** or **push\_notification** attribute and then perform an action if they were present.

For **callback\_url**, we fire an HTTP request like so:

```
HTTP POST https://mywebsite.com/checkout_complete
{
    'status':'completed',
    'cart_id': 1234,
    'source_url': 'https://api.lyst.com/cart/checkout/'
}
```
Let's talk a little about the choices made for this request. We're using POST for two reasons:

1) We can't guarantee that the request is going to be safe, so it is better to use an unsafe HTTP method.
2) We can send a json body with status information back to the callback endpoint.

The final thing we need to do is make sure our request back to the client is actually received, otherwise we might fire back our "finished" request and the client could miss it! Luckily, each HTTP Request has to have an HTTP Response. We can keep sending the request every 10 minutes or so until we get back a 200 OK response. We're relying on the client to understand this needs to be done however, but a good bit of documentation and education will help solve that problem.

For **push notifications**, we generate a new push notification back to the mobile app telling them their checkout has finished.

### How This Improves The Service
This is a much better experience for the mobile app users, especially. The entire experience is now in-app. They don't have to exit and check their email, which we found can sometimes lead to a disjointed experience. Even the callback\_url formula provides a programmatic method of feedback on asynchronous processes.

Some consider the webhook method to be an extra step that feels slow, but I believe it is a much more integrated experience regardless of the extra time it takes.

The cool thing about this, is that it still keeps the requests and responses stateless, so HTTP stays happy.

If you've tried to provide asynchronous services through Web APIs, I'd love to hear how you did it. Shoot me a [tweet on twitter](https://twitter.com/phalt_)!
