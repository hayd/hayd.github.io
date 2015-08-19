---
layout: post
title:  "Adding slides to github pages (gh-pages)"
date:   2015-08-19 12:05:17
categories: markdown gh-pages slides remark

---

# Adding slides to gh-pages

I've previously used some external services to write my slides (either
Publisher, slides.com, or jupyter notebook). I really wanted to move to
something which could be put under version control\* and hosted on my
gh-pages site (this one!).

Firstly I want to say I'm not ecstatic with the arrangement I'm about to
describe, but nonetheless it works...

## Using remark.js

[remark](https://github.com/gnab/remark) is a "markdown-driven" slide tool.
You link to the minimified javascript, add some css, and you're away:

```js
var slideshow = remark.create({
  sourceUrl: 'content.md',
});
```
where `content.md` is a markdown file with horizontal rules (`---`) to denote
new slides. Easy peasy.

## Repo layout

I created a new repository for my slides, which I somewhat cryptically called
[`s`](https://github.com/hayd/s). For the uninitiated, this means, if you
have a `gh-pages` branch,  a get request asks for `hayd.github.io/s/*` it hits
this repository.

1. Set the [index.html](https://github.com/hayd/s/blob/gh-pages/index.html) to
   redirect to the hayd.github.io#slides.
2. Create a `talk_title` directory with an index.html and a content.md. (When
   a user visits `hayd.github.io/s/talk_title`, they are shown the slides
   from talk_title/content.md.
3. Add links to your slides in your main gh-pages.

## Notes

1. I already have my [404 page](http://hayd.github.io/404) set up to redirect to
   the home page, after 5 seconds, if it hits a bad URL.
   This is inherited by `/s`.

2. There is a lot of repetition here, especially the `index.html`s and adding
   the list of slides manually to your site. It would be nice to have this
   generated with jekyll (I haven't looked into this)...

*(It seems like the above, wrapping Jekyll and remark.js would be a
really useful project.)*

Perhaps there's all ready be something out there and my googling failed?

## Aside / the future

Upon researching this post I found that there is package,
[RISE](https://github.com/damianavila/RISE), which creates "live" slides using
the jupyter back-end. This could actually be the perfect solution for
presenting...

It doesn't have very much documentation at the moment, but this
demonstratation shows some of the power and easy of use:

[RISE, jupyter slides](https://camo.githubusercontent.com/6193fec30468242c8af2fa838eae55c02d6ff1a6/687474703a2f2f692e696d6775722e636f6d2f50657333556f332e676966).

This along with web-based python connection (e.g. [wakari](https://wakari.io/))
will be a fantastic slideshow tool.

\*Version control can work with jupyter notebook.

