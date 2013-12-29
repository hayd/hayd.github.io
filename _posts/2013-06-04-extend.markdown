---
layout: post
title:  "Presentationally descriptive class attributes"
date:   2013-06-04 22:05:17
categories: css
---

<span id="bad"></span>
### ... are bad

Class and id attributes should be used to describe the *meaning* of different objects, **not** how they are presented. Presentation should be entirely described in the css.

##### Inline styles are bad

These are inline style in disguise.


Here's how we can use Sass[^1] to rid ourselves of this plague.

---

- [Rise of the presentationally descriptive class attributes](#rise)
- [Remove inline styles](#remove_inline_styles)
- [Remove inline classes](#remove_inline_classes)
- [Multiple classes](#multiple_classes)

[Warning notes:](#warning)

- [The order is critical](#order_is_critical)
- Specifiers not extending?

--

<span id="rise"></span>
### Rise of the presentationally descriptive class attributes

The advent of bootstrap (itself written in Less[^2]) has led to some developers to use *presentationally descriptive* class attributes.

TODO give example:

We can use Sass' `@extend`[^3] (this can also be done using Less[^4]) to keep the main code have purely descriptive classes.

TODO come up with analolgy to object oriented inherit…

<span id="remove_inline_styles"></span>
### Remove inline styles:

{% highlight html %}
<span style="color: red">alert</span>
{% endhighlight %}

to

{% highlight html %}
<style>
  .alert {color: red;}
</style>
<span class="alert">alert</span>
{% endhighlight %}


<span id="remove_inline_classes"></span>
### Remove inline classes:

*Note: this is rather an simplified example, and surely not a real-life one...*

{% highlight html %}
<style>
  .red {color: red;}
</style>

<span class="red">alert</span>
{% endhighlight %}

to

{% highlight html %}
<style> // Note: this is in your sass file
  .red {color: red;}
  .alert {@extend .red;}
</style>

<span class="alert">alert</span>
{% endhighlight %}


This reads: extend `.alert` to give it the properties of `.red`.

Note: this compiles (in CSS) to:

{% highlight html %}
<style>
  .red, .alert {color: red;}
</style>

<span class="alert">alert</span>
{% endhighlight %}

In this way, if you want to change all of the "alerts" from `.red` to `.blue`, then you only need change one line in the sass.

<span id="multiple_classes"></span>
### Extend multiple classes

You can also `@extend` multiple classes:

{% highlight html %}
<style>
  .red {color: red;}
  .bold {font-weight: bold;}
  .alert {@extend .red; @extend .bold;}
</style>

<span class="alert">alert</span>
{% endhighlight %}

which compiles to:

{% highlight html %}
<style>
  .red, .alert {color: red;}
  .bold, .alert {font-weight: bold;}
</style>

<span class="alert">alert</span>
{% endhighlight %}

<span id="warning"></span>
### Warning notes

<span id="order_is_critical"></span>
##### The order is critical

The order of these is **critical**, as with all css the things lower down have precendence (if they have the same score/specificity[^5]  [^6]).

Keeping those classes you which to `@extend` at the below those which they are extending from should help you to preserve some sanity.

##### Specifiers not extending?

TODO mention something about some specifiers not extending (e.g. `:first`?).

[^1]: [Sass (Syntactically Awesome StyleSheets)](http://sass-lang.com/)

[^2]: [Bootstrap: Built with LESS](http://twitter.github.io/bootstrap/extend.html)

[^3]: [Sass @extend reference](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#extend)

[^4]: [Sass vs Less](http://css-tricks.com/sass-vs-less/)

[^5]: [CSS3: specificity](http://www.w3.org/TR/css3-page/#specificity)

[^6]: [Stackoverflow: Points in CSS specifity](http://stackoverflow.com/questions/2809024/points-in-css-specificity)
