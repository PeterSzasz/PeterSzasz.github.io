---
layout: post
author: SzP
title:  "Platformer game 1"
date:   2021-12-27 11:23:10 +0100
categories: backlog apps pltfrmr
excerpt_separator: <!--more-->
---
A platformer game needs graphics for the levels and characters, movement logic (physics), menus, enemies, etc. It is a good base for design patterns too, not just game related but anything.
<!--more-->
I liked that I can use Tiled for map creation and arcade handles the import and first goal was just to create a few maps then implement pymunk as the physics engine, some menu screens.
Then I abandoned this project just before pymunk implementation and when I re-started work a few weeks ago a big refactor brought a real State pattern into the game. And it became obvious that in this project, in this blob of a game without strict design ideas, I can implement a bunch of patterns. So I started searching for patterns. I was just curious what functionalities these patterns can bring to this game.

The game uses a State pattern for menus and level loading. In the first iteration the menu/view system used a string based state machine kinda thing, it worked but it was messy. A state variable stored the name of the actual state and one big method handled the state switching. The good thing about this was that state change rules were in one place, for 3-5 states this is still in a 'human readable form', but after that you need something more robust.
When someone starts prototyping a game a menu can easily been left out at first, who wants to constantly click through it just to test something simple. At a certain point it became necessary to implement at least a minimalistic one just to give more personality to the game. Arcade got the option to handle views, this builds upon pyglets window and graphic context handling, and it is a good way to show different scenes, levels, menus, etc in one particular window. A slight problem comes when you want to transfer data between views. A solution for this is to inherit a class from pyglet/arcade Window class and create some permanent data struct there. The different views can reach it, but you have to manage the data, make sure everybody can reach everything it needs. Some data remains there in this situation even when the actual view does not need it. A better or just different solution could be to inherit a base State class from arcade.View and inherit every view from it. It seemed more simple so I decided to use this version. For the shared data I created a `NamedTuple` named `Context` and now this is the only class that the view share between each other (previous version contained 3-4 different classes). The problem with unnecessary information floating around is still exists but at least they are in their own structured classes.
{% highlight python %}
class Context(NamedTuple):
    window: Window
    game_logic: GameLogic
    player: 
{% endhighlight %}
With a base class like this the views became States which creates a more obvious role for them. Especially with a set_next_state method which is to load the next state/view into the window and handles the `Context` transfer too.
![state pattern image]({{ site.url }}/assets/StateClassDiagram.png){: .align-center}


Enemy creation was handled with a Factory design pattern. Later when the enemy numbers became so big that creation should be based on quasi random groups or level/biom/origin based (forest, beach, water, etc) mixing an Abstract Factory should be used.
For this game right now the difficulty system is a 3 level enemy generation system. Three diffficulty class determines the properties like damage and health of said monsters. At the beginning of each level during level creation the `game_logic` tells the actual difficulty and based on this different classes will be called to generate foes.
![factory pattern image]({{ site.url }}/assets/FactoryClassDiagram.png){: .align-left}
Not very sophisticated but it does the job. The really interesting system would be with the Abstract Factory and twice as many enemies. Even better if the `Gameplay` class got its difficulty class through dependency injection and the whole determine difficulty would be inside game_logic.

Second part [here]({% post_url 2022-01-11-platformer-game-2 %}).
{% highlight python %}
{% endhighlight %}