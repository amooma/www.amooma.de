---
layout: post
title: "Verbesserung des Speed Index auf 401"
lang: de
date: 2014-09-29 01:00:00
tags:
  - AMOOMA
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: Der Weg von einem Speed Index von 1.700 (alte Seite) auf 401 (neue Seite).
excerpt: "Blog-Post: Der Weg von einem Speed Index von 1.700 (alte Seite) auf 401 (neue Seite)."

custom_css: blog
---
Letzte Woche ging die neue AMOOMA Webseite online. Heute möchte ich ein wenig mehr in die Details der Index-Seite gehen und erklären wie man auf einen [Speed Index](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index) von 401 kommen kann.

## Der Grundgedanke

Eine zweite Chance auf den ersten Eindruck gibt es nicht. Deshalb ist die Index-Seite so enorm wichtig. Sie muss schnell laden und auch optisch schön anzuschauen sein. Und das sowohl auf dem Desktop wie auch auf dem Handy. Mit einem rein textbasierten Ansatz wäre das Geschwindigkeitsproblem leicht lösbar gewesen. Aber so eine Seite sieht schon reichlich trostlos aus. Es sollte auf jeden Fall das AMOOMA Logo als Grafik in der Navigationszeile, ein Avatar-Bild von mir und zentral ein Hintergrund-Foto im Splash-Bereich angezeigt werden.

## Das Ergebnis

Die Seite lädt innerhalb von Deutschland mit einer DSL-Verbindung in ca. 500ms. Auf dem Handy einen Tick länger, aber immer noch unter der magischen 1.000 ms Barriere.

{% image 2014/09/29/webpagetest_filmstrip_20140929.png class="pure-img-responsive" alt="Filmstrip des Seitenaufbaus" %}

## HTML und CSS

Durch Verwendung von [http://purecss.io/](http://purecss.io/) konnte ich das Inline CSS für ein responsives Layout zusammen mit dem HTML-Code der Index-Seite leicht komprimiert innerhalb der 14 KB Grenze übertragen. Ein Round-Trip. Schneller geht es nicht.

## Grafiken und Fotos

Das AMOOMA Logo hat eine Grösse von 4 KB. So viel Platz war noch in der index.html Datei bei zur 14 KB Grenze. Deshalb wurde diese Datei Inline eingefügt. Damit schaft es das Logo zwar nicht in den Browser-Cache, aber der ist ja auch kein Selbstzweck (sprich man muss es immer abwägen). Das Avatar-Foto im unteren Bereich der Seite hat rund 8 KB. Da es aber nicht im Above-The-Fold-Bereich angezeigt wird, hat mir das keine schlaflosen Nächte bereitet.

Das Hauptproblem war das zentrale Splash-Hintergrund-Strand-Foto mit rund 40 KB. Es gab zwei Möglichkeiten:

- Das Foto wird ganz normal nachgeladen. Vom optimischen Bildaufbau nicht ganz so schön (es kann kurz - unter 100ms -  ruckeln), aber bei langsamen Verbindungen mit einer hohen Latenz ist sichergestellt, das der wichtigste Teil (der Text) innerhalb von einem Round-Trip beim Browser ankommt und gerendert werden kann. Den Browser-Cache nehmen wir dann für zukünftige Besuche noch gratis mit.
- Das Foto Inline einzubetten. Das hätte beim Laden zwei Round-Trips mehr gekostet, aber wir wären immer noch weit unter der 1.000 ms Grenze gelandet. Der grosse Vorteil wäre ein ruckelfreier Bildaufbau gewesen.

Ich habe mich für die erste Variante entschieden. Hier ist der Wasserfall:

{% image 2014/09/29/waterfall.png class="pure-img-responsive" alt="Stefan Wintermeyer" %}

Für alle die sich sonst nicht mit Webperformance beschäftigen: Wichtig ist die grüne Linie bei der 0.4 Markierung. Der Browser ist danach zwar noch weiter beschäftigt, aber dabei handelt es sich nur um Google Analytics Code der im Hintergrund abgearbeitet wird. Die Seite ist nach 500 ms fertig gerendert.

## Vorher-Nachher-Vergleich

Damit jeder die Werte nachvollziehen kann, habe ich die Test bei [webpagetest.org](webpagetest.org) abgespeichert und den Link dazu in die Tabelle geschrieben.  

<table class="pure-table">
  <thead>
    <tr>
      <th> </th>
      <th>Start Render</th>
      <th>Visually Complete</th>
      <th>Speed Index</th>
      <th> </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alte Seite</td>
      <td>1.696s</td>
      <td>1.700s</td>
      <td>1700</td>
      <td><a href="http://www.webpagetest.org/result/140923_N4_G32/">webpagetest.org/…/140923_N4_G32</a></td>
    </tr>
    <tr>
      <td>Neue Seite</td>
      <td>0.391s</td>
      <td>0.500s</td>
      <td>401</td>
      <td><a href="http://www.webpagetest.org/result/140929_TQ_HVP/">webpagetest.org/…/140929_TQ_HVP</a></td>
    </tr>
  </tbody>
</table>

Wie immer freue ich mich über Anregungen und Feedback.
