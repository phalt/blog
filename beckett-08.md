Beckett has been out for a little over three weeks now. It's had a lot of good feedback from the Python and API community which has helped with bug fixing and figuring out what people want from an API Client library.

![yay_beckett](https://media.giphy.com/media/3avO0QNTQKO8o/giphy.gif)

I've been doing small patches to fix bugs, but I recently added a few big features that prompted a minor version update to **0.8.0**, you can install it using pip:

```python
pip install beckett==0.8.0
```

Let's go over the bug fixes and features between 0.7 and 0.8

# Bug fixes

- Missing default values `valid_status_codes` and `pagination_key` used to cause errors. It now supports defaults. [#32](https://github.com/phalt/beckett/pull/32)
- If no `uid` parameter was supplied to the generated `get_`, `put_`, `patch_`, and `delete_` methods, then it would not error. It now properly raises `MissingUIDException`. [#34](https://github.com/phalt/beckett/pull/34)
- For hypermedia related resources where the URL is just a string, you no longer need to supply the uid, just call the method. [#36](https://github.com/phalt/beckett/pull/36)
- If a HypermediaResource had more than one related resource, the generated methods for each would point to the same method in memory and just not work. That's fixed now. [#40](https://github.com/phalt/beckett/pull/40)

# New Features

## Hypermedia Improvements

The support for Hypermedia in Beckett has improved greatly. Responses that return a list of links or an individual link will intelligently transform into a method.

Consider the following JSON response for a "Book resource":

```json
{
    "author": "http://myapi.com/authors/1",
    "title": "A Farewell to Arms"
}
```

The resulting `BookResource` instance from this response will have the method `get_authors()`, which returns an `AuthorResource` instance:

```python
book.get_authors()
>>> [<Author | Hemmingway>]
```

If there is a list of many related links, Beckett will now take each link and resolve it into a resource.

So for the following JSON where `authors` is a list:

```json
{
    "author": [
        "http://myapi.com/authors/1",
        "http://myapi.com/authors/3"
    ],
    "title": "A Farewell to Arms"
}
```

Then Beckett will return the list of `AuthorResource` instances:

```python
book.get_authors()
>>> [<Author | Hemmingway>, <Author | Fitzgerald>]
```

Beckett 0.8 has also had small fixes to ensure the algorithms generating methods works more efficiently.
## SubResources

One feature request for Beckett was to be able to provide types for nested sub-resources in responses. Beckett 0.8 now allows you to set `subresources`.

Consider the following JSON response for a "Book resource":

```json
{
    "author": {
        "Name": "Earnest Hemingway",
    }
    "title": "A Farewell to Arms"
}
```

In Beckett 0.7, `author` would be set as a dictionary. However, it would be nice to type this dictionary as an `Author`. By setting the new `subresource` attribute on Resources in Beckett, we can give it a type:

```python
class AuthorSubResource(SubResource):

    class Meta:
        name = 'Author'
        identifier = 'name'
        attributes = (
            'name',
        )


class BookResource(BaseResource):

    class Meta(resources.BaseResource.Meta):
        ...other attributes...
        subresources = {
            "author": AuthorSubResource
        }
```

This will turn the `author` property on the BookResource into an `AuthorResource` instance.

We've also added a **simple [SubResource](https://phalt.github.io/beckett/resources/#class-subresource)** class, which provides just typing and property assignment, if you do not want to provide URL generation.

All Resource classes can be used in the `subresource` attribute, so it offers up a lot of flexibility for composing rich interfaces to your API.

## Better Exceptions

Finally, we've improved the exceptions that Beckett raises when it receives invalid status codes.

You can set the desired status codes for a resource using the `valid_status_codes`, and the exception returned when the HTTP response does not match is now much more useful:

```python
try:
    client.get_book(uid=1)
except InvalidStatusCodeError as e:
    # Check the status code in the error:
    if e.status_code == 404:
        # Handle 404 responses
```

That's it for now. The next thing I want to work on is handling JSONAPI and HAL formats. So look for a 0.9 version soon!
