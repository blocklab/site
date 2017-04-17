---
layout: post
author: "Axel Hodler"
title: "Verwendung von Ethereum Smart Contracts in Java"
image: pipes.jpg
---

Etliche Geschäftsanwendungen sind in Java geschrieben bzw. laufen auf der [JVM](https://de.wikipedia.org/wiki/Java_virtual_machine). Folglich sind wir der Meinung, dass die Grundlagen wie ein [Ethereum](https://www.ethereum.org/) [Smart Contract](https://en.wikipedia.org/wiki/Smart_contract) in eine bestehende Anwendung eingebunden wird, sich für den Leser als hilfreich erweist.

Der vorliegende Artikel setzt das Wissen was ein Smart Contract ist, und wie er verteilt wird, voraus.

## Grundlagen

Um zu verstehen, warum wir eine library verwenden, um mit dem Smart Contract zu interagieren, ist es hilfreich ein Verständnis zu haben wie [JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC) in Ethereum eingesetzt wird.

Hinter der Funktionalität, die eine Library vermeintlich verbirgt, steckt wenig Magie. Angenommen wir möchten den Wert von `gaslimit`, dem aktuellen Maximum an rechnerischem Aufwand, welcher in einem Block geleistet werden kann, herausfinden. Mit [curl](https://curl.haxx.se/) und einem JSON parser wie [jq](https://stedolan.github.io/jq/) können wir, unter der Annahme, dass ein Ethereum Knoten auf localhost port 8545 aktiv ist, den folgenden Befehl absetzen

{% highlight bash %}
curl --silent -X POST -H "Content-Type: application/json"\
  --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest", true],"id":1}'\
 localhost:8545 | jq .result.gasLimit
{% endhighlight %}

Das Ergebnis war zum Aufrufzeitpunkt `"0x4c4b3c"`. Es ist [hexadezimal](https://de.wikipedia.org/wiki/Hexadezimalsystem). Um den Wert ohne Nachdenken lesen zu können, verwenden wir eine weitere [Pipe](https://de.wikipedia.org/wiki/Senkrechter_Strich) und der Befehl wird zu

{% highlight bash %}
curl --silent -X POST -H "Content-Type: application/json"\
  --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest", true],"id":1}'\
  localhost:8545 | jq .result.gasLimit | xargs printf '%d\n'
{% endhighlight %}

welcher uns das Ergebnis `4999996` liefert.

All diese Schritte sollen als Beispiel fungieren, warum die Einführung einer Abstraktion Sinn macht. Nahezu alle Libraries bieten bequeme Interfaces für diese RPC calls.

Mit der Library [web3j](https://web3j.io) wird der lange Aufruf oben zu

{% highlight java %}
web3j.ethGetBlockByNumber(DefaultBlockParameter.valueOf("latest"),
  true).send().getBlock().getGasLimit();
{% endhighlight %}

Zwar weiterhin einige Schritte, und von der Software-Technik Perspektive gesehen eine Verletzung des [Law of Demeter](https://de.wikipedia.org/wiki/Gesetz_von_Demeter), aber doch weniger fehleranfällig und weitaus komfortabler. Dazu kommt, dass wir beim Einsatz dieser Funktionalität mit hoher Wahrscheinlichkeit eine weitere Abstraktion wie `ethereumGateway.currentGasLimit()` schaffen.

## web3j

Um web3j zu testen, verwenden wir einen einfachen Smart Contract zur Aktenhaltung. Der Smart Contract bietet eine Übersicht von `Deliverables` bzw. Lieferungen. Hierbei könnte es sich um einen Schiffscontainer handeln. Es werden Zugriffskontroller eingesetzt um nur dem `owner` (Besitzer) des Smart Contracts zu erlauben dessen Zustand zu ändern. Dies erreichen wir mit dem `onlyByOwner` _function modifier_. Der Besitzer kann eine neue Lieferung speichern (`store`) und deren Zustand auf `delivered` setzen. Im Beispiel mit dem Schiffscontainer bedeutet dies, dass der Container sein Ziel erreicht hat. Die Abfrage ob der Container sein Ziel erreicht hat, ist durch jeden Netzwerkteilnehmer mit der Funktion `statusFor` möglich. Der Wert von _1_ als Status bezeugt die Ankunft der Lieferung.

{% highlight javascript %}
contract Deliverables {
  address public owner;
  mapping (address => Deliverable) deliverables;

  struct Deliverable {
    address id;
    uint status;
  }

  modifier onlyByOwner() {
    if (msg.sender != owner) throw;
    _;
  }

  function Deliverables() {
    owner = msg.sender;
  }

  function store(address id) onlyByOwner {
    deliverables[id] = Deliverable(id, 0);
  }

  function statusFor(address id) constant returns(uint status){
    return deliverables[id].status;
  }

  function delivered(address id) onlyByOwner {
    deliverables[id].status = 1;
  }
}
{% endhighlight %}

Um mit dem [web3j Kommandozeilen-Programm](https://docs.web3j.io/command_line.html) zu arbeiten, benötigen wir die hex-enkodierte Binärdatei des Contracts, die zugehörige Application Binary Interface (ABI) Datei, einen Zielordner sowie den Packagename.

{% highlight bash %}
web3j solidity generate compiled_contract/Deliverables.sol:Deliverables.bin\
  compiled_contract/Deliverables.sol:Deliverables.abi\
  -o src/main/java -p com.yopiter
{% endhighlight %}

Dies erzeugt eine `.java` Datei mit den folgenden Inhalten.

Manche Stellen wurden entfernt um das Beispiel kurz zu halten.

{% highlight java %}
public final class Deliverables extends Contract {
  // [...]
  public Future<TransactionReceipt> store(Address id) {
    Function function = new Function("store",
      Arrays.<Type>asList(id), Collections.<TypeReference<?>>emptyList());
    return executeTransactionAsync(function);
  }

  public Future<TransactionReceipt> delivered(Address id) {
    Function function = new Function("delivered",
      Arrays.<Type>asList(id), Collections.<TypeReference<?>>emptyList());
    return executeTransactionAsync(function);
  }

  public Future<Uint256> statusFor(Address id) {
    Function function = new Function("statusFor",
                Arrays.<Type>asList(id),
                Arrays.<TypeReference<?>>asList(
      new TypeReference<Uint256>() {}));
    return executeCallSingleValueReturnAsync(function);
  }
  // [...]
}
{% endhighlight %}

Wit erkennen zwei Methoden, um den Zustand des Contracts zu ändern, `store` (speichern) und `delivered` (als geliefert markieren). Beide liefern uns ein `TransactionReceipt`. Weiterhin existiert eine Methode, um den Contract nach dem Status einer Lieferung zu fragen. Diese Methode `statusFor` liefert den Wert zurück, ohne eine Transaktion zu erstellen. Gleichzeitig wird der korrekte Typ `Uint256` verwendet. Diese Typsicherheit wird als Vorteil gegenüber den Libraries in bspw. der JavaScript Welt gewertet.

Die beispielhafte Interaktion mit dem Contract kann als Integrationstest dargestellt werden.

{% highlight java %}
@Test
public void deliverables_can_be_delivered() throws Exception {
  Deliverables deliverables = Deliverables.load(address, web3j,
    credentials, gasPrice, gasLimit);
  String hash = web3j.web3Sha3("CONTENTS").send().getResult();
  Address deliverable = new Address(hash);
  deliverables.store(deliverable).get();

  deliverables.delivered(deliverable).get();

  BigInteger result = deliverables.statusFor(deliverable).get()
    .getValue();
  assertThat(result, is(BigInteger.valueOf(1)));
}
{% endhighlight %}

Wir zergliedern den Programmcode in die relevanten Schritte.

{% highlight java %}
Deliverables.load(address, web3j, credentials, gasPrice, gasLimit);
{% endhighlight %}

Der Contract wird geladen, indem wir dessen `address` eine Instanz von `web3j`, die `credentials` (Ein entschlüsseltes Wallet), den `gasPrice` und das `gasLimit` angeben.

Es ist festzustellen, dass `credentials`, `gasPrice` und `gasLimit` nicht notwendig wären, wenn wir nur daran interessiert wären Werte im Contract zu lesen. Beispielsweise durch die Verwendung von `statusFor`. Leseoperationen auf einem Contract sind kostenfrei. Sie benötigen keine Transaktion und somit auch nicht die damit verbundenen Transaktionskosten. Die generierte Methode benötigt diese Argumente trotzdem.

{% highlight java %}
web3j.web3Sha3("CONTENTS").send().getResult();
{% endhighlight %}

Hier erstellen wir den Hashwert ([SHA-3](https://de.wikipedia.org/wiki/SHA-3)) von etwas Einzigartigem. Es könnte der Lieferbeleg verwendet werden.

Es sollte bedacht werden, dass wir vermutlich keine kompletten Dokumente auf der Blockchain speichern wollen, da wir für jedes Byte, das in einer Transaktion gesendet wird, bezahlen müssen. Die Verwendung eines Hashes ist somit eine Möglichkeit die Transaktion schmal und günstig zu halten, die Lieferung aber trotzdem eindeutig Identifizieren zu können.

{% highlight java %}
deliverables.store(deliverable).get();
{% endhighlight %}

Als nächstes wird die Transaktion, um die Lieferung (`Deliverable`) auf der Blockchain zu speichern, erstellt und ans Netzwerk gesendet und erfasst.

{% highlight java %}
deliverables.delivered(deliverable).get();
{% endhighlight %}

Fast fertig. Wir ändern den Status auf ausgeliefert und warten kurz bis die Ausführung abgeschlossen ist.

{% highlight java %}
BigInteger result = deliverables.statusFor(deliverable).get()
  .getValue();
assertThat(result, is(BigInteger.valueOf(1)));
{% endhighlight %}

Zum Schluss verifizieren wir, ob die Lieferung ihr Ziel erreicht hat.

## Fazit

Im Vergleich zu [web3.js](https://github.com/ethereum/web3.js), der _originalen_ Library in JavaScript, fühlt es sich teilweise umständlicher an mit ihrer Schwesterlibrary in Java zu arbeiten.

Beispielsweise würden wir um in web3.js unseren Account zu verwenden, das folgende schreiben

{% highlight javascript %}
web3.personal.unlockAccount("someAddress", "somePassword")
{% endhighlight %}

In web3j benötigt dies das Einlesen der Wallet-Datei und das umherreichen der `Credentials` Instanz.

{% highlight java %}
Credentials c = WalletUtils.loadCredentials("somePassword",
  "wallets/UTC-2017-03-31T09-19-59.503000000Z--6dc71522dfff6e05c450abe2a68e11392e31551d.json");
{% endhighlight %}

Am Ende handelt es sich um persönliche Präferenzen. Sicherlich ist die web3.js Library ausgereifter und öfter im Einsatz als web3j. Dies verdeutlicht, dass auch web3j sich in Zukunft weiterentwickeln und verbessern wird.

Zusätzlich existiert mit [EthereumJ](https://github.com/ethereum/ethereumj) eine Java Implementierung des Ethereum Protokolls. Dies bedeutet, es enthält Mining Funktionalität und eine Implementierung der Blockchain. Web3j, welches sich selbst als _lightweight_ bezeichnet, beinhaltet diese Funktionalität nicht, da diese sicherlich nicht für jeden Nutzer bei der Interaktion mit einem Smart Contract von Relevanz ist.

Abschließend sollte erwähnt werden, dass wir im Hinblick auf die Zukunft unserer Anwendung eine Abstraktion für jegliche Library, die wir verwenden, erstellen sollten. Dies erlaubt uns die bereitgestellte Funktionalität, ohne die Elemente, welche die Geschäftslogik enthalten zu verändern, auszutauschen, sollten wir eine für unsere Anwendung besser geeignete Umsetzung finden. Es erlaubt uns sogar das Einbinden von unterschiedlichen Implementierungen, bspw von diversen Konkurrenzprodukten wie [Rootstock](http://www.rsk.co/), oder gar einer langweiligen Datenbank.

The article has also been released in english on [Medium](https://medium.com/yopiter/interfacing-with-ethereum-smart-contracts-in-java-cf39b2e95b4e)
