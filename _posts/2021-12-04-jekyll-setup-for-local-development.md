---
layout: post
author: SzP
title:  "Jekyll setup for local development."
date:   2021-12-04 11:15:10 +0100
categories: jekyll update test
excerpt_separator: <!--more-->
---
A quick description about Jekyll + Github and how to set an environment for local testing if you happen to use a custom theme.
<!--more-->
Create a local repo.
{% highlight bash %}
git init REPONAME
cd REPONAME
{% endhighlight %}

Initialize Jekyll.
{% highlight bash %}
$ jekyll new --skip-bundle .
{% endhighlight %}