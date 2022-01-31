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
Basically you get yourself the theme and your site's base code locally. Modify part of the theme and when you push to remote it will replace default theme's files with these modified files when github generates the static site.
Here we use the [midnight][midnight] theme.
{: .text-justify}

Create a local repo.
{% highlight bash %}
git init REPONAME
cd REPONAME
{% endhighlight %}

Create Jekyll site locally.
{% highlight bash %}
jekyll new --skip-bundle .
{% endhighlight %}

Edit `Gemfile`. Comment out jekyll gem and set theme and github-pages gem.
{% highlight bash %}
#gem "jekyll", "~> 4.2.1"
gem "github-pages", "~> 219", group: :jekyll_plugins
gem 'jekyll-theme-midnight', '~> 0.2.0'
{% endhighlight %}

Install theme from github, like:
{% highlight bash %}
git clone https://github.com/pages-themes/midnight
{% endhighlight %}
and insert this to your `_config.yml`:
{% highlight bash %}
theme: jekyll-theme-midnight
{% endhighlight %}

Copy necessary subfolders from installed theme to the created site directory.
When you upload all this to github the parser will use these instead of the default, so you can modify what you need.
{% highlight bash %}
cp -a /../theme_folder/_layouts/ .
cp -a /../theme_folder/_includes/ .
cp -a /../theme_folder/_sass/ .
{% endhighlight %}

Modify.
Then push to the previously created and activated Github Pages remote repo.

More info [here][github-jekyll] and [here][jekyll-doc].

[midnight]: https://github.com/pages-themes/midnight
[github-jekyll]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll
[jekyll-doc]: https://jekyllrb.com/docs/step-by-step/01-setup/