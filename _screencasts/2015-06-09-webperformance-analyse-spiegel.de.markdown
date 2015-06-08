---
layout: screencast
title: "WebPerformance Tipps für www.spiegel.de"
lang: de
date: 2015-06-09 08:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Tipps zur Verbesserung der WebPerformance von www.spiegel.de"
excerpt: "Screencast: Tipps zur Verbesserung der WebPerformance von www.spiegel.de"
youtube_video_id: H1U932ILSpg
video_length: 08:12 min
custom_css:
  - blog
  - syntax
  - screencast
---

In dieser Woche veröffentliche ich täglich zu jeweils einer großen deutschen Nachrichten-Webseiten eine kurze (5-10 Minuten) Analyse zur WebPerformance. Alle Screencasts habe ich am Sonntag innerhalb von 2 Stunden aufgenommen.

Heute gibt es Tipps zur WebPerformance bei  [spiegel.de](http://www.spiegel.de).

Die Webpagetest Daten der Analyse kann man sich unter [webpagetest.org/result/150607_B9_DKR](http://www.webpagetest.org/result/150607_B9_DKR) anschauen.

So viel vorweg: Die Seite fängt bei leeren Cache und mit einer DSL-Verbindung erst nach 8,952s mit dem Rendern an. Das ist sehr langsam. Auch bei dieser Seite gibt ein paar Low-Hanging-Fruits, die man sehr schnell mitnehmen und damit ohne große Mühe 1-2 Sekunden rausholen kann.

{% image 2015/06/09/filmstrip-view-spiegel.de.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus www.spiegel.de" %}
