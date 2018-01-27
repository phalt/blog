Over the past few months I've been trying to hone my Django skills and improve how efficiently views load. A big part of this is reducing database queries, especially unnecessary ones.

Whilst the following may be trivial to Django experts, if you're knew to Django and want to quickly learn a few nifty tricks for reducing database queries and page load times, read on.

## Use count() instead of len()

A simple trick I learned when I was calling *len()* on a QuerySet that returned over 46,000 entries (and thus took ages). Using *count()* instead performs an *SQL COUNT(\*)* command on the table, which is much faster:

    User.objects.count()
    >>> 43

However, if you know you'll need to use those 43 fields later on, using ___len()___ is acceptable. Why? Because *len()* will evaluate (execute) the SQL SELECT statement and dump it into memory. In the case where you're determining what to do based on the number of fields returned I'd suggest using *count()* always.

This can also be used nicely with filters:

    User.objects.filter(username='Paul').count()
    >>> 1

This is a good way to determine if a particular field exists (such as the User called Paul), but a much nicer way to do this is with *exists()*.

## exists() checks something is there

Why is this much nicer than count()? It returns a bool:

	User.objects.exists()
	>>> True

This executes a very similar QuerySet, which is slightly faster than using something like this:

	bool(User.objects.all())
	>>> True

However depending on how large your QuerySet is, it might be easier to just use *bool()*.

Just like *count()*, this ties in nicely with filters:

	User.objects.filter(username='Paul').exists()
	>>> True

The larger and more complex the QuerySet (especially where a lot of fields are returned) means the more and more efficient exists() becomes. A simple statement like the one above might not be that much faster, so do some testing first.

## Make less queries with select_related()

The QuerySet function selected_related grabs every foreign-key and one-to-one relationships for the object you're querying. For example:

	b = Blog.objects.select_related.filter(title='Hello, world!')
	# This does not make another call on the database
	c = b.comments

This is especially handy if you know you'll be using the related objects. I wouldn't recommend using this otherwise. It is important to take note that sometimes this can cause a very large single database query!

## Use prefetch_related() to grab M2M relationships

This works exactly like *select_related()* but with two notable exceptions:

- It can grab many-to-many relationships.
- It joins everything together in Python after the database query.



```
Blog.objects.all().prefetch_related('authors')
```   
Django has a huge number of helpful functions for its QuerySet API. I'd suggest to any Django developer to [look through them](https://docs.djangoproject.com/en/1.7/ref/models/querysets/), you'll probably be able to find something to help drastically improve your project that you didn't even know about!
