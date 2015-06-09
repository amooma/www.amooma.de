---
layout: screencast
title: "WebPerformance Tipps für www.zeit.de"
lang: de
date: 2015-06-10 08:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Tipps zur Verbesserung der WebPerformance von www.zeit.de"
excerpt: "Screencast: Tipps zur Verbesserung der WebPerformance von www.zeit.de"
youtube_video_id: oOh0FNxnHiE
video_length: 07:32 min
custom_css:
  - blog
  - syntax
  - screencast
---

In dieser Woche veröffentliche ich täglich zu jeweils einer großen deutschen Nachrichten-Webseiten eine kurze (5-10 Minuten) Analyse zur WebPerformance. Alle Screencasts habe ich am Sonntag innerhalb von 2 Stunden aufgenommen.

Heute gibt es Tipps zur WebPerformance bei  [zeit.de](http://www.zeit.de).

Die Webpagetest Daten der Analyse kann man sich unter [webpagetest.org/result/150607_CT_DXA](http://www.webpagetest.org/result/150607_CT_DXA) anschauen.

So viel vorweg: Die Seite lädt schneller als [sueddeutsche.de](http://www.sueddeutsche.de) und [spiegel.de](http://www.spiegel.de). Aber bei leeren Cache und mit einer DSL-Verbindung fängt der Chrome auch bei dieser Seite erst nach 7s mit dem Rendern an. Das ist immer noch sehr langsam.

Auch bei dieser Seite gibt ein paar Low-Hanging-Fruits, die man sehr schnell mitnehmen und damit ohne große Mühe 1-2 Sekunden rausholen kann:

* Der initale 301 Redirect von http://www.zeit.de auf http://www.zeit.de/index geht gar nicht. Das ist pure Zeitverschwendung.
* Als 3. Datei wird ein JavaScript geladen, das zum Testzeitpunkt nicht verfügbar war. Ein 404 so früh ist pure Zeit- und Resourcenverschwendung.
* Die auf http://scripts.zeit.de gehosteten JavaScripts müssen zusammengeführt und als eine Datei geladen werden. Auch hier pure Resourcenverschwendung.

**Ich bin aber davon überzeugt, das man diese Seite mit ein wenig Arbeit richtig schnell bekommen kann. Das Potential ist auf jeden Fall da.**

{% image 2015/06/10/filmstrip-view-zeit.de.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus www.zeit.de" %}
