---
layout: post
title: "Deploy with rsync and purge the CDN with curl"
lang: en
date: 2015-02-16 11:00:00
tags:
  - CDN
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "How-To deploy static content with rsync to your webserver and use curl afterwards to purge the Fastly CDN."
excerpt: "How-To deploy static content with rsync to your webserver and use curl afterwards to purge the Fastly CDN."
custom_css:
  - blog
---

If you work with rsync to deploy static content to your webserver and what to purge those files from your Fastly CDN here is the script.

What the script does:

- Create a tempfile to store the rsynced files.
- rsync the files and log everything in the tempfile.
- Loop through the tempfile. Do some bash magic to run a curl -X PURGE on all synced files.
- Delete the tempfile.

{% highlight bash %}
# Create a tempfile
#
tempfoo=`basename $0`
TMPFILE=`mktemp /tmp/${tempfoo}.XXXXXX` || exit 1

# Deploy
#
rsync -rlpcgoDvz --log-file=$TMPFILE --delete _site/* deployer@webserver-farm.example.com:/var/www/www.example.com/

# Purge the CDN
#
cat $TMPFILE | grep "\] <fc...... " | cut -d \  -f 5 | while read -r filename; do
echo "Purge http://www.example.com/$filename"; curl -X PURGE http://www.example.com/$filename; done
echo

# Remove the tempfile
#
rm -f $TMPFILE
{% endhighlight %}
