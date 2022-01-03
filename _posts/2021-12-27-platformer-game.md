---
layout: post
author: SzP
title:  "Platformer game"
date:   2021-12-27 11:23:10 +0100
categories: backlog apps pltfrmr
excerpt_separator: <!--more-->
---
A platformer game needs graphics for the levels and characters, movement logic, menus, enemies, etc. It is a good base for design patterns, not just game related but anything.
<!--more-->
The game uses a State pattern for menus and level loading. When someone starts prototyping a game a menu can easily been left out at first, who wants to constantly click through it just to test something simple. At a certain point it became necessary to implement at least a minimalistic one just to give more personality to the game. Arcade got the option to handle views, this builds upon pyglets window and graphic context handling, and it is a good way to show different scenes, levels, menus, etc in one particular window. A slight problem comes when you want to transfer data between views. A solution for this is to inherit a class from pyglet/arcade Window class and create some permanent data struct there. The different views can reach it, but you have to manage the data, make sure everybody can reach everything it needs. Some data remains there in this situation even when the actual view does not need it. A better solution could be to inherit a base State class from arcade.View and inherit every old view from it. For the data create and externalize a game_logic and maybe a player class and store permanent information there in its own class. The problem with unnecessary information floating around is still exists but at least they are in their own classes. With a base class like this the views become States which creates a more obvious role for them. Especially with a set_next_state method which is to load the next state/view into the window.

Enemy creation can be handled with the help of a Factory pattern or an Abstract Factory.

The Observer was interestin at first, then I realized its potential and implemented logging and from that a replay class. and more to come
