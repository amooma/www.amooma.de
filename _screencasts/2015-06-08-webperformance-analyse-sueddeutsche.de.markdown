---
layout: screencast
title: "WebPerformance Tipps für www.sueddeutsche.de"
lang: de
date: 2015-06-08 09:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Tipps zur Verbesserung der WebPerformance von www.sueddeutsche.de"
excerpt: "Screencast: Tipps zur Verbesserung der WebPerformance von www.sueddeutsche.de"
youtube_video_id: cR1bnQy_p1Y
video_length: 09:03 min
custom_css:
  - blog
  - syntax
  - screencast
---

In dieser Woche veröffentliche ich täglich zu jeweils einer großen deutschen Nachrichten-Webseiten eine kurze (5-10 Minuten) Analyse zur WebPerformance. Alle Screencasts habe ich am Sonntag (07.06.2015) innerhalb von 2 Stunden aufgenommen.

Heute fange ich mit [sueddeutsche.de](http://www.sueddeutsche.de) an.

Die Webpagetest Daten der Analyse kann man sich unter [webpagetest.org/result/150607_YJ_D8F](http://www.webpagetest.org/result/150607_YJ_D8F) anschauen.

So viel vorweg: Die Seite fängt bei leeren Cache und mit einer DSL-Verbindung erst nach 8,3 Sekunden mit dem Rendern an. Das ist sehr langsam. Es gibt ein paar Low-Hanging-Fruits, die man sehr schnell mitnehmen und damit ohne große Mühe 1-2 Sekunden reinholen kann.

{% image 2015/06/08/screenshots-webperf-analyse-sueddeutsche.de.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus www.sueddeutsche.de" %}
