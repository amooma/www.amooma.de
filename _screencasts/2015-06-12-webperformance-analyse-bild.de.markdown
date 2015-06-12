---
layout: screencast
title: "WebPerformance Tipps für www.bild.de"
lang: de
date: 2015-06-12 08:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Tipps zur Verbesserung der WebPerformance von www.bild.de"
excerpt: "Screencast: Tipps zur Verbesserung der WebPerformance von www.bild.de"
youtube_video_id: i9HzIWrBilk
video_length: 06:42 min
custom_css:
  - blog
  - syntax
  - screencast
---

In dieser Woche veröffentliche ich täglich zu jeweils einer großen deutschen Nachrichten-Webseiten eine kurze (5-10 Minuten) Analyse zur WebPerformance. Alle Screencasts habe ich am Sonntag (07.06.2015) innerhalb von 2 Stunden aufgenommen.

Heute gibt es Tipps zur WebPerformance bei  [bild.de](http://www.bild.de).

Die Webpagetest Daten der Analyse kann man sich unter [webpagetest.org/result/150607_4M_EXE](http://www.webpagetest.org/result/150607_4M_EXE) anschauen.

So viel vorweg: Die Seite ist sehr grafiklastig. Wunder bei der Ladezeit sind da nicht zu erwarten. Bei leeren Cache und mit einer DSL-Verbindung fängt der Chrome bei dieser Seite nach 9.608s mit dem Rendern an. Das ist schnell.

Auch bei dieser Seite gibt ein paar Low-Hanging-Fruits, die man sehr schnell mitnehmen und damit wahrscheinlich auf eine Ladezeit von knapp über einer Sekunde kommt:

* Die CSS-Dateien auf [bild.de](http://www.bild.de) müssen zu einer Datei zusammengefügt werden.
* Die JavaScript-Dateien auf [bild.de](http://www.bild.de) müssen zu einer Datei zusammengefügt werden.
* Alle Bilder sollten mit [kraken.io](http://www.kraken.io) oder einem ähnlichen Dienst optimiert werden.



{% image 2015/06/12/filmstrip-view-bild.de.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus www.bild.de" %}
