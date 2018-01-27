Most services do way more than just CRUD. That is: Creating, Reading, Updating, and Deleting, objects in a database.

Services often keep data about events that clients want to know about. In the past, API services have provided access to these events via a “polling” API - the client makes calls every few seconds asking for the status, and acts depending on the response. This is generally a wasteful and messy way of sharing that information. Some events may only happen once in a blue moon, so you have to figure out how often to make the polling calls, and you might miss it.

> _The design of an API is as important to a developer as a webpage is to a user._

An alternative way of sharing the data about the events if for the __service__ to call the client when an event happens. The client only has to wait and act when it hears something. This method is referred to as a Webhook or Callback service.

There are many examples of this already - [Stripe](https://stripe.com) processes payments, [Mondo](https://getmondo.co.uk/docs) can inform you of transactions you make with your credit card, and [Twilio](https://twilio.com) can ask you how to handle phone calls.

![webhook](http://i.imgur.com/BPvnmk6.png)

## So How Do I Design A Webhook For My Service?

The aspect of HTTP APIs I am most passionate about is their **design** - how the API works from the point of view of the client. It's as important as the design of a webpage to a user. An API service is a product, and the interface should be treated like any other - it should make sense, it should be easy to use, and it should be well documented.



How does a well designed webhook interface work?

Let's figure this out by splitting the Webhook service in three areas and talking about each of them:

1. Letting clients create new webhooks subscriptions.
2. Letting clients manage their webhooks.
3. Sending webhook payloads to clients.


## Creating New Webhooks Subscriptions

For creating new webhooks subscriptions, treat the *subscription* like any other resource in an HTTP API.

If your service is pretty simple and the webhooks you want to create are all of a similar type (for example, email delivery notifications) then creating a `api/webhooks` endpoint is a good option. Accepting `HTTP POST` requests to create new resources is easy to implement:

```
HTTP POST /api/webhooks
{
...post data...
}
```

[Mailgun](https://documentation.mailgun.com/api-webhooks.html#webhooks) and [Mondo](https://getmondo.co.uk/docs/#web-hooks) provide this type of interface for their APIs.

If your API service wants to provide webhooks for distinctly different things, then separating them into different endpoints is good practice.

_Handle phone calls_
```
HTTP POST /api/webhooks/calls
{
...post data...
}
```

_Handle messages_
```
HTTP POST /api/webhooks/messages
{
...post data...
}
```

This allows the client to subscribe describe endpoints in their application to different web hooks, instead of having to process all types of webhooks at one endpoint.

### What Information Should The Client Send?

At the very least, your webhook endpoint should require a `callback_url` parameter. This should be an absolute URL in the client application.

Some services suggest sending an `id` or `webhook_id` which can be used by the client if they receive multiple webhooks. You might also want to send an `account_id` or some way of identifying the user who is creating this webhook. However, that can be inferred from the authentication token.

Apart from that... there isn't much more that's really needed from the client at this stage. Of course, you'll want to be able to send authentication tokens and the usual stuff. But specifically for the webhook to work effectively, you only really need the following:

![client_attribs](http://i.imgur.com/pOf7oTD.png)

### How Should Clients Manage Their Webhook Subscriptions?

When creating new webhooks, the immediate response from a successful POST request should contain a hypermedia link or at least an ID. This can be used by the client to refer back to the Webhook resource they just created. This is just like any other traditional RESTful-style resource in an API. In fact, at this point, it should behave just like any other resource:

```
# List all my webhooks
GET /api/webhooks
# Get a single webhook
GET /api/webhooks/:ID
# Remove a webhook
DELETE /api/webhooks/:ID
# Update a webhook
PUT /API/webhooks/:ID
```

### What Should I Send In Webhook Payloads?

This section is where things get interesting. A good webhook service should provide as much information as possible about the event that is being shared, as well as additional information for the client to act upon that event.

Payloads should include a `timestamp` and `webhook_id` if one was given by the client when creating it. A `type` attribute should be included if you're providing different types of webhooks to a single endpoint. [Stripe's event types](https://stripe.com/docs/api#event_types) are a good example of this done well.

The best way to provide the data that is specific to the context of your application, is by wrapping it in a `data` attribute. Don't have this at the top level of the response. Wrapping it up separates it from the application-specific content and the content that is informational for the API client.

```
# Webhook payload for a newly created user
{
"timestamp": "2016-01-26T18:14:10.698154",
"type": "user.created",
"webhook_id": "clients_id",
"data": {
    "username": "janedoe",
    ...
  }
}
```

The client should respond with a 200 HTTP status code to confirm it has received the payload. A good webhook service will assume any other response a failed delivery and will queue the request to be sent again at a later date. If the client continues to not reply with a 200 OK response, the Webhook service should wait exponentially longer. After the fourth or fifth request the client is probably not working and the Webhook services should stop trying to talk to it.

![webhook_payloads](http://i.imgur.com/ydp37PF.png)


### Going forward

We've covered the very basics of Webhook design in this blog post to help get you  started. The best next steps would be to look at the following:

- __Documentation__ - dangerously important and something you'll always want to provide. Why not write some now? Your designs could just be your docs!
- __Security__ - not just for talking to your API, but so your service can prove to the client they're the real deal. Look at [Amazon's guide to verifying signatures](http://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.verify.signature.html) or [Mandrill's documentation](https://mandrill.zendesk.com/hc/en-us/articles/205583257-How-to-Authenticate-Webhook-Requests) on authenticating webhook requests.
- __Programming__ - despite the lack of good tutorial content on webhooks, there are quite a few third party libraries for popular frameworks. Get on [GitHub](https://github.com) and see what's available for your favourite language.

If you've had any experience creating Webhooks before, or want to share your thoughts, then you can find me [on twitter](https://twitter.com/phalt_) or leave a comment below! :)
