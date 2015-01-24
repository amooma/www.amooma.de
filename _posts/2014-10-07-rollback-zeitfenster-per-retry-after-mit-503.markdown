---
layout: post
title: "Rollback Zeitfenster per 503 plus Retry-After Header"
lang: de
date: 2014-10-07 11:00:00
tags: SEO
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: Wie kann man bei einem Relaunch dem Google Crawler sagen, das er später noch mal wieder kommen soll?
excerpt: "Blog-Post: Wie kann man bei einem Relaunch dem Google Crawler sagen, das er später noch mal wieder kommen soll?"
custom_css:
  - blog
  - syntax
---
Beim Relaunch von komplexen Webseiten besteht immer ein Restrisiko, das einem ein unvorhergesehener Seiteneffekt ins Knie schießt. Gerade bei Seiten, die mit legacy Systemen kommunizieren oder beim kompletten Umstieg von einer Programmierplattform auf eine andere.

## Worst Case Scenario Rollback

Wenn man den Relaunch auf die neue Plattform gestartet hat, dann wird - je nach Lust und Laune des Google Crawlers - irgendwann der neue Content und damit die neue Struktur von Google eingelesen. Das ist ein zwei schneidiges Schwert. Wenn man sich seiner Sache 100%ig sicher ist, dann will man, das Google so schnell wie möglich die neue Seiten-Struktur und den neuen Content indiziert (dazu werde ich noch mal einen extra Blog-Eintrag schreiben). Wenn man aber ein gewisses Rest-Risiko abdecken will, dann wäre ein Zeitfenster für einen möglichen Rollback auf die alte Plattform praktisch. Das bedeutet natürlich auch, das man für diesen Schwenk die technischen Möglichkeiten schaft (z.B. Umstellung des DNS-Eintrages oder Anpassung eines Loadbalancers).

Die SEO-Voodoo-Priester sagen im allgemeinen, das man ein zwei Tage Zeitfenster hat, um noch mal alles zurück zu drehen. Dummerweise finden sich dafür keine Informationen bei Google. Wer lieber auf Nummer sicher gehen will, der nimmt besser eine 503 (Service unavailable) Fehlermeldung.

## 503 (Service unavailable)

Die HTTP-Fehlermeldung 503 sagt aus, das der Webserver gerade nicht läuft.

Siehe [https://support.google.com/.../answer/40132](https://support.google.com/webmasters/answer/40132?hl=en&ref_topic=4610900):

*"The server is currently unavailable (because it is overloaded or down for maintenance). Generally, this is a temporary state."*

Ein 503 bedeutet für den Google Crawler, das er später einfach noch mal nachschaut. Das funktioniert aber nur innerhalb eines bestimmten Zeitfensters. Wenn eine Webseite *ständig* 503s ausliefert, dann "denkt" sich Google das dort etwas nicht stimmen kann und nimmt im Zweifel die Seite aus dem Index.

## Retry-After Header

503 ist vielen Webmastern bekannt. Für unser Zeitfenster-Problem brauchen wir aber noch eine weitere Option. Im (RFC 2616)[http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.37] steht die Definition eines *Retry-After* Headers. Mit diesem kann man einer Suchmaschine sagen, das ein weiterer Versuch erst in x Sekunden oder zu einer bestimmten Uhrzeit stattfinden soll.

Beispiel für eine Uhrzeit:

```
Retry-After: Fri, 31 Dec 2015 23:59:59 GMT
```

Beispiel für eine Sekunden-Angabe:

```
Retry-After: 300
```

## robots.txt als zentraler Schalter

Am besten ist es sicherlich, wenn man auf allen Seiten einen solchen 503 ausgibt. Wenn das allerdings technisch nicht möglich oder zu aufwendig ist, dann kann man auch die `robots.txt` Datei als zentralen Schalter nehmen. Der Inhalt wird dabei nicht verändert. Es wird nur ein 503 ausgegeben.

Da jeder Crawler zu erst die `robots.txt` einliest, hat man damit einen unkomplizierten An-/Aus-Schalter.

## Zusammenfassung

Es liegt in der Natur einer Suchmaschine, das diese nicht Ewigkeiten - ohne Bestrafung des Ratings - auf neuen Content und auf eine neue Struktur warten kann. Aber es liegt auch im Interesse jeder Suchmaschine eine Webseite nicht künstlich zu bestrafen. Man darf sich diesbezüglich nicht von "wohlwollenden" SEO-Consultans ins Boxhorn jagen lassen.

Die Kombination aus `503` und `Retry-After` ist der von [Google empfohlene](http://googlewebmastercentral.blogspot.de/2011/01/how-to-deal-with-planned-site-downtime.html) Weg.

BTW: Wenn Ihr SEO-Consultant diesen Weg nicht vorgeschlagen hat bzw. ihn kennt, dann spricht das Bände.

## Weitere Informationen

Ein paar weiterführende Links zu diesem Thema:

- [How to deal with planned site downtime](http://googlewebmastercentral.blogspot.de/2011/01/how-to-deal-with-planned-site-downtime.html)
- [Website outages and blackouts the right way](https://plus.google.com/+PierreFar/posts/Gas8vjZ5fmB)
- [HTTP 503: Handling site maintenance correctly for SEO](https://yoast.com/http-503-site-maintenance-seo/)
- [RFT 2616 - Retry-After](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.37)
