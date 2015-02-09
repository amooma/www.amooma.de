---
layout: post
title: "HTTP/2 - Statusreport: Januar 2015"
lang: de
date: 2015-01-28 18:00:00
tags:
  - WebPerf
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "HTTP/2 - Statusreport: Januar 2015. Was wird 2015 bezüglich HTTP/2 passieren?"
excerpt: "HTTP/2 - Statusreport: Januar 2015. Was wird 2015 bezüglich HTTP/2 passieren?"
custom_css:
  - blog
---

HTTP/2 ist der Nachfolger von HTTP/1.1 (mit dem heute jede Webseite ausgeliefert wird) und wird im Laufe des Jahres 2015 richtig interessant. In diesem Blog-Post möchte ich den aktuellen Stand der Dinge/Technik erläutern.

## Ist HTTP/2 das gleiche wie SDPY?

Nein! SDPY (siehe [http://www.chromium.org/spdy/](http://www.chromium.org/spdy/)) ist der Vorgänger von HTTP/2. Es war quasi ein sehr wichtiger Feldversuch, der von Google durchgeführt wurde. Dabei wurden einige Architekturprobleme entdeckt, die man dann bei HTTP/2 direkt lösen konnte. Mehr dazu im [What’s the relationship with SPDY?](http://http2.github.io/faq/#whats-the-relationship-with-spdy) HTTP/2 FAQ Eintrag.

SDPY wird heute schon von sehr vielen großen Webseiten eingesetzt (z.B. Google, Twitter und Facebook). Wer einmal testen möchte, welche Webseite SDPY unterstützt, kann dies auf [SPDYCheck.org](https://spdycheck.org/) überprüfen.

## Status RFC

Das RFC zu HTTP/2 ist noch nicht verabschiedet. Es wird sich wohl zum aktuellen Draft Release 16 (siehe [https://tools.ietf.org/html/draft-ietf-httpbis-http2-16](https://tools.ietf.org/html/draft-ietf-httpbis-http2-16)) nicht mehr viel ändern, aber es gibt halt kein bisschen schwanger. Den Status kann man auf [http://http2.github.io/](http://http2.github.io/) verfolgen.

Frühestens wird das RFC im Februar 2015 abgesegnet.

## Status Browser

[Firefox 35](https://www.mozilla.org/de/firefox/new/) wurde vor ein paar Tagen released und ist der erste Browser, der HTTP/2 (Draft 14) per Default aktiviert hat. Chrome kann mit dem Kommdo-Zeilen-Schalter `--enable-spdy4` ebenfalls mit aktivierten HTTP/2 gestartet werden (alternativ kann man es auf chrome://flags/ aktivieren).

Ich bin mir aber sehr sicher, das alle Browser im Laufe des Jahres 2015 HTTP/2 per Default aktiviert haben werden.

## Status Webserver

Nur ein Webserver unterstützt heute HTTP/2 (Draft 16): [H2O](https://github.com/h2o/h2o). Noch etwas von H2O nie gehört?! Ist auch relativ neu und offiziell im Alpha-Stadium. Die Performance-Werte von H2O sind übrigens imposant.

[Nginx](http://nginx.org/) unterstützt zwar SPDY, aber noch nicht HTTP/2.

[nghttp2](http://nghttp2.org/) ist eine C Bibliothek, die HTTP/2 unterstützt. Aber das ist halt kein Webserver.

Ich hoffe das sich die Situation in den nächsten Monaten ändert. Wahrscheinlich wird von den großen Spielern als erstes Nginx HTTP/2 unterstützen. H2O sollte man aber im Auge behalten.

## Welche Features werden am interessantesten sein?

Geschwindigkeit ist augenscheinlich das wichtigste Feature.

Man wird kein Domain Sharding mehr benötigen. Der ganze Durchsatz wird einfach viel schneller sein. Der Irrsinn mit dem Inline-en von CSS und Grafiken wird mit HTTP/2 der Vergangenheit angehören.

Mein Lieblings-Feature ist allerdings Server-Side-Push. Damit wird der Webserver selbst Inhalte priorisieren können. Das ist für die WebPerformance ein sehr wichtiges Thema.

## Fazit

Ruhe bewahren! HTTP/2 ist am Horzont, aber mehr halt auch nicht. So bald sich diesbezüglich etwas ändert werde ich hier drüber bloggen. Auf Twitter sollte man [@HTTP_2](https://twitter.com/HTTP_2) folgen.

Es ist aber zu empfehlen mal über SDPY nachzudenken. Wenn man heute die Webseite mit SDPY ausliefert, dann ist der Schritt zu HTTP/2 nicht mehr so groß.
