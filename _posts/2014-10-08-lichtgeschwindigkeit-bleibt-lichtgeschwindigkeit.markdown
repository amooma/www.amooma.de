---
layout: post
title: "Lichtgeschwindigkeit bleibt Lichtgeschwindigkeit"
lang: de
date: 2014-10-08 09:00:00
tags: WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: Eine Ladezeiten-Aufstellung einer deutschen Webseite von verschiedenen Orten dieser Welt (z.B. Sydney und New York).
excerpt: "Blog-Post: Eine Ladezeiten-Aufstellung einer deutschen Webseite von verschiedenen Orten dieser Welt (z.B. Sydney und New York)."
custom_css: blog
---
Wenn ich beim Thema Webperformance eine Sache immer und immer wieder erklären muss, dann ist es die immense Wichtigkeit einer geringen Latenz. Für IT-Laien: Glasfaserleitungen verbinden die einzelnen Länder bzw. Kontinente. Die Geschwindigkeit einer Glasfaserleitung ist durch die Lichtgeschwindigkeit begrenzt. Schneller geht nicht! Es macht deshalb keinen Sinn einen Server in den USA für deutsche Kunden zu benutzen. In diesem Posting zeige ich die gewaltigen Zeitunterschiede.

Latenz ist die Zeit, die ein einzelnes Datenpaket vom Server zum Browser benötigt. Diese Zeit ist für die Geschwindigkeit einer Webseite viel **WICHTIGER als die Bandbreite**.

Anhand der `amooma.de` Webseite, die auf einem Webserver in Frankfurt am Main gehostet wird, habe ich mit [webpagetest.org](http://www.webpagetest.org) ein paar Tests mit Browsern in anderen Ländern durchgeführt. Man sieht, das Besucher in den USA doppelt so lange auf die gleiche Webseite warten müssen wie Besucher aus Deutschland. Dabei benötigen Besucher aus Los Angeles noch einmal mehr Zeit als Besucher aus New York. Besucher aus Australien und Neuseeland müssen besonders viel Zeit mitbringen.

<table class="pure-table">
  <thead>
    <tr>
      <th> </th>
      <th>First Byte</th>
      <th>Start Render</th>
      <th>Speed Index</th>
      <th>Test Protokoll</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Falkenstein, Deutschland</td>
      <td>0.143s</td>
      <td>0.390s</td>
      <td>410</td>
      <td><a href="http://www.webpagetest.org/result/141001_8F_M2X/">webpagetest.org/...M2X/</a></td>
    </tr>
    <tr>
      <td>New York, USA</td>
      <td>0.286s</td>
      <td>0.587s</td>
      <td>875</td>
      <td><a href="http://www.webpagetest.org/result/141001_VK_MTX/">webpagetest.org/...MTX/</a></td>
    </tr>
    <tr>
      <td>Los Angeles, USA</td>
      <td>0.452s</td>
      <td>0.694s</td>
      <td>934</td>
      <td><a href="http://www.webpagetest.org/result/141001_Z7_MYZ/">webpagetest.org/...MYZ/</a></td>
    </tr>

    <tr>
      <td>Wellington, Neuseeland</td>
      <td>0.719s</td>
      <td>1.424s</td>
      <td>1.722</td>
      <td><a href="http://www.webpagetest.org/result/141001_GY_M6Y/">webpagetest.org/...M6Y/</a></td>
    </tr>
    <tr>
      <td>Sydney, Australien</td>
      <td>1.069s</td>
      <td>1.295s</td>
      <td>1.699</td>
      <td><a href="http://www.webpagetest.org/result/141001_18_MAD/">webpagetest.org/...MAD/</a></td>
    </tr>
  </tbody>
</table>

## Analyse

Schauen wir uns erst mal den Aufbau bei einem Aufruf innerhalb Deutschlands an. Wichtig ist die grüne Line bei 0.4 Sekunden. Die grüne Linie markiert den Zeitpunkt, an dem der Browser die Seite darstellt.

{% image 2014/10/08/waterfall-falkenstein.png class="pure-img-responsive" alt="Wasserfall für Falkenstein." %}

In den USA dauert der Aufbau mindestens doppelt so lange wie innerhalb von Deutschland. Die grüne Linie ist bei 0.9 Sekunden. Man sieht aber auch, das die Bilder zu diesem Zeitpunkt noch nicht ganz downgeloaded sind.

{% image 2014/10/08/waterfall-los-angeles.png class="pure-img-responsive" alt="Wasserfall für Los Angeles." %}

Der Aufbau der Webseite dauert in Australien und Neuseeland mehr als **4 mal** so lange wie innerhalb von Deutschland. Beim Vergleich des Wasserfalles mit dem deutschen Wasserfall kommen einem die Tränen.

{% image 2014/10/08/waterfall-wellington.png class="pure-img-responsive" alt="Wasserfall für Wellington." %}

## Lösungen für das Problem

Wer internationale Kunden schnell bedienen will, der sollte bei statischen Elementen auf ein CDN zurückgreifen. By dynamischen Elementen kann ein CDN auch helfen, allerdings ist das ein gutes Stück komplizierter. Eine Möglichkeit ist Edge-Site-Include (siehe [Blog-Post von Fastly](http://www.fastly.com/blog/using-esi-part-1-simple-edge-side-include/)). Natürlich können Sie auch selbst Server auf der ganzen Welt verteilen. Das ist aber deutlich teurer und bedarf einer enormen Logistik.

Gerne helfen wir bei der Suche eines guten CDNs und bei der Umsetzung.
