---
layout: post
author: SzP
title:  "Apps in Backlog"
date:   2021-12-21 11:33:10 +0100
categories: backlog apps pomodoro ttrs pltfrmr graphapp
excerpt_separator: <!--more-->
---
Earlier this year I started working on a few mini projects to try things. Basically every project was for one or two specific technic, api, etc.
For example in Pomodoro I wanted to create an app with a custom pixelated window 'skin', like in the 90's, in python.
<!--more-->
Pomodoro uses Qt's python binding PyQt5. 
In ttrs I was curious what can be done to create some custom graphics in code using pyglet.
Pltfrmr started as a python arcade experiment and then it outgrow itself. It started just a simple platformer test project.
I liked that I can use Tiled for map creation and first goals was just to create a few maps use then implement pymunk as the physic engine (just like in arcade tutorials).
I abandoned this project just before pymunk implementation and when I re-started work a few weeks ago a big refactor brought State pattern into the game.
And it became obvious that in this project, in this blob of a game without strict design ideas, I can implement a bunch of patterns. So I started searching for patterns
that can be implemented in a game like this, in python and make sense (more or less). I was just curious what functionalities these patterns can bring to this game.
