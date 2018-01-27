I wish I could make a career out of building novelty APIs because I love seeing the reaction from people when I announce a funny or nerdy thing that I'm working on.

The [Star Wars API](http://swapi.co) was an idea I came up with a few months ago when someone asked me what I was planning on turning into an API next. We'd been chatting about the almost unbelievable success of [PokéAPI](http://pokeapi.co), an API I made for Pokémon that had served over 13 million requests in one year.

I'm a big Star Wars fan: I've got the 1997 releases on VHS, the Bluray releases (two copies, actually) and loads of extended universe books. My room is decorated with Lego Star Wars ships and minifigures.

My Brother and I once got Star Wars Trivial Pursuit for Christmas. We went through the entire stack of questions and answered them all correctly the first time. I have watched the Episode VII trailer about fifty times.

I'm a big Star Wars fan.

So to me it seemed obvious to merge that nerdy love with my fanatical obsession for building APIs. I decided to build a simple sign up page about my idea and if I got 10 to 20 people thinking it was cool or funny then I'd build it.

I didn't expect this:

![crazy_numbers](http://i.imgur.com/EyjLbhH.jpg)

It blew up. I always wondered what it felt like to be "Hacker News-ed" and I can tell you it's a combination of mostly fear and some excitement. The excitement rises from knowing you've just made or suggested something that people enjoy.

The big fear stuff is probably a result of imposter syndrome:

*What if people start critising it? What if the comments are bad? What if someone spots a mistake? What if the site goes down?*

It's an nerve wracking and scary thing to go through sitting alone in a coffee shop whilst hundreds of people looked at the thing you just spent 8 hours building. I had planned lunch with a friend that afternoon and I had to cancel because *"things got a big crazy on the web"*. He saw it so he understood.

After one week of almost constant emails and twitter notifications I had some stats to pour over and ask myself *"what have I done"*:

- 17,000 unique users
- 400 peak concurrent visitors
- 1,000 sign ups

Thank you to everyone who commented, tweeted, and signed up. With these results I can definitely say that **people would be interested in using a Star Wars API**. Also props to Heroku which managed to take this traffic on just two dynos and still didn't charge me anything.

# So what now?

Now I have the fun task of building an API for Star Wars. I have limited the data set to just a few resources for now:

- People
- Planets
- Starships
- Vehicles
- Species
- Films

I consider these to be the main quantifiable data points in Star Wars. I've also decided to limit the data to the **first six films**. I will be adding data from Episode VII as it starts to come out. Adding in the (now non-canon) extended universe will introduce conflicts and errors which I can only imagine would be a data scientists nightmare. So let's just stick to what we know and what is consistent within the movies.

Collecting this data has involved watching the movies one by one and pausing each time a new piece of data is shown. I search Wookiepedia or just Google to gather more technical information, such as the population of a planet.

<blockquote class="twitter-tweet" lang="en"><p>Research. <a href="http://t.co/J6sBZXikR6">pic.twitter.com/J6sBZXikR6</a></p>&mdash; Paul (Pool) Hallett (@phalt_) <a href="https://twitter.com/phalt_/status/542695514685595648">December 10, 2014</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Because this data has never been quanitifed in an API before I've had to make a few assumptions so that it formats nicely and doesn't get too confusing to use. Most of these are novel and very specific to the Star Wars universe so I thought I'd share the interesting points that I've had to contend with when formatting this data.

# Dates
All dates within the Star Wars universe will be **BBY** (Before Battle of Yavin) or **ABY** (After Battle of Yavin), which is the big fight at the end of episode IV. This is the same scale used officially within the universe. I don't think it is a canon scale anymore but any fan of Star Wars knows the Battle of Yavin.

# Real Estate Hazards

![alderaan_boom](http://media.giphy.com/media/H0J0d5U7uWNY4/giphy.gif)

Are [Alderran](http://starwars.wikia.com/wiki/Alderaan) and [Polis Massa](http://starwars.wikia.com/wiki/Polis_Massa) still considered a "planet"? At the time of 0 ABY they are both asteroid fields. I think for the purposes of this API they will have their original planet data. I might decide to add additional resources for their state after their calamaties.

# A ship that small can't have a cloaking device!

Distinguishing between **Starship** and **Vehicle** has been tricky. I initially went with the mass of the ship and declared:

*"Anything smaller than the Millenium Falcon is a vehicle, anything bigger a starship"*.

But would you consider the [X Wing](http://starwars.wikia.com/wiki/T-65_X-wing_starfighter) and a [74-Z speeder bike](http://starwars.wikia.com/wiki/74-Z_speeder_bike) the same thing? Probably not. I quickly realised one major distinction between the transport technology in Star Wars: **Hyperdrive capability**. This will be the main distinction between a Starship and a vehicle, with a sub-type attribute on each class like "corvette" or "starfighter".

![vader_spinning](http://media0.giphy.com/media/1pVhAOYf1J00U/giphy.gif)

# Chancellor Palpatine is a Sith Lord?

Little naming decisions, like "Obi-Wan Kenobi" or "Ben Kenobi", "Emperor Palpatine" or "Darth Sidious" boil down to this:

Which is the most frequently used in the films? In this case, Obi Wan Kenobi and Emperor Palpatine trumps.

# No, I am your father

Some characters can have slightly differing names, but there is person that are obviously two separate characters: Anakin Skywalker and Darth Vader.

I've decided to make this distinction because of the obvious differences in their height, appearance and general attitude towards life. Darth Vader even refers to Anakin Skywalker as if they are another person.

But this distinction is important also because of Star Wars lore: for those who grew up with the original trilogy Anakin and Darth Vader *were* different characters. At least until the biggest plot twist in cinema history at the end of Empire Strikes Back.

I know this is a really tricky subject to deal with and I expect plenty of nerd rage for my decisions, but I'm trying to be pragmatic and also logical at the same time. This is a data set that requires clear definitions, it doesn't understand how to deal with "what if's" and "yeah but" issues.

# These aren't the droids

They may be shiny rusty and hugely impractical (wheels?! On a dessert planet?!) but they're definitely sentient enough to be considered a species, so droids will be classed under that resource.

# Background characters

![helmet_knock](http://media3.giphy.com/media/ay2rVmZgxsoSs/giphy.gif)

*(Did you know they added the noise of this helmet bash in the bluray editions?)*

Basically, I'm going to filter out any character that isn't explicitly named in the film credits or on the [Star Wars wiki](http://starwars.wikia.com/), because otherwise the People resource will be huuugge.

Characters like [TK-421](http://starwars.wikia.com/wiki/TK-421) (who wasn't at his post) will be omitted. But people like [Mon Mothma](http://starwars.wikia.com/wiki/Mon_Mothma) will be added because they have long speaking lines and are relevant to the main plot.

The Star Wars wiki logs **absolutely everything** about Star Wars. Each and every insignificant character is documented and has a background. This API for now is limiting it's scope purely because of the time and effort required to reformat that data into an API. After dealing with the main movies, I will certainly love to add new resources and extra characters, ships and planets.

# Progress

I've just finished the second movie (episode V) and have started to add the data from it. The application infrastucture is very barebones at the moment. This weekend I aim to add Episode VI data and then tidy up the API as it is currently messy. I'm aiming for a beta next week. If you head over [to the website](http://swapi.co) you can sign up for early access!

These are just some of the assumptions and decisions I've had to make building an API for Star Wars. If you've got your own opinions on my choices then please shoot me a [tweet](https://twitter.com/phalt_). I am certain, as a Star Wars fan, that you will be contacting me to debate over these points :)

With the relase of the beta I'll also turn the source code into an open source project on GitHub, so issues can be raised there too.
