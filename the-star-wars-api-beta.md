After hours of tedious film watching and crawling the internet for any information I could find on the most insignificant of vehicles or planets, I've finally launched the [first version of The Star Wars API](http://swapi.co).

[![swapi](http://i.imgur.com/PqkJzx4.png)](http://swapi.co)

With the beta out in the wild I expect to spend the next few weeks fixing small data inconsistencies, deciding what data to add next, and battling all manner of bugs that I am no doubt going to have to deal with.

But in the mean time, let's talk about the cool stuff.

## What can I do with this data now?

Well, you can find out all sorts of crazy stuff now that the data is quanitifed. You could use standard HTTP requests, but with the [python helper library](https://swapi-python.readthedocs.org/en/latest/readme.html) you can get started doing actual data comparison.

**List the planets in order of size:**


    import swapi
    for planet in swapi.get_all("planets").order_by("diameter"):
        print(planet.name)


**View the people who have piloted more than one starship:**

    import swapi
    for people in swapi.get_all("people").iter():
        if len(people.starships) > 1:
            print(people.name)

**Discover if Jar Jar Binks ruined a film just by being in it:**

    import swapi
    pm = swapi.get_film(4)
    jj = swapi.get_person(36)
    for c in pm.get_characters().iter():
        if c.name == jj.name:
            print("Why George, why.")

There is also a cool little feature for printing out the opening crawl of each film, if you want to:

![opening_crawl_code](http://i.imgur.com/l02u363.gif)

If you're really itching to do something useful or cool why not build a helper library in your favourite language? Let me know when you finish it and I'll add it to the list of helper libraries on the [documentation page](http://swapi.co/documentation).

## Help me Star Wars community, you're my only hope!

I know that the data is likely to upset or disagree with a few devout fans. I'm pretty serious about my Star Wars lore, so I know how you feel. If you believe I've made a mistake or I'm missing a data point that just absolutely must be on swapi, then [tweet me](https://twitter.com/phalt_) or [email me](mailto:paulandrewhallett@gmail.com) and I'll get around to adding it or fixing it.

I had to make a few tough decisions when adding in the data for the beta but I **am adding more** so relax, your favourite EU characters will be in their eventually.

In the mean time, back to tinkering...

![han_grin](http://i.imgur.com/Qa8GVPU.gif)
