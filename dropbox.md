Dropbox recently announced their [brand new Web API](https://blogs.dropbox.com/developers/2015/04/a-preview-of-the-new-dropbox-api-v2/) which allows developers a programmatic way of adding file syncing and cloud storage functionality to their own software. I have used their old API before and I had hoped that this new web API would be better built and much easier to use and it does look much simpler at first. They've definitely made improvements.

*However*, it seems that Dropbox has forgotten one fundamental set of rules that is important for modern Web API services: **HTTP**. In fact, their blog post about the new Web API seems to deliberately try to simplify HTTP.

HTTP is a set of **standards** with **rules** that developers should follow to create great web services and I can't help but feel that Dropbox has just completely forgotten about this. I'm not going to speculate just yet on why Dropbox chose the path they did for building this new service and I'll happily concede if the reason is obvious and I've missed the point. But HTTP's rules are rules for a reason and it's sad to see such a great service provide something which basically breaks the standards of the web.

## How I Would Build Dropbox's New Web API

I want to suggest some changes to the new Dropbox web API to show how it might look if it was using HTTP nicely. Dropbox mentioned feedback in their blog post, so hopefully this will help them steer their service towards something more suitable for the web. These are speculative suggestions based on my knowledge of HTTP and resource-based web services.

### URI Routing and HTTP Methods

Dropbox's new Web API is using an RPC-style URI scheme which contains a set of methods. Here is an example request to get folder details of a certain path on Dropbox:

```
curl -X POST https://api.dropbox.com/2-beta/files/list_folder  \
    --header "Authorization: Bearer <access-token>" \
    --header "Content-Type: application/json" \
    --data "{\"path\": \"\"}"
```

For a non-HTTP based API service, this RPC style is perfectly fine. RPC conforms nicely to other transport protocols, it existed before HTTP. But it does not conform well to HTTP.

The example above is breaking two rules of HTTP.

In HTTP, a URI path should be used to address a resource. **The problem is this: they are not using the URI to address a resource, they're addressing a method instead**.

Why is this bad? HTTP supports methods already (I'll get to that in a minute) but it also makes caching on URI's much harder. If you wanted to get the folder details of a file path, you would use the URI above. If you wanted to get the folder details of a *different* path, you would use the exact same URI. If there is any caching between you and Dropbox's service then the second request might not give you the response you expect. It is too much to ask to assume that Dropbox and every node between you and their service *isn't* caching requests and responses.

The second breaking rule is related to HTTP Methods (the POST bit). HTTP methods let the server and client know what type of action is being performed. It allows the client to know the HTTP request is going to change some state, or do something [safe or unsafe on the server](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

It also allows you to stop re-inventing the wheel! I know HTTP's methods are limited, but standard CRUD-style methods map nicely to the HTTP methods so you should use them where applicable.

Dropbox's current API is effectively telling HTTP that _"I am unsafely requesting details about a resource on the server even though no state will change."_.

Methods and URIs separate addressing of resources and actions performed on resources for a reason. Dropbox give no anecdotal reason why they're only using one HTTP method. Simplicity isn't a good enough reason.

Here is the same example I've written conforming to HTTP:

```
curl GET https://api.dropbox.com/2-beta/files/<folder_path> \
	--header "Authorization: Bearer <access-token>" \
    --header "Content-Type: application/json"
```

The major difference here is that we're moving the path of the file or folder into the URI. I argue this style because URIs address file paths on a server anyway, so why not just mimic the Dropbox file system with it? We're also using a **safe** GET method: the server and the client now know their action isn't going to change any state and will always return a safe result.

(Note: the `--data` attribute in Dropbox's example might be the reason they're using HTTP POST, as you shouldn't send POST parameters in a GET request.)

Let's fix another of their example requests. This one gets the account details for a user:

```
curl -X POST https://api.dropbox.com/2-beta/users/get_current_account \
    --header "Authorization: Bearer <access-token>" \
    --header "Content-Type: application/json" \
    --data "null"
```

Again, this is using a POST method to *get* something from the server. They're also using a `--data "null"` header here which is frankly quite weird: why do I have to explicitly say it is null? Can I omit it?

Here's a more HTTP-friendly alternative:

```
curl GET https://api.dropbox.com/2-beta/users/<user_id> \
    --header "Authorization: Bearer <access-token>" \
    --header "Content-Type: application/json" \
```

We now have:

1) A safe HTTP method
2) URI addressing which will cache properly.
3) and no more redundant `--data "null"` header.

### Status codes

Dropbox have [talked about using fewer status codes before](https://blogs.dropbox.com/developers/2015/04/how-many-http-status-codes-should-your-api-use/) and whilst I'm an advocate for pragmatism and minimalism in interfaces, I believe being verbose is necessary for errors.

Dropbox's new API uses **only one** status code for errors: [409 CONFLICT](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.10). Extra error details are present in the response body.

The [HTTP standard](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.10) has this to say about 409 CONFLICT:

>_"This code is only allowed in situations where it is expected that the user might be able to resolve the conflict and resubmit the request"_

Awesome! This is a pretty good catch-all error code for a Web API service. It's definitely likely in a 4\** response that the developer is going to be able to figure out what is wrong. So it could be used for almost anything.

There are two caveats here though: server errors and existing error codes.

Dropbox doesn't explain what happens if an error occurs on **their** server, which would usually elicit a 5\** response to  the client.

If I never receive a 5\** error when the server breaks then I'm going to keep trying because the response is telling me that **I'm** wrong and not the **server**. It is nice to think that Dropbox's service will never have errors, but in the edge case where it does I would hope to get the correct error response so I know about it.

### Extending status codes

Dropbox is doing a great thing by extending the standard status codes so they can provide errors that relate to Dropbox's service. Other API's like Twilio do this as well and it's a great idea. But I don't fully agree with replacing the standard status codes.

In the user details request above, what type of response am I going to expect if a user does not exist? The way Dropbox have described it, I'd probably get something like this:

```
HTTP 1.1 409 CONFLICT
Content-Type: application/JSON

{
	'error_code' : 12345,
	'error': 'This user does not exist'
}
```

In an HTTP-complaint Web API, I'd expect to see this response:

```
HTTP 404 NOT FOUND
```

Why do you need to recreate an error code that already exists?

Finally, why are they using the 409 CONFLICT status code for their catch-all? If I was building Dropbox's new Web API, I'd try to use some of the more common status codes, such as 404 NOT FOUND and any other error responses would be built in the current way but under the **[400 BAD REQUEST](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.1)** response instead.

Using a 400 BAD REQUEST error response has two benefits: a lot of other great Web APIs use this format already, so developers will be used to seeing it, and the HTTP standards say this response should be used when _"The client SHOULD NOT repeat the request without modifications"_, which a client is almost always likely to do anyway when they see this error.

The same idea also applies on successful response codes such as the 2\** range.

The response **200 OK** is not descriptive enough to describe every action performed. Using a few more responses like 201 CREATED is much more verbose. Then if Dropbox needs to provide a service-specific response they can use the normal 200 OK response with some extra status details in the response body.

### Wrap up

I **love** Dropbox's service and I recommend it to anyone looking to store stuff in the cloud. (I even convinced my non-technical parents to use it). I also know they've got a great bunch of people working there. I'd love to see them put a bit more thought and care into building an API service for the web that is complaint with the standards of the web.

I know there is a big _"HTTP is limited"_ argument but that's no excuse for throwing out the things that make sense. And if it's speed / connection time that you're concerned about, HTTP2 is introducing multiplexing very soon.

After writing this, I can think of two ways why the new API looks like it does:

1) The Web API maps closely to the internal services of Dropbox, so it is much easier for Dropbox's developer to map the Web API service to their internal APIs.

2) The Web API team haven't approached the design from a user point of view.

The Web API service **does not** have to mimic their internal service. The Web API should be a set of HTTP-complaint interfaces that connect to a magic box that is the Dropbox service. Looking at existing examples of great Web API services such as Stripe and Twilio should point them in a better direction. This is an opportunity to take their existing service and build new interfaces with the lessons they've learned from building their internal services. Heck, if it is good enough, they might even be able to use their own service too.

I'd love to get your feedback on my suggestions, and do tell me if I'm wrong. [Tweet me](https://twitter.com/phalt_) and let's chat :) (Especially if you work at Dropbox!)
