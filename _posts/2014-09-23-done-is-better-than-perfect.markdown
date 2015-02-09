---
layout: post
title: "Done is better than perfect!"
lang: de
date: 2014-09-23 15:00:00
tags:
  - AMOOMA
  - CSS
  - HTML
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: Unsere neue Homepage ist online und als Quellcode auf Github abrufbar.
excerpt: "Blog-Post: Unsere neue Homepage ist online und als Quellcode auf Github abrufbar."
custom_css: blog
---
Die eigene Webseite gefällt einem ja fast nie 100%ig. Da gibt es immer hier und dort ein paar Sachen, die man gerne besser/anders hätte. So ist das auch mit amooma.de. Ausserdem wollte ich schon seit langem wieder ein kleines Blog veröffentlichen. Deshalb gibt es jetzt einen neuen Webauftritt.

Die AMOOMA Webseite wird von mir erdacht, geplant, gebaut und dann auch deployed. Chefsache. Full disclosure: Beim Thema CSS habe ich externe Hilfe eingekauft.

## Was ist wichtig?

Geschwindigkeit! Unsere Webseite sollte schneller sein, als jemals zuvor. Webperformance ist ein Thema, das mir sehr am Herzen liegt und mit unserer neuen Webseite wollte ich zeigen, was heute technisch möglich ist.

## Technik

Die alte Webseite war ein Ruby on Rails Projekt. Die neue Webseite wollte ich erst als Ember.js Projekt erstellen. Ich habe sie sogar mit Ember.js einmal fertig programmiert. Ich wollte unseren Kunden zeigen, wie schnell eine Ember.js Applikation war. Dann bin ich einen Schritt zurück getreten und habe mein Ego kurz mal vor die Tür geschickt.

Unsere Homepage ist keine Applikation! Es ist eine Webseite mit Informationen drauf. Dafür brauche ich nicht den Ember.js Overhead. Die Seite könnte man auch als plain HTML erstellen. So toll eine Technologie wie Ember.js ist, sie darf nie Selbstzweck sein!

Die Seite als plain HTML zu erstellen ist zwar machbar, aber doch reichlich umständlich und schwierig in der Wartung. Ich suchte eine kleines Framework, das mir die ganz grundlegende Tools zur Verfügung stellt und daraus dann eine Webseite generiert. [Jekyll](http://jekyllrb.com/) macht genau das. Man kann mit Markdown oder auch direkt mit HTML Webseiten erstellen und mit 'include' einzelne Dateien einbinden. Das ganze kann man dann recht einfach exportieren.

Um es direkt zu sagen: Ich bin mit dem aktuellen Workflow noch nicht zufrieden. Gerade beim HTML- und CSS-Minimieren ist noch einiges zu tun.

## CSS Framework

Meiner Meinung nach muss eine Webseite responsiv sein. Deshalb habe ich als erstes mit meinem alten Freund [Twitter Bootstrap](http://getbootstrap.com/) angefangen. Dort kenne ich mich am besten aus und es fühlt sich einfach schön an. Dann habe ich mich auf die Suche nach etwas schlankeren gemacht und eine Version mit [Foundation](http://foundation.zurb.com/) erstellt. Mein Lieblingsziel von 14 KB pro Seite war aber mit beiden CSS-Frameworks nicht realisierbar.

[Kasper Tidemann](http://www.kaspertidemann.com/) hat mir dann den entscheidenen Tipp gegeben: [Pure](http://purecss.io/). Um mal die Webseite zu zitieren _"Pure is ridiculously tiny. The entire set of modules clocks in at 4.4KB* minified and gzipped."_
Mit Pure konnte ich ein responsives Layout mit einem minimalen Code Overhead realisieren. Die 14 KB waren realisierbar.

## Github

Ich habe mich entschieden den Source-Code der Webseite auf [https://github.com/amooma/www.amooma.de](https://github.com/amooma/www.amooma.de) für Jedermann zur Verfügung zu stellen. Nicht weil ich besonders stolz auf die Sauberkeit des Codes bin (bin ich nämlich nicht), sondern um die Grundideen zu zeigen und um auch von anderen Entwicklern Feedback zu bekommen. Der Inhalt der Seite ist ja eh für jedermann einsehbar und da war es nur ein kleiner Schritt die Build-Umgebung auch öffentlich verfügbar zu machen.

Ich würde mich über Issues und Pull-Requests sehr freuen. Natürlich auch über E-Mails mit Feedback und Anregungen an [stefan.wintermeyer@amooma.de](mailto:stefan.wintermeyer@amooma.de)

## Nächste Schritte

Die nächsten Wochen werde ich noch ein paar Optimierungen und Verbesserungen vornehmen. Für heute gilt: **Done is better than perfect!**
