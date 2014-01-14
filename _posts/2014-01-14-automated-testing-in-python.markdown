---
layout: post
title:  "Automated testing in python"
date:   2014-01-14 22:05:17
categories: python testing
---

*I haven't found an easier way to do this up in python, so if anyone does then please [let me know](#disqus_thread)!*

### python guard

Having tests run in the background during implementation faster and more robust (provided you've come at it with a plan!), and notify you of your success or failure.

![tests pass!](http://jam.im/images/posts/guard-rspec-osx-notification.png)

One tool to do this is [Guard](https://github.com/guard/guard) (a Ruby gem), which watches which files you have change, and on changes triggers events. This enables you to control which tests are run. I found [an example Guardfile](https://gist.github.com/misaka/1688369) for [py.test](http://pytest.org/latest/) which I've tweaked to be useful for general projects:

> https://github.com/hayd/python_guard

I added tests for various placements of test files, but this could be configurable to make this a more useful tool for your project.

*Note: I did come across [guard-nosetest](https://github.com/medihack/guard-nosetests) which runs all tests after any change all of the time (though often this is desired!), it could be that a pull-request to tweak their Guardfile could fix that... though I think it's probably nicer to have a plugin to your text editor of choice.*