---
layout: post
author: "Jan-Paul Buchwald"
title: "ethverein - Vereinsverwaltung mit Smart Contracts"
image: "ethverein-MyVerein.png"
---

## Highlights (aka TL;DR)
* Mit ethverein existiert ein Prototyp, um wichtige Vereinsvorgänge wie die Aufnahme neuer Mitglieder und Abstimmungen über eine Ethereum Decentralized App abzubilden.
* Durch die Verbriefung von Entscheidungen und Abstimmungsergebnissen in einer öffentlichen Blockchain besteht die Perspektive eines behördlich anerkannten neuen Typs von Vereinen als Zwischenstufe zwischen dem nicht eingetragenen und eingetragenen Verein.
* Der Prototyp-Code ist [Open Source auf GitHub](https://github.com/blocklab/ethverein) verfügbar.
* blockLAB nutzt den Prototyp momentan für echte Vereinsvorgänge in einer Beta-Test-Phase auf dem Ethereum Ropsten Testnetz unter [ethvere.in](http://ethvere.in).


## Motivation
blockLAB Stuttgart ist seit einiger Zeit bereits ein "nicht eingetragener Verein (n.e.V)" und hat vor kurzem in einer Mitgliederversammlung die Umwandlung in einen eingetragenen Verein (e.V.) beschlossen. Nach deutschem Recht unterliegen vor allem eingetragene Vereine gewissen Vorschriften zur Anmeldung bzw. Eintragung von Satzungsänderungen oder der Berufung von Vereinsvorständen beim Vereinsregister. Ebenso sollten auch die alltäglichen Vereinsvorgänge wie die Aufnahme neuer Mitglieder oder sonstige Beschlüsse für die Vereinsmitglieder transparent und nachvollziehbar sein. Aus diesem Grund liegt es für das Blockchain-Experten-Netzwerk blockLAB nahe, für relevante Vereinsvorgänge eine Blockchain bzw. Smart Contracts zu nutzen. Mit ethverein existiert hierzu mittlerweile ein Prototyp auf Basis Ethereum, der in einem ersten Schritt die Mitgliederverwaltung und verschiedene Abstimmungsvorgänge im Verein über eine Decentralized App realisiert. Diese Lösung soll mittelfristig ausgebaut werden, so dass immer mehr Abschnitte der "analogen" Vereinssatzung mit Smart Contracts in der Blockchain abgebildet werden können. In diesem Beitrag werden die Funktionen und Hintergründe der umgesetzten Anwendung unter einem eher technischen Fokus beleuchtet und ein Ausblick auf weitere Schritte gegeben.

## Funktionsbeschreibung
ethverein bietet in der ersten Fassung folgende Funktionen.

### Mitgliederverwaltung
Nutzer werden über einen Ethereum-Account (Adresse bzw. Public Key) identifiziert, zur einfacheren Zuordnung kann jeder Nutzer für sich einen "Alias" (Klartextname oder "Nickname") setzen. Nutzer haben einen von vier möglichen Status "Kein Mitglied", "Mitgliedsantrag gestellt", "Normales Mitglied" oder "Vorstandsmitglied". Die Ethereum-Adressen der Vorstandsmitglieder werden initial festgelegt, weitere Ethereum-Accounts können sich für eine Mitgliedschaft bewerben. Wenn alle aktuellen Vorstandsmitglieder der Bewerbung zugestimmt haben, wird der Nutzer zum normalen Mitglied. Nutzer können ihre eigene Mitgliedschaft auch beenden, womit sie wieder auf den Status "Kein Mitglied" zurückfallen. Die Liste der Mitglieder und offenen Mitgliedsanträge ist jeweils aktuell und transparent in der Blockchain einsehbar.

### Abstimmungen
Jedes Vereinsmitglied kann verschiedene Arten von Abstimmungen anstoßen oder an Abstimmungen teilnehmen. Die Abstimmungen sind dabei immer als "Ja"/"Nein"-Entscheidungen gestaltet und müssen explizit von einem Vereinsmitglied geschlossen werden. Das Schließen einer Abstimmungen ist nur möglich, wenn die Hälfte der Vereinsmitglieder für "Ja" oder "Nein" gestimmt hat. Folgende Arten von Abstimmungen werden momentan unterstützt:
* **Bestätigung eines Dokuments** (z.B. Protokoll einer Mitgliedsversammlung, Beschlüsse etc.) - es wird über den Hashwert einer Datei abgestimmt, die den Vereinsmitgliedern auf anderem Wege, z.B. per E-Mail oder eine gemeinsame Dateiablage, zugegangen ist. Idee ist, dass damit die in dem zugehörigen Dokument protokollierten Geschehnisse oder Beschlüsse auf der Blockchain verbrieft werden.
* **Bestätigung eines Dokuments und der Wahl des Vorstands** - es wird über den Hashwert einer Datei und eine Liste von Ethereum-Adressen der neuen Vorstandsmitglieder abgestimmt. Die Datei ist typischerweise das Protokoll einer Mitgliederversammlung, in der ein neuer Vereinsvorstand gewählt wurde. Über die zusätzliche Abstimmung auf der Blockchain wird diese Wahl verbrieft, und bei Schließen der Abstimmung mit dem Ergebnis "Ja" werden die Vorstandsmitglieder automatisch auf die Ethereum-Adressen aus der Liste der Abstimmung gesetzt.
* **Bestätigung eines neuen Smart Contracts** - es wird über eine Ethereum-Adresse abgestimmt, die den Code eines aktualisierten Smart Contract für die Abstimmungen im Verein enthält. Damit können z.B. geänderte Bedingungen für Abstimmungen wie notwendige Mehrheiten oder Quoren geändert werden. Bei Schließen der Abstimmung mit dem Ergebnis "Ja" wird der Contract automatisch aktualisiert. Zu der angegebenen Ethereum-Adresse des neuen Contracts sollte der Source Code auf Etherscan hinterlegt sein, so dass sich die Mitglieder von der Korrektheit selbst überzeugen können.

## Softwarearchitektur
Die grundsätzliche Architektur der Lösung ist relativ schlank gehalten und entspricht dem Ansatz einer Ethereum Decentralized App (siehe folgende Abbildung): Die wesentliche Datenhaltung und Business-Logik  ist in zwei miteinander verbundenen Smart Contracts realisiert, diese werden von einer Angular-basierten Web App über die web3.js Library angesprochen. Dazu benötigt der Nutzer einen Browser mit Ethereum-Wallet (z.B. Chrome mit MetaMask Extension).

![Architekturübersicht](/assets/images/ethverein-architecture.png "Architektur von ethverein")

Sämtlicher Source Code ist über ein [öffentliches GitHub Repository](https://github.com/blocklab/ethverein) verfügbar.

## Smart Contracts
Die beiden Smart Contracts wurden mit dem [Truffle-Framework](https://truffleframework.com/) in Solidity entwickelt und mit JavaScript-basierten Tests getestet. Die Zuständigkeiten der Contracts sind dabei folgendermaßen:
* Der **Members Contract** wird mit einer Liste von Adressen der initialen Vorstandsmitglieder initialisiert und beinhaltet alle Funktionen bezüglich der Mitgliederverwaltung (Mitgliedsantrag und dessen Bestätigung durch die Vorstandsmitglieder, Setzen/Ändern des Alias, Austritt aus dem Verein, Ersetzen der Vorstandsmitglieder, diverse Abfragen wie Anzahl Mitglieder, Status einer Adresse).
* Der **Voting Contract** wird mit der Adresse des Members Contract initialisiert und beinhaltet alle Funktionen bezüglich Abstimmungen (Anlegen von neuen Abstimmungen, Abgeben einer Stimme zu einer Abstimmung, Schließen einer Abstimmung, diverse Abfragen zu offenen Abstimmungen, deren Status oder Details).
* Der Members Contract hält ebenfalls eine Referenz auf den Voting Contract, die im Rahmen einer erfolgreichen Abstimmung zur Bestätigung eines neuen Voting Smart Contract aktualisiert werden kann. Hierzu wird geprüft, dass nur der bisherige Voting Contract die Funktion zum Update der Contract Adresse aufrufen kann. Die initiale Adresse des Voting Contracts kann nur einmalig vom Owner (d.h. Deployer) des Members Contracts gesetzt werden.

## Benutzeroberfläche
Das Frontend wurde als Angular 6 basierte Single Page Web Application entwickelt, die über einen Angular Service pro Contract per web3.js Library und den von Truffle generierten ABIs mit den Smart Contracts interagiert.

Die Bedienung ist aufgeteilt in vier Ansichten:

1. Dashboard für die auf den aktuellen Benutzer bezogenen Daten und Aktionen ("My Verein")
2. Übersicht / Tabelle der Mitglieder
3. Übersicht / Tabelle der Abstimmungen
4. Starten einer neuen Abstimmung

### Ansicht "My Verein"
Der folgende Screenshot gibt einen Eindruck der Ansicht "My Verein": es werden links der Alias, die Ethereum-Adresse und der Status des aktuellen Benutzers angezeigt. Rechts werden offene Abstimmungen angezeigt, für die der aktuelle Nutzer noch nicht abgestimmt hat; für Vorstandsmitglieder beinhaltet dies auch die Abstimmung über die Aufnahme neuer Mitglieder.

![Ansicht My Verein](/assets/images/ethverein-MyVerein.png "Dashboard-Ansicht My Verein")

Ein Klick auf eine Abstimmung öffnet einen Layer, der je nach Typ der Abstimmung weitere Details anzeigt und eine Bestätigungs- bzw. Wahlmöglichkeit bietet, die dann als Ethereum-Transaktion bestätigt werden muss (siehe weiterer Screenshot).

![Maske zur Abstimmung](/assets/images/ethverein-CastVote.png "Maske zur Abstimmung über ein Dokument")

### Ansichten "Members" und "Votes"
In den Ansichten "Members" und "Votes" werden jeweils Tabellen der Mitglieder und Abstimmungen angezeigt. In der Mitgliedertabelle (siehe beispielhafter Screenshot unten) können Vorstandsmitglieder ebenfalls die Bestätigung von Aufnahmeanträgen neuer Mitglieder vornehmen, in der Tabelle der Abstimmungen kann eine Abstimmung geschlossen werden, wenn deren Ergebnis feststeht.

![Ansicht Mitgliederliste](/assets/images/ethverein-MembersList.png "Mitgliederliste")

### Ansicht "New Vote"
Über diese Ansicht können Mitglieder neue Abstimmungen gemäß einem der unterstützten Typen von Abstimmungen starten. Für Abstimmungen mit Dokumentenbezug können Dokumente geladen bzw. per Drag&Drop in den Browser gezogen werden, die Anwendung berechnet dann den entsprechenden Hash des Dokuments nach SHA256. Für die Adressen der neuen Vorstandsmitglieder können nur vorhandene Mitglieder ausgewählt werden.

![Starten einer Abstimmung](/assets/images/ethverein-InitiateVote.png "Starten einer Abstimmung")

## Testbetrieb
Die Smart Contracts wurden zunächst für den Testbetrieb auf dem Ethereum Ropsten Testnet deployed, die Decentralized App auf einem Web Server unter [ethvere.in](http://ethvere.in) verfügbar gemacht. Damit ist ein erster "öffentlicher" Testbetrieb mit den Vereinsmitgliedern von blockLAB möglich, der in einer Art Beta-Phase zur Einholung von Nutzer-Feedback und weiteren Erkenntnissen aus einem realen Vereinsablauf genutzt werden soll.

Die Ablage der Dokumente, über die abgestimmt wird, erfolgt über eine Nextcloud-Instanz, die bereits auch für anderweitigen Dokumentenaustausch innerhalb von blockLAB genutzt wird. Zusätzlich notwendige Kommunikation im Rahmen der Nutzung sowie das Sammeln von Feedback wird über mehrere Channels innerhalb des ebenfalls bereits existierenden Slack Teams von blockLAB realisiert.

## Bewertung
Auch wenn in diesem ersten Prototyp nur wenige Funktionen realisiert wurden und der Nutzungskomfort an einigen Stellen sicherlich noch ausgebaut werden kann, ergeben sich eine Reihe von Vorteilen aus der Verwendung von Blockchain-basierten Smart Contracts für die Abbildung von Abläufen in einem Verein:

* Relevante Vorgänge wie die Aufnahme von Mitgliedern, aktueller Mitgliederbestand und Abstimmungen sind transparent und nachvollziehbar. Dies ist ein vertrauensförderndes Element sowohl im Innenverhältnis zwischen den Vereinsmitgliedern, als auch für die interessierte Öffentlichkeit im Sinne der Transparenz und Öffentlichkeitsdarstellung des Vereins.
* Durch die Verbriefung von Basis-demokratischen Entscheidungen ist perspektivisch die Schaffung eines behördlich anerkannten neuen Typs von Vereinen als Zwischenstufe zwischen dem nicht eingetragenen und eingetragenen Verein ("Blockchain-eingetragener Verein") denkbar, analog zu den Ansätzen einer DAO ["Decentralized Autonomous Organization"](https://de.wikipedia.org/wiki/Dezentralisierte_Autonome_Organisation). Noch einen Schritt weiter gedacht könnte eine solche Lösung unter Einbeziehung von notariellen Attestaten und Identity/KYC-Nachweisen langfristig auch ein Vereinsregister ersetzen.
* Die Lösung lässt sich mit geringem Einsatz von Mitteln kostengünstig umsetzen - benötigt werden lediglich etwas Web Space für das Hosting der Webanwendung und Kommunikationskanäle zwischen den Vereinsmitgliedern wie Email-Listen oder Team-Chats, die in den meisten Vereinen bereits existieren dürften.

Allerdings können einige Aspekte auch als nachteilig angesehen werden und müssen kritisch betrachtet werden:
* Im Gegenzug für die günstige Umsetzung für den Verein fallen für die Nutzer der Lösung regelmäßig Transaktionskosten für die Erstellung von oder Teilnahme an Abstimmungen an.
* Die allgemeine Benutzerfreundlichkeit und Fehlertoleranz von Decentralized Apps ist schlechter als die von zentralisierten Lösungen, der Nutzer muss sich um die Installation und Konfiguration eines Browsers mit Wallet kümmern und selbst passende Sicherungsmaßnahmen für den privaten Schlüssel des Wallets treffen.
* Ebenso ist die Lösung von den bekannten technischen Herausforderungen von Ethereum betroffen: Die Versionierung bzw. Änderungen in Smart Contracts ist mit einigem Zusatzaufwand und Risiko verbunden, bei einem flächendeckenden Einsatz der Lösung für viele Vereine würden auch die Skalierungsgrenzen (Throughput, Dauer von Transaktionen) des Ethereum Mainnet eine Rolle spielen.
* Aufgrund der Datenschutzgrundverordnung dürfen personenbezogene Daten nicht in der Blockchain gespeichert werden. Dies führte dazu, dass in der Lösung nur ein selbst gewählter "Alias" für die Nutzeridentifikation umgesetzt wurde, d.h. weitere benötigte Daten wie Klarname, Anschrift, Email-Adresse etc. der Vereinsmitglieder müssen separat außerhalb der Blockchain vorgehalten werden.

## Ausblick
Der erste Prototyp hat gezeigt, dass die grundlegenden Funktionen der Mitgliederverwaltung und Abstimmungen in einem Verein auf Basis einer Ethereum Decentralized App technisch machbar und handhabbar sind. Das Feedback aus der regelmäßigen Nutzung im Testbetrieb innerhalb von blockLAB wird verwendet, um die Lösung weiter zu verbessern und zu verfeinern. Nach dem Testbetrieb und der Umsetzung von Verbesserungen ist im ersten Halbjahr 2019 der Übergang in den Livebetrieb mit Nutzung des Ethereum Mainnet geplant.

Neben technischen Verbesserungen der vorhandenen Lösungen bilden die bisher umgesetzten Funktionen die Basis für eine Reihe weitere möglicher Features, die wir in blockLAB evaluieren und ggf. in eine zukünftige Weiterentwicklung der Lösung einfließen lassen:
* Umsetzung weiterer typischer Punkte aus Vereinssatzungen mit Smart Contract Logik, z.B. Integration von KYC-Maßnahmen (blockLAB Mitglieder müssen volljährig sein), Kassenprüfung, Einberufung einer Mitgliederversammlung, Widerspruch gegen eine abgelehnte Aufnahme, Ausschlussverfahren, Auflösung des Vereins etc.
* Zahlungsfunktionen: durch die Möglichkeit, mit Transaktionen in Ethereum auch monetäre Werte zu transferieren, könnten Vorgänge wie Mitgliedsbeiträge, Spenden oder Sponsoring ebenfalls über Smart Contracts und die Decentralized App abgebildet werden.
* Ausgabe, Handel und Transfermöglichkeiten eines eigenen Vereinsbezogenen Tokens, der für Fundraising und/oder interne Bonus- oder Incentivierungssysteme verwendet werden kann.
* Whitelabeling: Für die Nutzung der Lösung durch andere Vereine müsste das Layout inkl. Logo und Farbschema ("Look&Feel"), Textelemente und sonstige blockLAB-spezifische Eigenschaften konfigurierbar gemacht werden.
* Zur Verbesserung der Benutzerfreundlichkeit und ganzheitlichen Abbildung der Vereinsvorgänge könnte die Lösung in eine existierenden Vereins-Software integriert werden, die bereits Funktionen wie DSGVO-konforme Verwaltung der Mitgliederdaten inkl. KYC-Prozesse, Dateiablage, Vereins-interne Kommunikationsmöglichkeiten etc. beinhaltet.

Bei Feedback jeglicher Art oder Interesse an einer Live-Demo oder Nutzung der Lösung im Rahmen eines Betatests melden Sie sich bitte per E-Mail unter [info@blocklab.de](mailto:info@blocklab.de).