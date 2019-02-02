---
title: "A simple API in Go"
date: 2015-07-25T10:49:06+01:00
draft: false
---

I recently delved into the world of [Go](https://golang.org). I wanted to try out a new programming language that wasn't Python and Go seemed like a great choice because it has:

- Built-in concurrency with ```go func()```
- Built-in package management with ```go get```
- A huge number of built in packages
- HTTP is super easy with it
- An amazing gopher mascot

![Gopher](http://i.imgur.com/ll0Pmra.jpg)

Now, as an API fanatic, I wanted to see how quickly I could build an API in Go that supports some RESTful ideas. The thing I noticed quickly about Go is that the 3rd party package ecosystem is extremely young, and a lot of the packages are poorly documented compared to the well established world of Python.

No matter, this would be a challenge!

# Setup

After [setting up my Go environment](http://golang.org/doc/install) and setting my [$GOPATH](http://golang.org/doc/code.html) I searched the web for some packages that would help me with HTTP routing and persistence.

I discovered [Gin](https://github.com/gin-gonic/gin), a microframework, that looked perfect for minimal HTTP APIs, and [Gorp](https://github.com/coopernurse/gorp) which is self labeled as an *ORM-ish for Go*.

I installed these using ```go get```:

	go get github.com/gin-gonic/gin
    go get github.com/coopernurse/gorp

and I created a new folder for my website with a ```main.go``` file in it:

	mkdir website
    cd website
    touch main.go


# URL Routing

The first thing I wanted to do was build a few routes using **Gin** and make sure they worked nicely, this was easy enough:

	package main

    import (
    	"github.com/gin-gonic/gin"
    )

    func index (c *gin.Context){
    	content := gin.H{"Hello": "World"}
        c.JSON(200, content)
    }

    func main(){
      app := gin.Default()
      app.GET("/", index)
      app.Run(":8000")
	}

What's going on here?

- We're importing Gin with the ```import``` statement.
- In the ```main()``` function we declare a new Gin app with the default settings.
- We add a simple **GET** route to ```/``` and link the ```index()``` function to it.
- The ```index()``` function builds some content and returns a ```200``` response with that content as **JSON**
- Finally, on the last line we tell our app to run on port 8000

If you run this with go:

	go run main.go

It should be viewable in your browser and with an HTTP request tool like [httpie](http://httpie.org):

	$ http localhost:8000/ --body
    {
    	"Hello": "world
    }

# Persistence

Before we can start creating, updating, reading and deleting resources, we need to be able to store them somewhere. The **gorp** package lets you interact with databases in an ORM-ish way. Let's set up a simple sqlite3 database and talk to it. Add these to your imports:

	import (
    	...
    	"database/sql"
        "github.com/coopernurse/gorp"
        _ "github.com/mattn/go-sqlite3"
        "log"
        "time"
        "strconv"
    )

 We'll be using all these things: the standard **database/sql** package, **gorp**, an easier **sqlite3** library, and the **log** and **time** libraries. Finally, to turn strings into integers and back, we've included the **strconv** library.

At the top of your file, just below the imports, let's declare a **global variable** called ```DBMap```:

	var dbmap = initDb()

and set up the ```initDb()``` function just below it:

	func initDb() *gorp.DbMap {
    	db, err := sql.Open("sqlite3", "db.sqlite3")
    	checkErr(err, "sql.Open failed")
    	dbmap := &gorp.DbMap{Db: db, Dialect: gorp.SqliteDialect{}}
   	 dbmap.AddTableWithName(Article{}, "articles").SetKeys(true, "Id")
    	err = dbmap.CreateTablesIfNotExists()
    	checkErr(err, "Create tables failed")
    	return dbmap
	}

    func checkErr(err error, msg string) {
        if err != nil {
            log.Fatalln(msg, err)
        }
    }

This will create a file called **db.sqlite3** in the route of our project. Don't run this yet, we need to create the ```Article``` resource we reference here, otherwise we'll just see some errors.

# Resources

Okay, let's get a bit more RESTful and set up an **article** resource with some appropriate REST-like URLS and HTTP methods. Change your ```main()``` function to this:

	func main() {
        app = gin.Default()
        app.GET("/articles", ArticleList)
        app.POST("/articles", ArticlePost)
        app.GET("/articles/:article_id", ArticleDetail)
        app.Run(":8000")
    }

 We've added the following routes:

 - ```HTTP GET /articles``` - Return all the articles in the list
 - ```HTTP POST /articles``` - Create a new article
 - ```HTTP GET /articles/<article_id>``` - Get back a single article via it's **id** attribute.

In Go, we use [**structs**](http://golang.org/ref/spec#Struct_types), which are a sequence of named elements or fields, which can have different types. A bit like a data model. This is the Article struct that we'll be using:

	type Article struct {
        Id int64 `db:"article_id"`
        Created int64
        Title string
        Content string
    }

It has a few basic fields - an **Id**, a **Created** value (which we'll store as a date), and a **Title** and **Content** string.

The URL Routing we just set up references a few functions that don't exist yet, so let's create them:

```
func ArticlesList(c *gin.Context) {
    var articles []Article
    _, err := dbmap.Select(&articles, "select * from articles order by article_id")
    checkErr(err, "Select failed")
    content := gin.H{}
    for k, v := range articles {
        content[strconv.Itoa(k)] = v
    }
    c.JSON(200, content)
}

func ArticlesDetail(c *gin.Context) {
    article_id := c.Params.ByName("id")
    a_id, _ := strconv.Atoi(article_id)
    article := getArticle(a_id)
    content := gin.H{"title": article.Title, "content": article.Content}
    c.JSON(200, content)
}

func ArticlePost(c *gin.Context) {
    var json Article

    c.Bind(&json) // This will infer what binder to use depending on the content-type header.
    article := createArticle(json.Title, json.Content)
    if article.Title == json.Title {
        content := gin.H{
            "result": "Success",
            "title": article.Title,
            "content": article.Content,
        }
        c.JSON(201, content)
    } else {
        c.JSON(500, gin.H{"result": "An error occured"})
    }
}
```

These "views" functions handle the CREATE and READ attributes we want for our API. I'll let you browse through the code leisurely , it should be pretty simple to understand.

The "view" functions call a few extra functions that handle the real resource creation, add them below the functions you just created:

```
func createArticle(title, body string) Article {
    article := Article{
        Created:    time.Now().UnixNano(),
        Title:      title,
        Content:    body,
    }

    err := dbmap.Insert(&article)
    checkErr(err, "Insert failed")
    return article
}

func getArticle(article_id int) Article {
    article := Article{}
    err := dbmap.SelectOne(&article, "select * from articles where article_id=?", article_id)
    checkErr(err, "SelectOne failed")
    return article
}
```

These just create new articles or get them from the database.

# Testing it out

Everything should be working lovely now! Try run it:

	go run main.go

If you want to see the entire code in one file, check out [this example on GitHub](https://gist.github.com/phalt/28d590eb916d5dca1a8f). With the server running, you should now be able to HTTP POST to create a new article:

	$ http POST localhost:8000/articles title="A simple API in Go" content="This is my content"

and get back the result:

	HTTP/1.1 201 Created
    Content-Length: 81
    Content-Type: application/json
    Date: Wed, 03 Dec 2014 14:28:22 GMT

    {
        "content": "This is my content",
        "result": "Success",
        "title": "A simple API in Go"
    }

Then query the entire list:

	$ http localhost:8000/articles
    HTTP/1.1 200 OK
    Content-Length: 105
    Content-Type: application/json
    Date: Wed, 03 Dec 2014 14:29:40 GMT

    {
        "0": {
            "Content": "This is my content",
            "Created": 1417616974905863050,
            "Id": 1,
            "Title": "A simple API in Go"
        }
    }

Or query a single resource:

	$ http localhost:8000/articles/1
    HTTP/1.1 200 OK
    Content-Length: 62
    Content-Type: application/json
    Date: Wed, 03 Dec 2014 14:30:32 GMT

    {
        "content": "This is my content",
        "title": "A simple API in Go"
    }

And there you have it: a simple HTTP API in Go, in just over 100 lines of code.

We're missing a few fundamentals, like HATEOAS here, but it should be enough to get you started.

Have any questions? Get in touch with me on [twitter](https://twitter.com/phalt_).

For further reading, checkout [this blog post](http://thenewstack.io/make-a-restful-json-api-go/), which goes into more detail and uses things like JSON API.
