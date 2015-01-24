---
layout: post
title: "Preconnect self"
lang: en
date: 2014-09-30 15:00:00
tags: WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: Improve your webperformance by pre-opening additional TCP pipes to yourself.
excerpt: "Blog-Post: Improve your webperformance by pre-opening additional TCP pipes to yourself."
custom_css:
  - blog
  - syntax
---
You can use `preconnect` to pre-open a new TCP connection. So the browser can do this time intensive task while it waits for the rest of the HTML-page. Example to preconnect `www.example.com`:

{% highlight HTML %}
<html>
  <head>
    <link rel="preconnect" href="//www.example.com">
  </head>
  ...
{% endhighlight %}

That's nothing new. But until I tried it I didn't know that this could be useful for your own domain too.

While optimizing the `index.html` of `amooma.de` I ended with [webpagetest.org/.../140925_HW_16V5/](http://www.webpagetest.org/result/140925_HW_16V5/1/details/) and this waterfall:

{% image 2014/09/30/waterfall_no_preconnect.png class="pure-img-responsive" alt="Waterfall without a preconnect." %}

In the 3rd line you can see that the browser opens a second TCP connection to fetch that image at the same time it fetches the image in the 2nd line. Makes sense. But by opening that TCP connection earlier we could save valueable time here. So I changed the HTML to this:

{% highlight HTML %}
<html>
  <head>
    <link rel="preconnect" href="//amooma.de">
  </head>
  ...
{% endhighlight %}

And guess what happend!? The page became about 100ms faster! Have a look at [webpagetest.org/.../140929_TQ_HVP/](http://www.webpagetest.org/result/140929_TQ_HVP/1/details/) and this waterfall:

{% image 2014/09/30/waterfall_with_preconnect.png class="pure-img-responsive" alt="Waterfall with a preconnect." %}
