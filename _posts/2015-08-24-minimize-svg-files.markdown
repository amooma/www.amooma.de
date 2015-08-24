---
layout: post
title: "Minimize SVG files"
lang: en
date: 2015-08-24 15:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "How-To to minimize the size of a SVG file."
excerpt: "How-To to minimize the size of a SVG file."
custom_css:
  - blog
---

Last Saturday I gave a [talk about WebPerformance](https://media.ccc.de/browse/conferences/froscon/2015/froscon2015-1536-webperformance_autobahn_oder_fussgangerzone.html#video) at FroSCON. On the same day Tobias Baldauf ([@tbaldauf](https://twitter.com/tbaldauf)) of [Akamai](https://www.akamai.com/) gave a great [talk about JPEG compression](https://media.ccc.de/browse/conferences/froscon/2015/froscon2015-1601-the_jpeg_is_dead_long_live_the_jpeg.html#video) which was very interesting.

But what was most interesting for me was not the JPEG part but the slide [121](https://speakerdeck.com/tbaldauf/froscon-2015-the-jpeg-is-dead-long-live-the-jpeg?slide=121) which displays the source code of a SVG file on the left page and a compressed version of the same file on the right page.

{% image "2015/08/24/svgo-example-screenshot.png" alt="Slide 121 Screenshot" %}

The moment I saw that slide it clicked. I wondered why I didn't think about this earlier: SVG files contain a lot of float numbers with many decimal places. By rounding those decimal places you'll end with a smaller file and in most cases you will not see a difference in the result. By rounding 1.2000001 to 1.2 you'll save 6 digits but lose only minor actual information.

And it even gets better: There is a tool for the job. [SVG Optimizer](https://github.com/svg/svgo) is a Nodejs-based tool for optimizing SVG vector graphics files. SVGO does all sort of optimization and minifying magic to SVG files. You can include it in your building pipeline easily:

{% highlight bash %}
svgo test.svg test.min.svg
{% endhighlight %}

You find the Github repository at [https://github.com/svg/svgo](https://github.com/svg/svgo) and there's an online version to get a feeling for it at  [https://jakearchibald.github.io/svgomg/](https://jakearchibald.github.io/svgomg/).

Many WebPerformance professionals use SVG files to save space. Now you can even save more space by minifying those files. Which was the first thing I did for www.amooma.de. Have a look at the [Github commit](https://github.com/amooma/www.amooma.de/commit/cb93cfbf69f04c12fbf1331f709b29f7bd04ede5) to see the difference.
