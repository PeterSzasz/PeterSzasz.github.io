---
layout: post
author: SzP
title:  "Pomodoro app"
date:   2021-12-22 12:23:10 +0100
categories: backlog apps pomodoro
excerpt_separator: <!--more-->
---
The Pomodoro app's main purpose was to create a custom window with pixelated look, custom controls and maybe some useful functionality as a pomodoro timer.
<!--more-->
![app image](https://github.com/PeterSzasz/PomodoroApp/raw/master/screenshot_1.png){: .align-right}
For windowing in python the PyQT library is a good choice, PyQT5.15 in this project. It's basically just a binding on the original C++ version, but still can be used on many OSs (like python), have a bunch of options doing things (like python) and you can rely on the original versions documentation.
First I googled some tomatom images, then instead draw one in 32x32 and enlarged. As it turns out in PyQT if you want a custom looking window its enough to set the proper width and height for the window and set the background tranclucent. This way you got an invisible window. The second part of the magic is in paintEvent, you simply draw the image first as a background and everything else on top of it.

{% highlight python %}
self.background = QPixmap('image/pomodo_bkg_02.png')
if self.background:
    self.width = self.background.width()
    self.height = self.background.height()

self.setGeometry(self.posx, self.posy, self.width, self.height)
self.setAttribute(Qt.WA_TranslucentBackground)
...
def paintEvent(self, a0: QPaintEvent) -> None:
    painter = QPainter(self)
    painter.drawPixmap(0, 0, self.background)
{% endhighlight %}

Next the buttons and for a pomodoro app some kind of timer. I created different classes for both purposes. `PomodoTimer` got the timetable and the actual state or phase. At first I wanted to create a mini state machine for this but it was too much for this app, overkill. Last remnant is the name of this dictionary and the one holding the actual position.
{% highlight python %}
self.states = [ {'STOPPED':99.99},
                {'RUN1':15},
                {'BREAK1':5},
                {'RUN2':15},
                {'BREAK2':5},
                {'RUN3':15},
                {'LONGBREAK':25}
                ]
self.state = self.states[0]
{% endhighlight %}
This class got a method for each basic ui interaction: Start, Stop, Pause. These handles the timer and play the preloaded sound. In a bigger system sound should get its own class and probably called throungh an event system but here it can stay. Besides some utility methods the `update` is where logic happens. It checks if puased and updates `elapsed_time` accordingly. If enough time elapsed it sets the next phase. But a bug emerges from these rows. The last line it plays the `break_sound`. The bug is either it should be called something like `next_phase_sound` or I should write something that checks what the next phase's type is and play a sound accordingly. Maybe next time.
For all these logic to run from time to time in the `MainWindow` class an update method calls this method, which is registered as a callback for every half sec.
{% highlight python %}
class MainWindow(QWidget):
    def __init__(self):
        self.timer = QTimer(self)
        self.timer.timeout.connect(self.update)
        self.timer.start(500)
        ...
    ...
{% endhighlight %}

For the buttons and the slider first I created a base class. This `CollisionRect` contains local position and dimension properties and an `isInside` method. This method tells you if the coordinate parameters are inside the body or not. Simple. `RectButton`, `EllipseButton` and `PassiveSlider` are inherited from this base class. The first two basically just draws a rectangular or elliptical shape which can function as a button with `isInside`. The slider class is a little bit more complex. It stores and updates the position of the slider, manages the drawing of it and the elapsed and end-time strings.
A very simple yet efficient way to create someting that can function as a button. In a duck-test way, look like one and act like one. Below the code from `MainWindow` where a `PassiveSlider` object `time_slider` reacts to a left click by calling `PomodoTimer`'s pause() method.

{% highlight python %}
def mousePressEvent(self, a0: QMouseEvent) -> None:
    if a0.button() == Qt.LeftButton:
        if self.time_slider.isInside(a0.localPos().x(), a0.localPos().y()):
            self.pom_timer.pause()
    ...
{% endhighlight %}