---
layout: post
title:  "Setup a Flask environment"
date:   2017-10-24 09:23:00
author: Anthony
categories: Development
tags:	flask python cli
cover:  "/assets/banner/flask.png"
---

# How to setup a proper Flask environment

[Flask][flask] is a microframework for Python based on [Werkzeug][werkzeug], [Jinja][jinja] 2 and good intentions. And before you ask: It's [BSD licensed][flask-license]!


A proper Flask environment need [Virtualenv][virtualenv] and [Virtualenvwrapper][virtualenvwrapper]. Virtualenv is a tool to create isolated Python environments. You can install it directly from your CLI with [PIP][pip]. PIP is a Python packages manager.

## Installation

To install pip and upgrade it to the last version, run the following:

{% highlight bash %}
# Intall
curl -O https://bootstrap.pypa.io/get-pip.py
python get-pip.py

# Upgrade
pip install -U pip
{% endhighlight %}


After that, you can simple install virtualenv and virtualenvwrapper with the following:

{% highlight bash %}
# Install virtualenv
$ pip install virtualenv

# Installation and configuration
$ pip install virtualenvwrapper
$ export WORKON_HOME=~/Envs
$ mkdir -p $WORKON_HOME
$ source /usr/local/bin/virtualenvwrapper.sh
{% endhighlight %}


## Flask Environment

Now you have to create a new virtual environment for your project

{% highlight bash %}
$ mkvirtualenv flask_project
{% endhighlight %}

In order to use your new virtual environment type:

{% highlight bash %}
$ workon flask_project
{% endhighlight %}

You can now install Flask and play with Python with the following:

{% highlight bash %}
$ pip install Flask Flask-SQLAlchemy
{% endhighlight %}


To return to your main environement, just run:

{% highlight bash %}
$ deactivate
{% endhighlight %}



[flask]:      			http://flask.pocoo.org
[werkzeug]:      		http://werkzeug.pocoo.org
[jinja]:      			http://jinja.pocoo.org
[flask-license]: 		http://flask.pocoo.org/docs/0.12/license
[virtualenv]:			https://virtualenv.pypa.io/en/stable
[pip]:					https://pypi.python.org/pypi/pip
[virtualenvwrapper]:	https://virtualenvwrapper.readthedocs.io/en/latest
