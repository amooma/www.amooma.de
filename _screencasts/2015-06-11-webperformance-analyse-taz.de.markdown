---
layout: screencast
title: "WebPerformance Tipps für www.taz.de"
lang: de
date: 2015-06-11 08:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Tipps zur Verbesserung der WebPerformance von www.taz.de"
excerpt: "Screencast: Tipps zur Verbesserung der WebPerformance von www.taz.de"
youtube_video_id: 9_VqmdVkvnE
video_length: 08:28 min
custom_css:
  - blog
  - syntax
  - screencast
---

In dieser Woche veröffentliche ich täglich zu jeweils einer großen deutschen Nachrichten-Webseiten eine kurze (5-10 Minuten) Analyse zur WebPerformance. Alle Screencasts habe ich am Sonntag (07.06.2015) innerhalb von 2 Stunden aufgenommen.

Heute gibt es Tipps zur WebPerformance bei  [taz.de](http://www.taz.de).

Die Webpagetest Daten der Analyse kann man sich unter [webpagetest.org/result/150607_A4_EB3](http://www.webpagetest.org/result/150607_A4_EB3) anschauen.

So viel vorweg: Die Seite lädt schneller als [sueddeutsche.de](http://www.sueddeutsche.de), [spiegel.de](http://www.spiegel.de) und [zeit.de](http://www.zeit.de). Bei leeren Cache und mit einer DSL-Verbindung fängt der Chrome bei dieser Seite nach 1.6s mit dem Rendern an. Das ist schnell.

Auch bei dieser Seite gibt ein paar Low-Hanging-Fruits, die man sehr schnell mitnehmen und damit wahrscheinlich auf eine Ladezeit von knapp über einer Sekunde kommt:

* Die CSS-Dateien auf [taz.de](http://www.taz.de) müssen zu einer Datei zusammengefügt werden.
* Alle Bilder sollten mit [kraken.io](http://www.kraken.io) oder einem ähnlichen Dienst optimiert werden.

**Bezüglich der WebPerformance ist diese Seite der Gewinner von allen fünf getesteten Nachrichten-Webseiten. Und das mit einem ziemlichen Abstand.**

{% image 2015/06/11/filmstrip-view-taz.de.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus www.taz.de" %}
