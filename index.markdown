---
layout: base
title: Twistar - Python Twisted ORM
---
# Twistar: Twisted Active Record 
Twistar is a Python implementation of the [active record pattern](http://en.wikipedia.org/wiki/Active_record_pattern) (also known as an object-relational mapper or ORM) that uses the [Twisted](http://twistedmatrix.com/trac/) framework's [RDBMS support](http://twistedmatrix.com/documents/current/core/howto/rdbms.html) to provide a non-blocking interface to relational databases.

Twistar currently features:
* A thoroughly asynchronous API
* Object validations (and support for the easy creation of new validation methods)
* Support for callbacks before saving / creating / updating / deleting 
* Support for object relational models that can be queried asynchronously
* A simple interface to [DBAPI](http://www.python.org/dev/peps/pep-0249/) objects
* A framework to support any relational database that has a module that implements the [Python Database API Specification v2.0](http://www.python.org/dev/peps/pep-0249/) (MySQL, PostgreSQL, and SQLite are all supported now)
* Support for object polymorphism
* Unit tests

# Quick Example For Those In A Rush 
For this example, assume that there is a table named "users" with varchar columns for first_name and last_name and an int age column.
{% highlight python %}
#!/usr/bin/env python
from twisted.enterprise import adbapi
from twistar.registry import Registry
from twistar.dbobject import DBObject
from twisted.internet import reactor

class User(DBObject):
     pass

def done(user):
     print "A user was just created with the name %s" % user.first_name
     reactor.stop()

# Connect to the DB
Registry.DBPOOL = adbapi.ConnectionPool('MySQLdb', user="twistar", passwd="apass", db="twistar")

# make a user
u = User()
u.first_name = "John"
u.last_name = "Smith"
u.age = 25

# Or, use this shorter version:
u = User(first_name="John", last_name="Smith", age=25)

# save the user
u.save().addCallback(done)

reactor.run()
{% endhighlight %}

Then, finding this user is easy:
{% highlight python %}
def found(users):
    print "I found %i users!" % len(users)
    for user in users:
        print "User: %s %s" % (user.first_name, user.last_name)

u = User.findBy(first_name="John", age=25).addCallback(found)
{% endhighlight %}

This is a very simple example - see the [Examples and User Documentation][examples] for more complicated examples and additional uses.

# Installation 
## Prerequisites 
One or more of the following Python database interfaces that implement the [DBAPI PEP](http://www.python.org/dev/peps/pep-0249/) must first be installed:
 * [MySQLdb](http://sourceforge.net/projects/mysql-python/) (MySQL interface)
 * [psycopg2](http://initd.org/psycopg/) (PostgreSQL interface)
 * [sqlite3](http://docs.python.org/library/sqlite3.html) (SQLite interface)

## Install 
Simply run:
{% highlight bash %}
pip install twistar
{% endhighlight %}

## Unit Tests 
Simply run:
{% highlight bash %}
trial twistar
{% endhighlight %}

# Debugging
To get debugging information (all queries executed) using *twisted.python.log*, just use:
{% highlight python %}
from twistar.dbconfig.base import InteractionBase
InteractionBase.LOG = True
{% endhighlight %}

# Docs 
 * [Examples and User Documentation][examples]
 * [API Docs][apidocs]

Questions and complaints can be added to https://github.com/bmuller/twistar

[examples]: doc
[apidocs]: apidoc