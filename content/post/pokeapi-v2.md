---
title: "PokéAPI V2"
date: 2015-07-25T10:49:06+01:00
draft: false
---

Over sixteen months after I promised to make the new Pokémon API, it has finally been finished!

In December 2014 I released [The Star Wars API](https://swapi.co) and [said](http://phalt.co/if-you-have-data-they-will-consume-it/) that I would try to bring out a new version of PokéAPI with all the data from the computer games. I immediately lost interest as I started a new job, and about four months later I almost totally forgot about it.

## A Note On Open Source

I want to quickly explain why it took so long to finish this project.

Something I didn't realise about making commitments is that you really, _really_ need to make sure you're committing to something you _want_ to do. Open source projects suck up a lot of time, especially when there is more than two people using a project or who have input on a project. This project isn't even that big, it's not Django or Ruby on Rails, and it was already difficult to respond to all the questions. It's especially hard when people suggest things like:

> Your project is missing this data add it already.

or

> This thing is broken why hasn't it  been fixed.

Well,  why don't you? It's open source and you already know what's wrong. Make a pull request!

Please don't misunderstand here, I'm not being lazy. I opened the project to allow others to contribute because that is what people wanted, and then I continued to get people asking me to do it all. 😞 Sometimes you get these moments of inspiration and you can sit and write code for hours. Sometimes the complete opposite can happen, and for me that happened for a long time.

### Open Source Support

Alas! It actually when quite well when a few people started to contribute to the V2 build. I had written a bunch of tickets outlining the general requirements, and one person in particular started working on this - [Zane Adickes](https://github.com/zaneadix).

Zane actually ended up building 90% of the rest of the V2 API.

My involvement consisted of:

- Code reviews.
- General project guidance.
- Deploying and testing the new code.
- Tweaking bits to make things work.
- Running the scripts that Zane built to load the data.

So if you're going to attribute me to building V2, please don't - go thank Zane ☺️ I asked him about why he wanted to help with the project, and he had this to say:

>  I guess PokéAPI was a bit of an experiment in backend development for me, since I'm normally a front end developer. I had been looking for something to jump in on and when V2 was announced it seemed perfect since I've loved pokemon since I first opened a pack of cards 16 years ago! I really just hope PokéAPI enables the community to do things it hasn't done before.

## Tell Me About The API Already

The new API provides resources for the following content:

```python
- Abilities
- Abilitie_names
- Abilitie_effect_texts
- Abilities_flavor_texts
- Abilities_changes
- Abilities_change_effect_texts
- Berries
- Berry_flavors
- Berry_firmnesses
- Berry_firmness_names
- Characteristics
- Characteristic_descriptions
- Contest_combos
- Contest_types
- Contest_type_names
- Contest_effects
- Contest_effect_texts
- Contest_effect_flavor_texts
- Egg_groups
- Egg_group_names
- Encounter_methods
- Encounter_method_names
- Encounter_conditions
- Encounter_condition_names
- Encounter_condition_values
- Encounter_condition_value_names
- Encounter_condition_value_maps
- Encounter_slots
- Encounters
- Evolution_chains
- Evolution_triggers
- Evolution_trigger_names
- Experiences
- Generations
- Generation_names
- Genders
- Growth_rates
- Growth_rate_descriptions
- Items
- Item_names
- Item_effect_texts
- Item_categories
- Item_category_names
- Item_attributes
- Item_attribute_maps
- Item_attribute_descriptions
- Item_flavor_texts
- Item_fling_effects
- Item_fling_effect_effect_texts
- Item_pockets
- Item_pocket_names
- Item_game_indexes
- Languages
- Language_names
- Locations
- Location_game_indexes
- Location_names
- Location_areas
- Location_area_names
- Location_area_encounter_rates
- Machines
- Moves
- Move_names
- Move_changes
- Move_flavor_text
- Move_effects
- Move_effect_changes
- Move_effect_change_effect_texts
- Move_effect_effect_texts
- Move_attributes
- Move_attribute_names
- Move_attribute_maps
- Move_attribute_descriptions
- Move_metas
- Move_ailments
- Move_ailment_names
- Move_battle_styles
- Move_battle_style_names
- Move_categories
- Move_damage_classes
- Move_damage_class_descriptions
- Move_learn_methods
- Move_learn_method_names
- Move_targets
- Move_target_descriptions
- Move_state_changes
- Natures
- Nature_names
- Nature_pokeathlon_stats
- Nature_battle_style_preference
- Pal_park_areas
- Pal_park_area_names
- Pal_parks
- Pokeathlon_stat_names
- Pokeathlon_stats
- Pokedexes
- Pokedex_descriptions
- Pokedex_version_groups
- Pokemon
- Pokemon_abilities
- Pokemon_colors
- Pokemon_names
- Pokemon_dex_numbers
- Pokemon_egg_groups
- Pokemon_evolutions
- Pokemon_forms
- Pokemon_form_names
- Pokemon_form_generations
- Pokemon_game_indices
- Pokemon_habitats
- Pokemon_habitat_names
- Pokemon_items
- Pokemon_moves
- Pokemon_shapes
- Pokemon_shape_names
- Pokemon_species
- Pokemon_species_names
- Pokemon_descriptions
- Pokemon_flavor_texts
- Pokemon_stat
- Pokemon_type
- Regions
- Region_names
- Stats
- Stat_names
- Super_contest_effects
- Super_contest_combos
- Super_contest_effect_flavor_texts
- Types - Type_names
- Type_game_indices
- Type_efficacy
- Versions
- Version_names
- Version_groups
- Version_group_move_learn_methods
- Version_group_regions
```

I know what you're thinking, and yeah, that's a heck of a lot of stuff. Luckily the [documentation](https://pokeapi.co/docsv2/) have been designed to make this much easier to navigate. Each resource has a response example as well as a request example to help you get started.

## What can I build with this now?

There is so much content here that it is hard to think about how many possible apps can be created with it. Here are a few ideas:

- A 3DS-browser tool to help me work out move effectiveness quickly.
- A mobile app for building Pokémon teams.
- An entire in-browser Pokémon game.
- A tutorial for PokéAPI to help people learn how to use APIs.

## Statistics

I did this last year, and it's really fun to see the usage graph of the API grow over time. The big spikes are probably attributed to integrations with DuckDuckGo (Just type [Pokemon Pikachu](https://duckduckgo.com/?q=pokemon+pikachu&ia=pokedex&iax=1) to see it working!) and also integrations with some coding schools.

![](http://i.imgur.com/ZYpWNSN.png)

Whilst not hugely different from the graph last year, it does show a steady upwards trend. If this was a start up I wonder if I'd be able to get investment with this type of growth?

Enjoy using PokéAPI V2, and feel free to submit any bugs or issues, or even contributions to [the open project](https://github.com/phalt/pokeapi)!
