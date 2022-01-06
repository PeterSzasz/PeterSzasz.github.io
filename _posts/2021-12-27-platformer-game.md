---
layout: post
author: SzP
title:  "Platformer game"
date:   2021-12-27 11:23:10 +0100
categories: backlog apps pltfrmr
excerpt_separator: <!--more-->
---
A platformer game needs graphics for the levels and characters, movement logic, menus, enemies, etc. It is a good base for design patterns too, not just game related but anything.
<!--more-->
I liked that I can use Tiled for map creation and first goals was just to create a few maps then implement pymunk as the physics engine (just like in arcade tutorials).
I abandoned this project just before pymunk implementation and when I re-started work a few weeks ago a big refactor brought State pattern into the game. And it became obvious that in this project, in this blob of a game without strict design ideas, I can implement a bunch of patterns. So I started searching for patterns. I was just curious what functionalities these patterns can bring to this game.

MVC was among the first that came in mind, but as it turns out if you are using a library like arcade half of the problems are already solved in this matter. Right now I only added Observer for Model. It handles player movement the main reason behind every change. I'm planning to extend this to menu navigation, sound triggering and maybe handling game logic changes, if these topics mature enough. Controller is handled in arcade and View is what pyglet created for, so not much to do here. I was thinking of how can I force the Strategy pattern into the system but I abandoned the idea because of its redundancy. As for implementation pyglet have an event handling system already with all the event registering, storing, notifying, etc. So `Gameplay` class inherits from `pyglet.event.EventDispatcher` also. The keyboard (and the minimalistic controller) presses and releases all dispatch an appropriate, registered event. The `MainActor` and a `MovementLogger` classes catches the relevant ones and reacts.

`actors.py`
{% highlight python %}
class MainActor(AnimatedWalkingSprite):
...
    def setup_subject(self, input_subject):
        input_subject.push_handlers(self)

    # catches event by name
    def move_left(self, moving):
    ...
...
{% endhighlight %}
`states.py`
{% highlight python %}
from pyglet.event import EventDispatcher

class Gameplay(BaseState, EventDispatcher):
    def __init__(self,...)
        ...
        self.player.setup_subject(self)
        ...
    def on_key_press(...)
        if symbol == arcade.key.LEFT:
            self.dispatch_event('move_left',True)
        ...
    ...

Gameplay.register_event_type('move_left')

{% endhighlight %}

Oh yea, the `MovementLogger` class is a great thing. It not just logs the movement events to the console but stores them as a list of timed movement actions in a file, which then can be 'interpreted' by a `LogReplay`. This means that you can replay previous player movements with a few hundreth seconds accuracy. This I want to use for automatic testing and/or for player-created-combo-sets. All great and interesting features and all based on a simple Observer pattern.
![state pattern image]({{ site.url }}/assets/ObserverClassDiagram.png){:align="center" width="75%"}

The game uses a State pattern for menus and level loading. When someone starts prototyping a game a menu can easily been left out at first, who wants to constantly click through it just to test something simple. At a certain point it became necessary to implement at least a minimalistic one just to give more personality to the game. Arcade got the option to handle views, this builds upon pyglets window and graphic context handling, and it is a good way to show different scenes, levels, menus, etc in one particular window. A slight problem comes when you want to transfer data between views. A solution for this is to inherit a class from pyglet/arcade Window class and create some permanent data struct there. The different views can reach it, but you have to manage the data, make sure everybody can reach everything it needs. Some data remains there in this situation even when the actual view does not need it. A better solution could be to inherit a base State class from arcade.View and inherit every old view from it. For the data create and externalize a game_logic and maybe a player class and store permanent information there in its own class. The problem with unnecessary information floating around is still exists but at least they are in their own classes. With a base class like this the views become States which creates a more obvious role for them. Especially with a set_next_state method which is to load the next state/view into the window.
![state pattern image]({{ site.url }}/assets/StateClassDiagram.png){:align="center" width="75%"}

Enemy creation can be handled with the help of a Factory pattern or an Abstract Factory.

The Observer was interestin at first, then I realized its potential and implemented logging and from that a replay class. and more to come
