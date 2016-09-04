---
layout: post
title:  "Auto generate requirements.txt file"
date:   2016-09-04 10:00:00
author: Anthony
categories: Python
tags:	Python requirements
cover:  "/assets/banner/python.png"
---

# What is a requirements.txt file ?


Usually, a requirement file is just a list of pip install arguments. You can also specify a match version for each. Theses files can be easy install with the [pip app][pip] `pip install -r requirements.txt`. A simple requirement file looks like:
```html
ovh==0.4.5
requests==2.11.1
tornado==4.4.1
```

But for a developper, how can we generate it ? I ask me that question when I push on github my last project [ExAlias][exalias]. I figure that you have many solutions to do this.

## 1. Directly with pip

With this solution, the requirement file will contains a pinned version of everything used in the project who has been installed with pip.
```
$ pip freeze > requirements.txt
```

## 2. Using a third tool

The second method, (that I used) is called [pipreqs][pipreqs]. A pip requirements.txt file generator based on imports of any project.

### Why using it

For 3 reasons
* `pip freeze` only saves the packages that are installed with pip install in your environment.
* pip freeze saves all packages in the environment including those that you don't use in your current project. (if you don't have virtualenv)
* and sometimes you just need to create requirements.txt for a new project without installing modules.

### how to use it

First of all, you have to install it.
```
$ pip install pipreqs
```
or
```
$ git clone https://github.com/bndr/pipreqs.git
$ python pipreqs/setup.py install
```

After that, the only thing to do is:
```
pipreqs Documents/project/exalias
```








[pip]:      https://github.com/pypa/pip
[exalias]:  https://github.com/Fen0l/exalias
[pipreqs]:  https://github.com/bndr/pipreqs

