---
layout: post
title: "Orakel in Ethereum"
image: oracle_of_delphi.jpeg
author: "Axel Hodler"
---

Sobald bekannt wird, dass ein [Smart Contract](https://de.wikipedia.org/wiki/Smart_Contract) in Ethereum nicht von selbst mit der Welt ausserhalb der Blockchain interagieren kann, führt dies bei einem Ethereum Einsteiger gerne zu Erstaunen. Ist es nicht die Hauptanwendung eines Smart Contracts abzufragen, ob ein Ereignis (Katastrophen, Sportergebnisse, Aktienkurse) in der Welt eingetreten ist um eine Transaktion einzuleiten?

Angenommen der Smart Contract würde die [Yahoo Finance API](https://developer.yahoo.com/finance/) verwenden um den Aktienkurs von Apple zum Zeitpunkt X zu erfahren. Diese Anfrage müsste von jedem Knoten im Ethereum Netzwerk durchgeführt werden. Leider kann nicht gewährleistet werden, dass jeder Knoten dieselbe Antwort erhält bzw. überhaupt eine Antwort erhält (Stichwort [Great Firewall](https://de.wikipedia.org/wiki/Internetzensur_in_der_Volksrepublik_China)). Als entsprechend schwierig erweist sich die Konsensfindung.

Trotzdem existieren Möglichkeiten Ereignisse außerhalb der Blockchain in diese einzuspeisen. Hierzu verwendet man ein _Orakel_. Ein Orakel kann diverse Formen annehmen, die im Folgenden durchleuchtet werden.

## Einsatz eines Moderators

Im Beispiel einer Sportwette besteht die einfachste Möglichkeit im Einsatz eines Moderators, dem beide Teilnehmerparteien vertrauen. Auf diese Weise kann im Smart Contract festgehalten werden, dass nur der Moderator das Ergebnis melden kann. Dies geschieht durch die Angabe der Ethereum Addresse des Moderators im Smart Contract. Das gemeldete Ergebnis wird im Anschluss verwendet, um den Gewinner der Wette zu ermitteln.

Auf den ersten Blick klingt dies unsicher und risikoreich. Allerdings muss bedacht werden, dass die Meldung des Moderators öffentlich sichtbar und für die Ewigkeit gespeichert wird. Auf diese Weise kann eine Falschangabe den Moderator auf Dauer diskreditieren und ihm zukünftige Moderationsarbeit unmöglich machen. Dennoch ist insbesondere bei hohen Beträgen Vorsicht geboten. Verbesserte Sicherheit kann durch den Einsatz mehrerer Moderatoren, welche untereinander zu einem Konsens kommen müssen, geschaffen werden.

## Automatisierte Orakel

Möchte man das Ergebnis automatisiert einspeisen, so existieren Werkzeuge, die dies anbieten. [TLSNotary](https://tlsnotary.org/) ermöglicht den Nachweis, dass ein bestimmer Server zu einem bestimmten Zeitpunkt eine bestimmte Antwort gesendet hat. Eingesetzt wird TLSNotary beispielsweise bei [Oraclize](http://www.oraclize.it/), einem Anbieter von Orakel Dienstleistungen. Um das Angebot in einem Smart Contract zu verwenden muss eine Anbindung an den Smart Contract von Oraclize stattfinden. Gegen eine Gebühr kann so die Verwendung von TLSNotary abstrahiert werden und der eigene Smart Contract leitet lediglich eine URL an den Smart Contract von Oraclize weiter. Somit kann exemplarisch über eine gewählte URL eines Anbieters, bspw. `https://www.therocktrading.com/api/ticker/BTCEUR`, der aktuelle Kurs von Bitcoin in Euro abgefragt werden. Der Nachweis über die Korrektheit der Antwort kann von Oraclize auf [IPFS](https://ipfs.io/) gespeichert werden und ermöglicht somit die spätere Verifizierung durch den Anwender.

Hierbei muss bekannt sein, dass man den vertrauenswürdigen Bereich der Blockchain verlässt und man gezwungen wird der Antwort des Servers zu vertrauen. Wenn ein Server meldet, dass der Flug XYZ42 von Barcelona nach Paris am 24.11.2016 eine Verspätung von 15 Minuten hatte, muss dies nicht zwingend der Wahrheit entsprechen. So kann ein bösartiger Administrator die Antwort zu seinen Gunsten anpassen oder die Datensätze direkt fälschen. Ein Anbieter wie Oraclize kann folglich nur prüfen, ob die Antwort in der Form XY wirklich so gesendet wurde. Eine Aussage über den Wahrheitsgehalt der Antwort kann nicht getroffen werden. Es liegt demnach in der Hand des Anwenders die Vertrauenswürdigkeit des gewählten Anbieters einzuschätzen.

## Prognosemärkte

[Augur](https://www.augur.net/), eine Plattform zum Erstellen von Prognosemärkten, kann als Orakel hinzugezogen werden. Das Paradebeispiel bei Augur ist die [US-Präsidentschaftswahl 2016](https://de.wikipedia.org/wiki/Pr%C3%A4sidentschaftswahl_in_den_Vereinigten_Staaten_2016). Hierbei kann prognostiziert werden, ob Hillary Clinton oder Donald Trump das Amt übernimmt. Nach der Wahl berichten Besitzer der Augur spezifischen Währung REP (Reputation) den Ausgang des Ereignisses. Falschabstimmung führt zu Verlust der Währung.

Der Ausgang des Ereignisses kann durch einen Smart Contract abgefragt und weiterverwendet werden. Siehe [Augur Dokumentation](http://docs.augur.net/#call-api).

Bei ungewöhnlichen oder für die Welt vermeintlich irrelevanten Ereignissen, wie der Prognose ob [Zwergflusspferd Hannibal in der Wilhelma Stuttgart](http://www.wilhelma.de/nc/de/aktuelles-und-presse/pressemitteilungen/2016/19102016-hannibal-wird-50.html) 55 Jahre Alt wird, muss der Markt für das Ereignis höchstwahrscheinlich selbst angelegt werden, da er noch nicht vorhanden sein wird. Zusätzlich müssen sich genügend Wettteilnehmer finden, damit die Prognose von Relevanz ist.

Offen bleibt die Frage warum man nicht direkt auf der Augur Plattform die Wette eingeht, anstatt das Ergebnis in einem anderen Smart Contract zu verwenden. Auch wenn der vorliegende Artikel sich stark auf Wetten fokussiert, da diese ein greifbares Thema als Einstieg darstellen, gibt es unzählige Möglichkeiten das Ergebnis einzusetzen. Exemplarisch könnte eine Summe von 1000 Ether bei Nichterreichen des 55. Lebensjahres von Zwergflusspferd Hannibal an ein Forschungsinstitut für regenerative Medizin bei Zwergflusspferden ausgeschüttet werden.
