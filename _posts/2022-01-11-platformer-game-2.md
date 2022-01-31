---
layout: post
author: SzP
title:  "Platformer game 2"
date:   2022-01-11 10:22:21 +0100
categories: backlog apps pltfrmr
excerpt_separator: <!--more-->
---
A platformer game needs more than just graphics and movement logic. It needs an event system (Observer), input handling (Strategy) and some kind of process management.
<!--more-->

MVC was among the first that came in mind, but as it turns out if you are using a library like arcade half of the problems are already solved in this matter. Right now I only added Observer for Model and Strategy for Controller. Right now both handles player movement, the main reason behind every change, but different aspects of it. View part of MVC is what pyglet created for, so not much to do there, but maybe when I extend the GUI capabilities a Composite pattern might come in handy. I'm planning to extend Observer to menu navigation, sound triggering and handling game logic changes, if these parts mature enough.
{: .text-justify}
![strategy pattern image]({{ site.url }}/assets/StrategyClassDiagram.png){: .align-right}The Input handling contains three classes. A base class called `BaseInput` and two that inherits from this: `KeyboardInput` and `ControllerInput`. Because arcade and pyglet does a realy good job about basic input handling (at least for keyboard and mouse clicks) the main purpose of these classes is to just hide all the quirks. This way the `Gameplay` class can focus on its own stuff and doesn't even know if the input is through a controller or keyboarg or something else. This version of the strategy pattern is not two different algorithms but two different aproaches instead. The `KeyboardInput` uses the `on_key_press` and `on_key_release` methods and `ControllerInput` uses the `update` method. The `Gameplay` class calls all three and when an unused called it just `pass`es. This way the input objects are interchangeable in `Game_logic` or any other place. Right now as I mentioned the menu clicks/enters is still handled directly in the State classes, but I'm plannig to move all that to the logic section or somehow into the input classes (not likely) or into their own menu-logic-and-descriptor class.
{: .text-justify}
As for implementation of the Observer pattern pyglet have an event handling system already with all the event registering, storing, notifying, etc. So `Gameplay` and the `*Input` classes inherit from `pyglet.event.EventDispatcher` also. The keyboard and controller presses and releases all dispatch an appropriate, registered event. The `MainActor` and a `MovementLogger` classes catches the relevant ones and reacts.
{: .text-justify}

From actors.py:
{% highlight python %}
class MainActor(AnimatedWalkingSprite):

    def setup_subject(self, input_subject):
        input_subject.push_handlers(self)

    # catches event by name
    def move_left(self, moving):
    ...
...
{% endhighlight %}
From states.py:
{% highlight python %}
from pyglet.event import EventDispatcher

class Gameplay(BaseState, EventDispatcher):
    def __init__(self,...)
        ...
        self.player.setup_subject(self.game_logic.input)
        ...

     def on_key_press(self, symbol: int, modifiers: int):
        ...
        self.input.on_key_press(symbol,modifiers)

{% endhighlight %}
From input.py:
{% highlight python %}

# the base class registers the events for all its children
class BaseInput(EventDispatcher):
    ...
BaseInput.register_event_type('move_left')

class KeyboardInput(BaseInput):

    def on_key_press(self, symbol: int, modifiers: int)
        if symbol == arcade_key.LEFT:
            self.dispatch_event('move_left',True)
        ...

{% endhighlight %}

![one example of observer pattern]({{ site.url }}/assets/ObserverClassDiagram.png){: .align-right}
Oh yea, the `MovementLogger` class is a great thing. It not just logs the movement events to the console but stores them as a list of timed movement actions in a file, which then can be 'interpreted' by a `LogReplay`. This means that you can replay previous player movements with a few hundreth seconds accuracy. This I want to use for automatic testing. With console parameters one can hand a game replay file, level number and maybe an end position for assertion. Embed all this into some scripted environment and we got ourselves a nice testbed. Beside testing a version of this can be used for player-created-combos. Players can record a 3-move combo for example two different buttons and use them for solving puzzles or create some gui for building and saving combos. All great and interesting features and all based on a simple Observer pattern.
{: .text-justify}

{% highlight python %}
{% endhighlight %}