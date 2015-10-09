---
layout: post
title: "Accelerated Mobile Pages"
lang: de
date: 2015-10-09 10:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "Webseiten mit Accelerated Mobile Pages beschleunigen. Wie geht das?"
excerpt: "Webseiten mit Accelerated Mobile Pages beschleunigen. Wie geht das?"
custom_css:
  - blog
---

Diese Woche wurde das [Accelerated Mobile Pages Project](https://www.ampproject.org/) (AMP) veröffentlicht. Es ist eine Open-Source Initiative von US-Firmen, das sich auf die Fahne geschrieben hat statische Webseiten schneller darzustellen. Dabei geht es ganz besonders um die schnelle Darstellung auf mobilen Geräten. Diese US-Firmen sind auch nicht irgendwelche Firmen. Das fängt mit [Google](http://www.google.de), [Twitter](https://www.titter.com) und [Pinterest](http://www.pinterest.com) an und hört bei der [New York Times](http://www.nytimes.com/) auf. Selbst die deutsche [Frankfurter Allgemeine Zeitung](http://www.faz.net/) ist mit im Boot.

Was verbindet diese Firmen? Sie wissen aus Erfahrung, das langsame Webseiten zu hören Abbruch-(Bounce)-Raten führen und das möchten sie vermeiden. Es geht also schlicht ums Geld verdienen. Sie möchten deshalb zusammen die WebPerformance verbessern und mit diesem Project auch nicht-WebPerformance-Profis ein Toolset an die Hand geben um es nachzumachen. **Es handelt sich um ein striktes Set aus Regeln und einer zentralen JavaScript Bibliothek.**

Welche Webseite wird vom Browser am schnellsten gerendert? Eine Webseite, die nur aus HTML-Code besteht. Am zweit schnellsten wird eine Webseite aus HTML- und CSS-Code gerändert. Fonts sind dann oft schon die erste Bremse, aber das Hauptproblem ist immer - meist suboptimales - JavaScript.

AMP sagt deshalb knallhart: **Ihr dürft kein eigenes JavaScript benutzen.** Peng! Es muss zwar eine zentrale AMP-JavaScript-Bibliothek geladen werden, aber es darf kein eigenes oder beliebiges 3rd-Party JavaScript geladen werden. Das muss man erst mal verdauen.  Zusätzlich muss die Größe bzw. das Seitenverhältnis von zu ladenen Resourcen (z.B. Bildern) im HTML-Code angegeben werden. Es kann damit nicht mehr vorkommen, das ein Seiten-Element später sagt "Ach, ich brauche jetzt aber 500 Pixel mehr Platz. Lass mich gerade mal alles hier verschieben!"

Wenn man das gemacht hat, dann können AMP HTML Dokumente mit einer kleinen Anzahl von HTTP-Requests geladen werden:

* das HTML-Dokument inkl. CSS
* der Font (falls benutzt)
* die AMP JavaScript Bibliothek (welche wahrscheinlich schon im Browser-Cache ist)

AMP kümmert sich um das Nachladen von Resourcen (z.B. Bildern). Da initial nicht so viele Daten benötigt werden, ist das Laden der Seite schon mal viel schneller.

## prerender

Man muss natürlich sagen, das es bis jetzt noch nichts Neues ist. Es ist klar, das der Verzicht auf suboptimales JavaScript zu einer schnelleren Webseite führt. Der Haupttrick kommt jetzt.

Mit AMP kann man dem Browser sagen: "Lade doch schon mal diese Seite und rendere den inital sichtbaren Teil." Wenn der User auf den Link zur entsprechenden Seite klickt, dann hat der Browser den initial sichtbaren Teil der Seite (above the fold) bereits fertig gerendert und kann ihn direkt darstellen. Das führt dann zu einem sehr schnellen Surf-Erlebnis.

Eine Application wie Twitter kann dann Seiten im Voraus laden. Da diese Seiten minimal sind und beim Prerendern drauf geachtet wird, das nicht zu viel CPU-Zeit verbraucht wird, kann man mehr "auf Verdacht" laden. Als Beispiel nehme ich mal diese Seite. Ich weiß anhand von Google-Analytics, das über 50% der User auf einen Menü-Punkt in der Navigations-Leiste klicken. Da diese Seiten jeweils - komprimiert - weniger als 14 KB gross sind, kann ich die Hauptverdächtigen laden ohne dem User dadurch grosse Nachteile zu machen (14 KB sind heute nichts). Klicken Sie jetzt einfach mal auf einen Navigations-Punkt in der Seiten-Navigation, um den Effekt selbst zu sehen/spüren. Danach probieren Sie das später bitte noch mal mit einem Handy aus. Schnell genug? ;-)

Bei einer Zeitungs-Seite wie der New York Times ist der Effekt natürlich ebenso: Man kann prognostizieren auf welchen Link sehr viele User klicken werden.

Zusätzlich kann sehr schnelle Previews im Browser anzeigen. Nutzer vom iPhone 6s kennen den Effekt. Wenn man mit 3D-Touch feste auf einen Link drückt, dann öffnet sich eine Preview-Ansicht der neuen Webseite. Mit AMP werden solche Mechanismen schneller.

## Alter Wein in neuen Schläuchen?

Meiner Meinung nach geht es bei AMP im ersten Schritt gar nicht um die Technologie, sondern darum das jeder Entwickler Argumente für WebPerformance Technologien bekommt. Es ist so viel einfacher zu entscheiden, auf JavaScript zu verzichten, wenn es grosse Firmen ebenfalls machen. Man kann sagen "Aber die Frankfurter Allgemeine und die New York Times machen das auch so!"

Bei vielen Projekten höre ich "wir brauchen aber dieses JavaScript UNBEDINGT und das hier auch und das auch noch". Am Ende lädt man dutzende JavaScript-Dateien, nur weil im Marketing jemand eine Tracking-Funktion haben will, den er nie benutzt. Schnelle Webseiten bekommt man nicht durch schnellere Server, sondern durch intelligente Programmierung der Seite.

**AMP zwingt zur intelligenten Benutzung von HTML und CSS.**

## Wirklich gar kein JavaScript?

Es geht bei AMP um die WebPerformance und deshalb wird selbstgeschriebenes oder 3rd-Party JavaScript in der Seite verboten. Es gibt aber eine gute Lösung dafür: Custom Elements und Web Components. Natürlich kann darin wieder JavaScript stecken, aber dadurch das die AMP-Bibliothek den Hut aufhat, kann sie sicher stellen, das es wenig Performance Einbussen gibt.

## Wie geht es weiter?

Die üblichen Verdächtigen werden Stück für Stück auf AMP umstellen. Das wird gerade das mobile Surfen stark verschnellern. Mit etwas Glück wird im gleichen Schritt auch direkt noch auf HTTP/2 umgestellt.

Webseiten mit einer schlechten WebPerformance sterben heute schon einen langsamen Tod. Das wird durch AMP nur ein wenig beschleunigt.

## Shameless Plug

Natürlich helfe ich Ihnen gerne bei der Adaption von AMP bzw. bei der Implementierung einer besseren WebPerformance. Bitte schreiben Sie mir eine E-Mail an stefan.wintermeyer@amooma.de mit einer kleinen Beschreibung Ihres Projektes. Ich melde mich dann mit einem Vorschlag oder direkt mit einem konkreten Angebot.

Mehr zum Thema WebPerformance können Sie auch in meinem [iX 8/2015 Artikel](http://www.heise.de/ix/inhalt/2015/8/102/) lesen.
