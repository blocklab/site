---
layout: post
author: "Axel Hodler"
title: "Wie kann man den Besitz von Cryptowährung nachweisen?"
image: hieroglyphics.jpg
---

Im privaten Kreis haben wir vor Kurzem diskutiert wie eine Person am geschicktesten nachweisen kann ob sie Bitcoin besitzt. Auf diese Weise könnte Zahlungsfähigkeit gewährleistet werden.

Oft hört man als Antwort auf diese Frage, dass der Nutzer einen niedrigen Betrag Bitcoin an eine, durch einen Validator ausgesuchte, Adresse senden soll. Hierdurch könnte die Kontrolle von Bitcoins auf der Absenderadresse nachgewiesen werden. Der Vorgang funktioniert. Trotzdem wird hierbei ein fehlendes tieferes Verständnis der Funktionsweise von Bitcoin sichtbar. Zusätzlich entsteht eine unnötige Transaktion und die damit verbundenen Transaktionskosten fallen an.

Bei jedem Vorgang in dem Bitcoins den Besitzer wechseln muss eine signierte Transaktion erstellt werden. Diese ist für das Netzwerk der notwendige Nachweis, dass der Bezahlende im Besitz der Bitcoin auf der Absenderadresse ist. Jede Adresse wird von einem [öffentlichen Schlüssel](https://de.wikipedia.org/wiki/Public-Key-Verschl%C3%BCsselungsverfahren) abgeleitet. Kontrolle der Adresse kann durch eine Signatur mit dem dazugehörigen geheimen Schlüssel gezeigt werden. Beim Einsatz eines Wallets wird dieser Vorgang weitestgehend vom Benutzer abstrahiert um die Komplexität möglichst gering zu halten.

Neben dem Signieren von Transaktionen können wir auch jegliche anderen Nachrichten mit unserem geheimen Schlüssel signieren. Genau dazu wird [Public-Key-Kryptographie](https://de.wikipedia.org/wiki/Asymmetrisches_Kryptosystem), bereits lange vor der Zeit von Bitcoin, eingesetzt um die [Authentizität](https://de.wikipedia.org/wiki/Authentizit%C3%A4t) einer Nachricht zu beweisen.

Das Signieren von jeglichen Nachrichten ist nicht nur mit Bitcoin möglich. Das folgende Beispiel verwendet Ethereum.

Mit [web3.js](https://github.com/ethereum/web3.js/) können wir die Nachricht einfach programmatisch signieren.

{% highlight javascript %}
web3.eth.sign(web3.utils.sha3("Thanks for reading the article. Cheers Axel"),
  "0x38588822Bea476d5e1D56cFC9CE9781Fe5262196").then(console.log)
> 0x027d1dd45ab0eeee5803079086679a70d444a2d4ea7e8db221894977eabf8bfc
7486d6a9413e4a9aeddccf851ba7c2ea81835576b0afabbcfd62493ff0924ff400
{% endhighlight %}

Die Signatur

{% highlight bash %}
0x027d1dd45ab0eeee5803079086679a70d444a2d4ea7e8db221894977eabf8bfc7486d6a9413e4a9aeddccf851ba7c2ea81835576b0afabbcfd62493ff0924ff400
{% endhighlight %}

die Adresse `0x38588822Bea476d5e1D56cFC9CE9781Fe5262196` und die Nachricht `Thanks for reading the article. Cheers Axel` können nun zur Verifikation herangezogen werden.

Um den Ursprung der Nachricht zu verifizieren kann beispielsweise ein Formular auf [etherscan.io](etherscan.io) verwendet werden.

Einfach auf [https://etherscan.io/verifySig](https://etherscan.io/verifySig) die Adresse, Signatur und Nachricht eingeben, dann den `Verify` Button klicken.

> Message Signature (with Geth Prefix) Verified. Pass!

Wenn man nun _Axel_ durch _Bob_ in der Nachricht ersetzt und verifiziert sieht man

> Sorry! The Signature Message Verification Failed

Folglich schützt die Signatur auch die Integrität, d.h. die Unversehrtheit, der Nachricht.

Bitcoin Wallets wie [Electrum](https://electrum.org/) bieten die selbe Funktionalität in ihrer Benutzeroberfläche.

Beim Erstellen der Signatur oben beweisen wir, dass wir den geheimen Schlüssel kennen und somit über die Coins auf der Adresse verfügen. An Stelle des Beispiels oben können wir selbstverständlich auch andere Aussagen signieren. So auch

> Ich, Alice McBob, verfüge über die Coins auf dieser Adresse.

Eine Herausforderung entsteht, wenn mehrere Unternehmen oder Personen sich den geheimen Schlüssel teilen und somit die gleiche Adresse nutzen um ihre Zahlungsfähigkeit zu gewährleisten. Mit der Annahme, dass diese Beweise der Zahlungsfähigkeit nicht öffentlich gemacht werden, könnte ein Opfer somit im Glauben gelassen werden eine Person sei zahlungsfähig obwohl in Wirklichkeit jeder in der Gruppe der geheimen Schlüssel Halter die Coins jederzeit ausgeben kann.

Wir benötigen eine Möglichkeit nachzuweisen, dass nur _eine_ Person Zugriff auf den geheimen Schlüssel hat.

__Dies ist nicht möglich!__

Auch wenn wir alle Coins auf eine neue Adresse senden, und folglich der Zugriff durch einen anderen geheimen Schlüssel geschieht, gibt es keinen Weg zu beweisen, dass dieser Schlüssel nicht mit jemandem geteilt wurde. Selbst wenn der Schlüssel nicht geteilt wurde besteht stets die Möglichkeit, dass der Schlüssel direkt nach seiner Generierung durch eine unautorisierte Person gestohlen wird. Sei es weil diese Person Zugriff auf das Gerät, auf dem der Schlüssel gespeichert wird, hat.

Natürlich existiert ein ähnliches Problem auch außerhalb der Blockchain. Angenommen ein Online-Banking Kunde verwendet Two-factor Authentifizierung um eine Überweisung zu erstellen. Er benötigt den Login mit Passwort für die Website oder App sowie zum Beispiel sein Handy. Auf sein Handy bekommt er eine [TAN](https://de.wikipedia.org/wiki/Transaktionsnummer) per SMS gesendet, mit der die Transaktion bestätigt werden kann. Sollte bei diesem Kunden ein Angreifer sowohl Zugriff auf das Handy als auch auf den Online-Banking Login erlangen, so kann dieser unerlaubt im Namen des Kunden Überweisungen tätigen. Trotz diesem Zustand ist der Bank durch [kenne deinen Kunden](https://de.wikipedia.org/wiki/Know_your_customer) bekannt wer der eigentlich Eigentümer des Kontos ist. Die Kontrolle über das Geld kann somit zurückerlangt werden.

Um die Herausforderungen oben zu lösen müssen wir mit einem vertrauenswürdigen Intermediär arbeiten. Genau dieser sollte eigentlich durch eine öffentlich Blockchain obsolet werden. Die grundlegende Idee einer Blockchain ist der "Konsens ohne Vertrauen". Wenn wir unserem Gegenüber vertrauen benötigen wir keine Blockchain.

Nichtsdestotrotz kann der Intermediär, vielleicht eine Bank oder ein Notar, die notwendige Attestierung vornehmen und so die folgende Aussage bereitstellen.

> Die Person Alice McBob gab uns 1000 Euro, welche wir in Bitcoin getauscht und an die Adresse 1A2a1NNjy4RCbabvnZWsAxJWrBRkyypq4H gesendet haben. Wir zertifizieren, dass Alice McBob Zugriff auf die Bitcoin dieser Adresse hat und diese Bitcoin zum Zeitpunkt der Attestierung auch auf dieser Adresse lagen.

Diese Aussage kann auch in einer [Bitcoin Transaktion](https://en.bitcoin.it/wiki/Proof_of_Ownership) enkodiert und somit verewigt werden. Leider führt dies zur Herausforderung wer dem Attestierer nun attestieren kann, dass es sich wirklich um einen rechtmäßigen Attestierer handelt. Demnach wird das Attest vermutlich auf einer Website, auf die hoffentlich nur der Attestierer Zugriff hat, bereitgestellt.

Ob diese Attestierung sinnvoll und aussagekräftig ist muss von den Benutzern dieser Services selbst bewertet werden.

Ich hoffe der vorliegende Artikel hat die Thematik wie jemand beweist wer die Coins auf einer Adresse kontrolliert ausführlich beleuchtet.

_The article has also been released in english on [Medium](https://medium.com/yopiter/proving-ownership-of-a-cryptocurrency-86a96f2c52b)_
