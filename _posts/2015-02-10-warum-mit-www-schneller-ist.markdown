---
layout: post
title: "www.amooma.de ist schneller als amooma.de"
lang: de
date: 2015-02-10 08:00:00
tags:
  - WebPerf
  - CDN
  - AMOOMA
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Damit die AMOOMA Homepage international schneller ausliefert wird, haben wir beim Relaunch von amooma.de auf www.amooma.de und damit auf eine Auslieferung über das Fastly CDN umgestellt."
excerpt: "Damit die AMOOMA Homepage international schneller ausliefert wird, haben wir beim Relaunch von amooma.de auf www.amooma.de und damit auf eine Auslieferung über das Fastly CDN umgestellt."
custom_css:
  - blog
---

Am [23.01.2015 ging unsere neue Homepage online](/2015/01/26/relaunch-webseite.html). Im Zuge dieses Relaunches haben wir den Web-Server Hostnamen von amooma.de auf **www.**amooma.de umgestellt. Das war kein Zufall und in diesem Blog-Post erkläre ich warum.

## Wo ist der Unterschied?

Den Hostnamen amooma.de kann man im [Domain Name System (DNS)](http://de.wikipedia.org/wiki/Domain_Name_System) nur mit einer IP-Adresse verknüpfen. Das ist technisch nicht anders möglich. Den Hostnamen www.amooma.de kann man aber nicht nur auf eine IP-Adresse, sondern auch auf einen [CNAME Resource Record](http://de.wikipedia.org/wiki/CNAME_Resource_Record) routen. Ein CNAME ist so etwas wie ein Alias.

Mit dem Linux Befehl [dig](http://de.wikipedia.org/wiki/Dig_(Software)) kann man rausfinden wie www.amooma.de jetzt im DNS eingetragen ist:

{% highlight bash %}
$ dig www.amooma.de

; <<>> DiG 9.8.3-P1 <<>> www.amooma.de
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 46368
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.amooma.de.			IN	A

;; ANSWER SECTION:
www.amooma.de.		1040	IN	CNAME	global.prod.fastly.net.
global.prod.fastly.net.	1	IN	CNAME	global-ssl.fastly.net.
global-ssl.fastly.net.	1	IN	CNAME	fallback.global-ssl.fastly.net.
fallback.global-ssl.fastly.net.	1 IN	A	23.235.43.184
fallback.global-ssl.fastly.net.	1 IN	A	23.235.43.185

;; Query time: 28 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mon Feb  9 15:00:21 2015
;; MSG SIZE  rcvd: 147
{% endhighlight %}

Wenn Sie diese Seite aufrufen, dann bekommen Sie die HTML-Datei nicht direkt von unserem Web-Server - der in einem Rechenzentrum in Frankfurt steht - sondern von global.prod.fastly.net. Dieser Hostname gehört zum [Content Delivery Network (CDN)](http://de.wikipedia.org/wiki/Content_Delivery_Network) von [Fastly.com](http://www.fastly.com/).

Ein CDN hält Content - in unserem Fall eine HTML-Datei oder genauer gesagt eine gezippte HTML-Datei - als Kopie auf verschiedene Server. Wie lange diese Kopie gültig ist, definiert man per [expires Einstellung](http://nginx.org/en/docs/http/ngx_http_headers_module.html) (dazu werde ich mal einen eigenes Blog-Posting veröffentlichen). CDNs werden oft von großen Software-Firmen eingesetzt, damit z.B. bei einem Software-Update weltweit Millionen von User gleichzeitig ein Software-Paket downloaden können, ohne das ein zentraler Server bzw. eine Server-Farm in die Knie geht. Firmen wie [Netflix](https://www.netflix.com) benutzen sogar eigene CDNs um Video-Content weltweit auszuliefern.

CDNs eigenen sich aber nicht nur zur Lastverteilung, sondern auch zur WebPerformance Optimierung.

## Die DNS Magie von global.prod.fastly.net

So bald ein Webbrowser den Hostnamen global.prod.fastly.net auflöst passiert ein kleines bisschen DNS-Magie: Der Browser bekommt nämlich als Antwort automatisch die IP-Adresse, des CDN-Servers mit der kürzesten Latenz zu ihm mitgeteilt.

Dadurch bekommt ein Webseiten-Besucher aus Australien die HTML-Datei nicht von unserem Web-Server in Frankfurt, sondern von einem CDN-Server in Sydney. Der Geschwindigkeitsunterschied ist wie Tag und Nacht (siehe [Lichtgeschwindigkeit bleibt Lichtgeschwindigkeit](/2014/10/08/lichtgeschwindigkeit-bleibt-lichtgeschwindigkeit.html)).

Seiteneffekt: Das CDN nimmt unserem Web-Server Last ab. Faktisch langweilt sich in Frankfurt unser [Nginx](http://nginx.com/), weil 99% aller Anfragen vom CDN beantwortet werden. Nur wenn dort ein Cache ausläuft muss er eine Anfrage von diesem CDN-Server beantworten. Bei Fastly wird übrigens [Varnish](https://www.varnish-cache.org/) eingesetzt. Falls einmal zeitgleich der Heise-Newticker, der Spiegel, die New York Times und der Papst auf www.amooma.de verweisen würde ([Slashdot-Effekt](http://de.wikipedia.org/wiki/Slashdot-Effekt)), dann würde sich unserer Web-Server immer noch langweilen.

## Was ist mit den Anfragen auf amooma.de?

Jetzt ist es ja nicht so, das über Nacht alle in der Vergangenheit verteilten Visitenkarten automatisch neue Hostnamen für den Webserver enthalten - ehrlich gesagt werde ich deshalb auch nicht direkt morgen neue Visitenkarten bestellen.

Testen wir auf der Shell mal eine Anfrage auf http://amooma.de

{% highlight bash %}
$ curl -I http://amooma.de
HTTP/1.1 301 Moved Permanently
Server: nginx/1.6.2
Date: Mon, 09 Feb 2015 15:18:04 GMT
Content-Type: text/html
Content-Length: 184
Connection: keep-alive
Location: http://www.amooma.de/
{% endhighlight %}

Ein [HTTP response status code 301 Moved Permanently](http://en.wikipedia.org/wiki/HTTP_301) gibt dem Browser den neuen Hostname. Bezüglich der WebPerformance ist das der Worst-Case, weil sich damit der Zugriff auf die Webseite mal eben verdoppelt. Es geht aber keine Anfrage verloren.

## Was kostet der Spass?

Die [Fastly Preisliste ist online](http://www.fastly.com/pricing/). Bei kleinen Webseiten ist der Einsatz von Fastly gratis, weil Fastly erst ab 50 USD eine Rechnung schreibt.

BTW: Unsere großen WebPerformance Kunden sparen durch den Einsatz von Fastly, da sie weniger Webserver und Load-Balancer benötigen. Die Verfügbarkeit wird dadurch übrigens erhöht (weniger Fehlerquellen im Gesamtsystem).

## Besseres Google Ranking durch Schnelligkeit?

Unser SEO Voodoo Priester on duty verweist diesbezüglich auf das Google Webmaster Central Blog Posting [Using site speed in web search ranking](http://googlewebmastercentral.blogspot.de/2010/04/using-site-speed-in-web-search-ranking.html). Wenn durch das CDN unser Google Ranking hoch gehen sollte, dann nehme ich das als angenehmen Seiteneffekt gerne mit. ;-)
