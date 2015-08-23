---
layout: post
title: "Blueprint for a Super Fast Homepage"
lang: en
date: 2015-02-13 06:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "We invested many months to create our super fast homepage. Here is a blueprint which includes the source code to save yourself a lot of time and work for your own super fast webpage. Speed matters!"
excerpt: "We invested many months to create our super fast homepage. Here is a blueprint which includes the source code to save yourself a lot of time and work for your own super fast webpage. Speed matters!"
custom_css:
  - blog
---

Our new AMOOMA homepage went online on the 23rd of January 2015. It did take many months of work to get there. I want to share the blueprint for it so that you can save time for your own super fast homepage. *[The code is available on Github](http://github.com/amooma/www.amooma.de). Feel free to copy any idea!*

## WebPerformance Time Budget

AMOOMA GmbH is a small consulting and training company which is based in Germany. Our bread and butter business is in Germany but every now and then we have a customer from a totally different continent. Our homepage informs a potential customer of our portfolio, hosts screencasts and this blog.

It represents us and what we can do. Because we offer WebPerformance consulting **it should load in under 1,000 ms** on any device from Frankfurt to Sydney.

But let's don't fool ourself. "Any device" is not as important as any network. I will settle for 2s on a bad G3 network. Here is a demo of an iPhone 6 accessing www.amooma.de on such a network. Animated GIFs are a bit tricky timing wise. You'll have to trust me or [download the original MOV file]({% asset_path "2015/02/13/iphone-g3-www.amooma.de-landscape-demo.mov" %}) that this is 2 seconds.

{% image "2015/02/13/iphone-g3-www.amooma.de-landscape-demo.gif" alt="G3 Demo" %}

## Ruby on Rails vs. Ember vs. Jekyll

First we had to decide which plattform we use to generate the HTML. Our portfolio contains [Ruby on Rails](/ruby-on-rails/), [Ember](/ember/), [Phoenix](/phoenix/) and [Jekyll](/jekyll/). All these plattforms can be used for this endeavor.

Of course we could show off some [Ember](/ember/) features but at the end of the day it would add some fancyness but not anything significant to the content. But it would mean saying good-bye to that 1,000 ms time budget (at least for now as we are all waiting for [FastBoot](http://emberjs.com/blog/2014/12/22/inside-fastboot-the-road-to-server-side-rendering.html)).

Ruby on Rails would be nice too but let's be honest: What feature would be needed? A login for our clients? C'mon why should a potential client register on our page? What potential benefit could that have for him? But the Rails stack would add at least 35 ms. Probably even a little bit more.

We could have just written plain old HTML files but who wants that?! It is nice to have some sort of framework to add headers, footers, variables and which takes care of assets. So we went with [Jekyll](/jekyll) which is perfect for creating a static page.

## Webserver

I have been a happy [nginx](http://nginx.org/) user for many years now. It is said that it has an edge over [Apache](http://httpd.apache.org/) for delivering static pages. I would not start a religious war about that. Both are fine webservers. On a sidenote: I'm looking forward to a stable [H2O](https://github.com/h2o/h2o) which is looking quite promising for future HTTP/2 use.

Long story short: This page gets delivered by a vanilla current nginx on a vanilla Debian 7 server. Our webserver is located in Frankfurt which is pretty much perfect for Germany. Not so much for Sydney but I'll come back to that later.

## CSS (Framework?)

I'm a fan of [Twitter Bootstrap](http://getbootstrap.com/) but that beast is way too big to be considered for any super fast webpage. [Pure.css](http://purecss.io/) by Yahoo! is a way better start. It's webpage says "The entire set of modules clocks in at 4.4KB minified and gzipped."

But this time I wanted to get the most bang for the buck so I asked [Kasper Tidemann](http://www.kaspertidemann.com/) to write our very own responsive CSS without using any CSS framework (please excuse my very lose use of the work "framework"). Kasper is a gifted coder and designer located in Copenhagen.

*As a side effect of this decision our project time and money budget busted. It did hurt but resulted in the best webperformance possible.*

After many rounds of different solutions we ended up with this:

{% highlight bash %}
$ tree -h _includes/css
_includes/css
├── [1.4K]  bio.css
├── [4.0K]  blog.css
├── [ 231]  faq.css
├── [ 109]  iframe.css
├── [ 18K]  list.css
├── [5.5K]  main.css
├── [ 629]  price_table.css
├── [ 501]  screencast.css
└── [4.3K]  syntax.css
{% endhighlight %}

It's a set of small CSS files which get inlined on demand. Yes, we do inline all needed CSS. We don't use a single (big) CSS file which gets downloaded separately. With this tactic we get all HTML and all CSS the bowser needs to render the webpage with the first 14 KB payload frame.

If you are not familiar with the 14 KB aka TCP Slow-Start problem: Read [High Performance Browser Networking](http://chimera.labs.oreilly.com/books/1230000000545) by [Ilya Grigorik](https://www.igvita.com/). Read it now because otherwise you will not understand the gravity of those 14 KB.

## No extra Web Font

Web Fonts are tricky webperformance wise. It takes time to download and it takes time to render. Take a minute and browse through our [Github repository](http://github.com/amooma/www.amooma.de) to see that we actually started with a web font in the early versions but decided against it in the optimization process. With our very ambitious time budget an extra web font could have been a deal breaker and it wasn't worth it.

## 14 KB max size. Any page. Seriously!?

Yes, as of today any page on the AMOOMA homepage fits into the first 14 KB payload TCP frame. Here is the secret sauce:

{% highlight bash %}
jekyll build

find _site -name "*.html" -exec echo -n "." \; -exec html-minifier --remove-comments --collapse-whitespace --collapse-boolean-attributes --remove-attribute-quotes --remove-redundant-attributes --remove-empty-attributes --remove-optional-tags --minify-urls --minify-js --minify-css -o {} {} \;

find _site -name "*.*ml" -exec echo -n "." \; -exec zopfli --i60 {} \;
{% endhighlight %}

[HTML Minifier](http://kangax.github.io/html-minifier/) gets rid of all the stuff the browser doesn't need (e.g. no need to close the <li> or <body> tag) and optimizes HTML, CSS and JavaScript.

## Why precompressed .gz files?

Normally one would upload the HTML files onto the webserver and let it do the work. But by uploading not only the HTML files but also the precompressed .gz files you save the webserver a little bit of work (see [ngx_http_gzip_static_module](http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html)). Small things count. You can tell nginx to use those precompressed .gz files by adding this line to the config:

{% highlight bash %}
gzip_static  on;
{% endhighlight %}

But the real secret is the use of the  [zopfli](https://code.google.com/p/zopfli/) gzip compatible compressor. It takes a lot more time to compress but results in smaller files. If we'd use `gzip` we would have files which needed a second TCP payload frame:

{% highlight bash %}
$ find _site -size +12k -name "*.html.gz" -exec du -hs {} \;
 16K	_site/ember/index.html.gz
 16K	_site/index.html.gz
 16K	_site/ruby-on-rails/index.html.gz
{% endhighlight %}

## Inlining of Graphics

Many pages don't use the total available 14 KB. So we searched for additional payload because what a waste of unused payload would it be not to.

We asked ourself what images are important and have to be rendered on the browser as fast as possible? It was not the avatar photo of me or my signature which is displayed on our homepage. It is the company logo. The logo is a SVG file which does compress quite well. And it's just shy of 4 KB:

{% highlight bash %}
$ du -hs gfx/logo.svg
4,0K	gfx/logo.svg
{% endhighlight %}

Long story short: We use the [base64 Jekyll Image Encode tool](https://github.com/GSI/jekyll_image_encode) to inline the logo in our CSS (have a look at the CSS code at [github.com/.../www.amooma.de/.../css/main.css](https://github.com/amooma/www.amooma.de/blob/master/_includes/css/main.css)). We did try to not use base64 because we could inline the SVG without encoding it but our good old friend Internet Explorer didn't like that.

We inline other small images too as long as we don't bust the 14 KB limit.

## SpeedIndex: 600

You want to see the waterfall of our index.html, don't you? This is a request from Falkenstein (Germany) with a Chrome browser:

{% image 2015/02/13/waterfall-index-html.png class="pure-img-responsive" alt="Waterfall" %}

The whole analysis is available at [webpagetest.org/result/150212_2Z_M5P](http://www.webpagetest.org/result/150212_2Z_M5P/1/details/).

## Prefetch

Have you seen the download of additional files in the waterfall (e.g. lines 4 - 9)? These are triggered by a [link rel="prefetch"](http://en.wikipedia.org/wiki/Link_prefetching) in the following lines of the `<head>` section:

{% highlight html %}
<link rel="prefetch" href="/blog/">
<link rel="prefetch" href="/screencasts/">
<link rel="prefetch" href="/labs/">
<link rel="prefetch" href="/webperformance/">
<link rel="prefetch" href="/ruby-on-rails/">
<link rel="prefetch" href="/ember/">
{% endhighlight %}

Google Analytics for the last 7 days says that in average - the average is a discussion for itself - every session visits 2.68 pages. By prefetching pages which are likely the next ones a user visits we increase the speed for that user dramatically. The TCP pipes are open already by that time and we have plenty of spare time in our 1,000 ms time budget anyway.

prefetch is a powerful tool to speed up the use of your webpage. But remember: [With great power comes great responsibility!](http://en.wikipedia.org/wiki/Uncle_Ben#.22With_great_power_comes_great_responsibility.22) We prefetch because we have the luxury time budget wise and we know that each page will take less than 14 KB of space in the cash of the browser. I don't want to waste our visitors resources.

To feel the benefit [click here to get to the main page](/). It'll take a jiffy because your browser cached it when you started to read this page.

## Cache

Caching in HTTP is paramount for any webpage. My reading tip for your next boring trans atlantic flight: [rfc2616 (section 13)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)

HTTP Caching offers the browser the opportunity to store content (e.g. HTML, CSS and images) locally and re-use it in the future without asking the webserver about it. Take the waterfall image on this page: It will never change (the [Jekyll asset pipeline](https://github.com/ixti/jekyll-assets) added a MD5 checksum fingerprint to the filename). So we tell the browser to store it for one year and re-use it when ever it needs to without asking the webserver for permission. We do the same thing with this HTML page. But not for a year. We tell the browser that this page is good for two weeks.

The index page is good till tomorrow 6:30 a.m. Because we want to be sure that new blog postings or screencasts are going to be displayed. Here is the nginx configuration for that:

{% highlight text %}
location /index.html {
  gzip_static on;
  expires @06h30m;
  add_header Cache-Control public;
  break;
}
{% endhighlight %}

For the browser this looks like this:

{% highlight bash %}
$ curl -I http://www.amooma.de
HTTP/1.1 200 OK
Server: nginx/1.6.2
Content-Type: text/html
Last-Modified: Wed, 11 Feb 2015 18:44:33 GMT
ETag: "54dba311-c20e"
Expires: Fri, 13 Feb 2015 05:30:00 GMT
Cache-Control: max-age=50355, public
Content-Length: 49678
Accept-Ranges: bytes
Date: Thu, 12 Feb 2015 15:50:54 GMT
Via: 1.1 varnish
Age: 0
Connection: keep-alive
X-Served-By: cache-lhr6328-LHR
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1423756254.210577,VS0,VE53
{% endhighlight %}

BTW: Only because of that we can use prefetch. Otherwise the browser would fetch every page with each new request. So if a user opens www.amooma.de and loses WiFi or GSM connection the very next second he/she will likely not recognise it as long as he/she browses this homepage.

## CDN

Anyone with knowledge in WebPerformance will argue: "That is all fine and dandy for German users but the benefit for a user in Sydney is marginal." And you are right Sir! Therefore we went the next step and deliver the complete website with the [Fastly CDN](http://www.fastly.com/). Because of the heavy use of HTTP caching our nginx gets actually quite a bit bored. The Fastly CDN servers handle 99.9% of all the work and because they are all over the world the overall performance is a lot better.

Frugal companies might argue that a CDN is expensive. According to my experience this is not true (any more!). Big companies can save more by reducing their server farm and small companies don't even hit the 50 USD minimum to trigger the invoice by Fastly ([their pricelist](http://www.fastly.com/pricing/)).

A nice side effect of using a CDN like Fastly: Your page is save from any sort of [Slashdot effect](http://en.wikipedia.org/wiki/Slashdot_effect).

## Images

It should be obvious but you have no idea how often I consult new clients who don't optimize their images. Optimizing images is a craft of its own and this is not the place to solve this problem. So my advice for optimizing images if you don't have time and knowledge to do it optimal in the first place: Go to [kraken.io](https://kraken.io/) and use their service.

## What about SDPY or HTTP/2?

Because we do the heavy lifting within the 14 KB limit we have little benefit from using SDPY or HTTP/2. The needed use of SSL would actually worsen the first download time.

Truth be told: We might be able to safe about 50 - 100ms by the use of HTTP/2 for our index page if there would be a solution to push the two images on that page. But right now neither nginx nor Apache support this feature of HTTP/2 and I don't have the confidence in H2O yet.

I have the feeling that Fastly is cooking a solution for this. But they keep their cards close to their chest. I'll keep you in the loop if there is an update or new technology.

**2015 will become a very interesting year regarding HTTP/2.**

## Questions?

Please feel free to contact me if you have a specific question or feedback. Obviously I'm also happy to offer consulting.
