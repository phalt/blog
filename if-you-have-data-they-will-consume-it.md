PokéAPI, the [REST API for Pokémon](http://pokeapi.co), is a project I started in October 2013 with the intention of demonstrating good [RESTful practices](http://phalt.co/rest-digested/).

In the space of 48 hours I built a really hacky application and stuck it online on a tiny little Digital Ocean droplet.

[![PokeAPI](http://i.imgur.com/lJVaCxD.png)](http://pokeapi.co)

Because of the data it was providing, it gained popularity very quickly. There are hundreds of websites about Pokémon but none of the data they had was consumable through an API.

The weekend after I launched the project, I spent all my time learning about caching, distribution, and how to handle heavy traffic. I did not expect the application to get so popular. It was a pretty hectic time. It was fun.

I think one of the reasons it ended up processing so many requests was because of something my friend told me recently:

<blockquote class="twitter-tweet" lang="en"><p><a href="https://twitter.com/phalt_">@phalt_</a> <a href="https://twitter.com/PokeAPI">@PokeAPI</a> It&#39;s a great example of how if data is there, people will use it.</p>&mdash; Claire / &#39;Chad&#39; (@kitation) <a href="https://twitter.com/kitation/status/532218040495382529">November 11, 2014</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

I think Chad has a really good point: No matter the data, even if it's only usable by 2 or 3 people *someone will consume it*. It's an idea I've thought about for a long time and it is nice to see friends who I hold in high regard thinking of the same thing.

# PokeAPI Version 2

Since October 2013 PokéAPI has served over **13.5 million** API requests, averaging **35 thousand** API calls a day. It has been the source of a number of [cool](http://www.polygon.com/2014/6/27/5850720/pokemon-battle-slack-vox) [applications](https://www.twilio.com/blog/2014/11/build-your-own-pokedex-with-django-mms-and-pokeapi.html) and has even been used in [school lessons to help teach APIs](http://projects.codeclub.org.uk/en-GB/06_python/04/Pokedex.html).

Here's a chart of the requests for each day, with a yellow growth trend line:

![chart_one](http://i.imgur.com/O3Vyt2a.png)

I really wish this wasn't a joke: some days, people on the internet made nearly 300,000 requests for data about Pokémon.

Pokémon.

Now, I have a confession: **I never planned to build something that would be used so much**. Because of this I've negelected the project and left out quite a lot of data. For example, generation 6 Pokémon (there have been that many) don't have any attributes at all. I never planned to set any time aside for adding new content and I've had hundreds of personal emails asking me to update it. I feel awful for neglecting what is obviously a beloved and fun API.

I'm sorry folks.

So, I think it's time I did something about it. Today I am announcing that **PokeAPI will become an open source project** and big updates will be made over the next few months which will include:

- Putting all the source code online.
- Building a 100% Test Coverage test suite.
- Building a new version of the API under ```api/v2/``` that will have some slightly reformatted data and new resources.
- Using [Veekun's Pokedex GitHub data](https://github.com/veekun/pokedex) for V2 of the API with data models that map closely to their database.
- Deprecation of the **V1** API in January 2015.
- Content update to support the latest release of games.

# Deprecation?

Deprecating the V1 API will mean that no more data or changes will be made to it as of **January 1st 2015**. It will be put into a stable state and after that no more content or data will be added to it. Instead, all new development and new data will be added to the **V2** API.

From January 2015 an extra HTTP Header will be in the responses from the API recommending people upgrade.

# Contributing

The project can be forked [on GitHub right now](https://github.com/phalt/pokeapi). If you want to contribute, look at the extensive list of [issues that I have addressed](https://github.com/phalt/pokeapi/issues) there are a tonne that I need help on.

I will be dedicating the next 6 weeks of my time towards building this so if you want to contribute or support me, feel free to donate through PayPal:


<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="\_top">
<input type="hidden" name="cmd" value="\_s-xclick">
<input type="hidden" name="encrypted" value="-----BEGIN PKCS7-----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-----END PKCS7-----
">
<input type="image" src="https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif" border="0" name="submit" alt="PayPal - The safer, easier way to pay online!">
<img alt="" border="0" src="https://www.paypalobjects.com/en_GB/i/scr/pixel.gif" width="1" height="1">
</form>


This will help me keep the server up and running which so far I've been paying for out of my own pocket. I am also leaving my current role at Twilio to pursue more technical roles, so I can dedicate every day to this if I can afford to.


# A long time ago...

I've always said there should be APIs for everything. As I said earlier:

***"Any large data set, when easily accessible through an API, will be consumed by at least one developer."***

So I'm starting a new project that I will be working on alongside the V2 updates for PokeAPI. The title of this post probably gave it away already...

[![The Star Wars API](http://i.imgur.com/PqkJzx4.png)](http://swapi.co)

The Star Wars API will provide a RESTful API for **Planets, People, Starships, Vehicles, Species and Films** from the six major Star Wars films. Why just these films? There is much [dispute about canon](http://starwars.wikia.com/wiki/Canon) and I believe George Lucas recently said that the major films would be considered cannon. To avoid data clashes and weird hacks to make things match up properly, I'll just keep it to the major films. For now.

In the same style as PokéAPI **SWAPI** will be a completely open API with generous rate limiting and a REST-ish design. What's more **SWAPI** will have some interesting Resource relations such as "Pilots" on Vehicles:

```
HTTP GET http://swapi.co/api/vehicle/1

HTTP/1.1 200 OK
...
{
	"vehicle_id": "1",
    "name": "T-65 X-wing",
    "common_name": "X-wing",
    "pilots" : [
          {
              "name": "Luke Sykwalker",
              "people_id": "1",
              "url": "/people/1",
          },
          {
              "name": "Wedge Antilles",
              "people_id": "34",
              "url": "/people/34",
          }
    ],
    ...
}

```

and "Films" on planets:

```
HTTP GET http://swapi.co/api/planets/7

HTTP/1.1 200 OK
...
{
	"planet_id": "7",
    "name": Mustafar,
    ...
    "films": [
    	{
        	"film_id": "3",
            "name" "Revenge of the Sith",
            "url": "/films/3"
        }
    ],
    ...
}

```
It should be a pretty fun API to build as no easily formatted data for this data exists yet. I might end up scraping [Wookiepedia](http://starwars.wikia.com) or just watching the films over and over to get the right content.

Want to help out? You can [sign up](http://swapi.co) to get early access to the API which will be coming in the next few months. I'd love some support so [email me](mailto:paulandrewhallett@gmail.com) if you want to gather data for me or help me build the API.

*( Also, depending on how people feel, I might erase episode 1 from the database completely. :D )*

And yes: SWAPI **will** include data from [Star Wars episode VII: The Force Awakens](https://www.youtube.com/watch?v=erLk59H86ww) once I or my other data gatherers can get valid information on it.

If you've got any questions about all this contact me via [email](mailto:paulandrewhallett@gmail.com) or [tweet me](https://twitter.com/phalt_)

Here's to the next six weeks!
