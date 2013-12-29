---
layout: post
title:  "Dot notation in python nested dictionaries"
date:   2013-05-23 22:05:17
categories: python
---

This post gives a class [`Dotable`](#dotable) to make nested python dictionaries (json-like objects) accessable using dot notation. I then use this to fill in strings which use ruby's `#{}` format.

##### Why it's not there by default

I could't find a PEP for this, but it's easy to see the logic to it, for example consider the unruly behaviour of 

{% highlight python %}
d = {'get': 'me'}
d['get'] == d.get # ??
{% endhighlight %}


*Anyway, [practicality beats purity](http://www.python.org/dev/peps/pep-0020/), so here's my solution:*

<span id='dotable'></span>

{% highlight python %}
class Dotable(dict):

    __getattr__= dict.__getitem__

    def __init__(self, d):
        self.update(**dict((k, self.parse(v))
                           for k, v in d.iteritems()))

    @classmethod
    def parse(cls, v):
        if isinstance(v, dict):
            return cls(v)
        elif isinstance(v, list):
            return [cls.parse(i) for i in v]
        else:
            return v
{% endhighlight %}


-  [`__getattr__`](http://docs.python.org/2/reference/datamodel.html#object.__getattr__) is the important part which means we can use to dot notation [^4].

- The `parse` method is quite general, it takes any json-like object and returns an object whose dictionary part can be accessed with dot notation. I thought this was quite a neat solution (not just because it's recursive).


Note: Some people on StackOverflow have posted about using dot notation before ([^1],[^2],[^3]), but I wasn't happy with these implementations (to be honest I didn't look too deeply, and think this is a simpler solution). *If you have a nicer way, please leave a comment and let me know!*


Example use:

{% highlight python %}
In [1]: d1 = Dotable.parse({'a': [{'b': 3, 'c': 5}]})

In [2]: d1.a
Out[2]: [{'b': 3, 'c': 5}]

In [3]: d1.a[0].b
Out[3]: 3

In [4]: d2 = Dotable.parse([42, {'a': 'shrubbery'}])

In [5]: d3 = Dotable.parse(7)
{% endhighlight %}


<span id='why'></span>
##### Why I wanted it:

I wanted to populate strings which contained strings with ruby syntax `#{blah.foo}` from text files, where `blah` a json-like object.

*__Before__ you start writing something with ~~evil~~ `eval` and regular expression ([*then you'll have two problems!*](http://www.codinghorror.com/blog/2008/06/regular-expressions-now-you-have-two-problems.html)) to fill in `#{blah.foo}`...*

You can use [`format`](http://docs.python.org/2/library/string.html#string.Formatter.format):

{% highlight python %}
In [11]: '{d1.a[0].b}'.format(**locals())
Out[11]: '3'
{% endhighlight %}


I lied about not using regular expressions, you need a little one to remove the `#`s:

{% highlight python %}
In [12]: s = '#{d1.a[0].b} #{d2[0].a}'

In [13]: import re

In [14]: re.sub('#({[^}]*})', r'\1', s)
Out[14]: '{d1.a[0].b} {d2[0].a}'

In [15]: re.sub('#({[^}]*})', r'\1', s).format(**locals())
Out[15]: '3 shrubbery'
{% endhighlight %}


Hope this helps.

[^1]: http://stackoverflow.com/questions/224026/javascript-style-dot-notation-for-dictionary-keys-unpythonic

[^2]: http://stackoverflow.com/questions/3031219/python-recursively-access-dict-via-attributes-as-well-as-index-access

[^3]: http://stackoverflow.com/questions/3797957/python-easily-access-deeply-nested-dict-get-and-set

[^4]: http://stackoverflow.com/questions/224026/javascript-style-dot-notation-for-dictionary-keys-unpythonic/224876#224876