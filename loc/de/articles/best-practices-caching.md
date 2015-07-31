<properties
   pageTitle="Caching guidance | Microsoft Azure"
   description="Guidance on caching to improve performance and scalability."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Zwischenspeicherung Anleitung

![](media/best-practices-caching/pnp-logo.png)

Zwischenspeicherung ist ein gebräuchliches Verfahren, das darauf abzielt, die Leistung zu verbessern und
Skalierbarkeit eines Systems durch vorübergehend kopieren häufig auf Daten zugegriffen.
zu schnelle Speicherung in der Nähe der Anwendungdes. Wenn diese schnelle Datenhaltung
liegt näher an die Anwendung als die ursprüngliche Quelle dann Zwischenspeichern
Antwortzeiten für Client-Anwendungen kann durch das dienen wesentlich verbessert werden.
Daten schneller. Zwischenspeicherung ist am effektivsten, wenn ein Client Instanz wiederholt
die gleichen Daten liest, vor allem, wenn die Daten relativ statisch bleibt und
der ursprünglichen Datenspeicher ist relativ zur Geschwindigkeit des Caches, langsam ist
ein hohes Maß an Konflikte unterliegen, oder ist weit weg als Netzwerk-Latenz
Zugang zu langsam sein kann.

## Zwischenspeicherung in verteilten Anwendungen

Verteilte Anwendungen in der Regel implementieren, eine oder beide der
folgende Strategien beim Zwischenspeichern von Daten:

- Verwenden einen privaten Cache, wo die Daten lokal auf dem Computer, auf dem eine Instanz von einer Anwendung oder einem Dienst gehalten wird.
- Verwenden einen gemeinsamen Cache, als eine gemeinsame Quelle, die von mehreren Prozessen und/oder Maschinen zugegriffen werden kann.

In beiden Fällen könnte Zwischenspeicherung durchgeführt clientseitige (indem Sie den Prozess bereitstellen
die Benutzeroberfläche für ein System wie ein Web-Browser oder Desktop-Anwendung)
und/oder serverseitige (durch den Prozess der Bereitstellung von Business-services
auf anderen Rechnern ausführen).

### Private Zwischenspeichern

Die grundlegendste Art der Cache ist ein im Speicher-Shop, gehalten in der Adresse
Raum von einem einzelnen Prozess direkt über ausgeführter Code zugegriffen
in diesem Prozess. Dieser Cache ist sehr schnell Zugriff auf, und es kann
bieten Sie eine extrem wirksame Strategie für die Speicherung von bescheidenen Mengen von
statische Daten wie die Größe eines Caches eingeschränkt ist in der Regel durch die
Volumen des Speichers auf dem Computer der Hostprozess verfügbar. Wenn Sie
benötigen Sie mehr Informationen, als physisch möglich ist im Speicher zwischengespeichert,
Sie können die zwischengespeicherte Daten im lokalen Dateisystem schreiben. Dies wird
unbedingt langsamer als Daten im Arbeitsspeicher gehalten, aber sollte Zugriff auf
werden Sie noch schneller und zuverlässiger als das Abrufen von Daten über ein Netzwerk.

Wenn Sie mehrere Instanzen einer Anwendung verwendet, die dieses Modell
gleichzeitig ausgeführt werden, wird jede Anwendungsinstanz eigene haben
unabhängigen Cache, eine eigene Kopie der Daten.

Sie sollten einen Cache als Momentaufnahme der Originaldaten auf einige denken.
zeigen Sie in der Vergangenheit. Wenn diese Daten nicht statisch ist, ist es wahrscheinlich, dass
anderen Anwendungsinstanzen hält verschiedene Versionen der
Daten in ihre Caches. Daher die gleiche Abfrage durchgeführt durch diese
Instanzen können unterschiedliche Ergebnisse zurück, wie in Abbildung 1 dargestellt.

![Verwenden einen in-Memory-Cache in verschiedenen Instanzen einer Anwendung](media/best-practices-caching/Figure1.png)

_Abbildung 1: Verwenden eines in-Memory-Caches in verschiedenen Instanzen einer Anwendung_

### Laufzeit-Zwischenspeicherung

Verwenden einen gemeinsamen Cache kann dazu beitragen, die Sorge, dass Daten können
unterscheiden sich die Caches mit Zwischenspeicherung im Arbeitsspeicher auftreten können. Gemeinsam
Zwischenspeicherung wird sichergestellt, dass verschiedene Anwendungsinstanzen gleich sehen
Ansicht des zwischengespeicherten Daten durch den Cache an einem separaten Speicherort suchen,
in der Regel im Rahmen einer separaten Service gehostet, wie in Abbildung 2 dargestellt.

![Einen gemeinsamen Cache verwenden_](media/best-practices-caching/Figure2.png)

_Abbildung 2: Verwenden eines freigegebenen Caches_

Ein wesentlicher Vorteil der Verwendung des gemeinsam genutzten Zwischenspeichern Ansatzes liegt die
Skalierbarkeit kann es helfen, um zu bieten. Viele gemeinsame Cache-Dienste sind
implementiert mithilfe von einen Cluster von Servern und Software zu nutzen, die
verteilt die Daten über den Cluster in einer transparenten Weise. Ein
Anwendungsinstanz sendet einfach eine Anfrage an den Cache-Dienst,
und die zugrunde liegende Infrastruktur ist verantwortlich für die Bestimmung der
Lage der zwischengespeicherten Daten im Cluster. Sie können problemlos skalieren die
Cache, indem Sie weitere Server hinzufügen.

Die Nachteile der Zwischenspeicherung von freigegebenen Ansatz sind, die den Cache
ist langsamer auf zugreifen, weil es nicht mehr lokal für jeden gehalten wird
Anwendungsinstanz und das Erfordernis eine separaten implementieren
Cache-Dienst kann Komplexität der Projektmappe hinzufügen.

## Überlegungen zur Verwendung von Zwischenspeichern

Die folgenden Abschnitte beschreiben im Detail die Überlegungen
für das Entwerfen und verwenden einen Cache.

### Wenn werden Daten zwischengespeichert soll?

Zwischenspeicherung kann die Performance, Skalierbarkeit und Verfügbarkeit drastisch verbessern. Je mehr Daten
die Sie haben und je größer die Anzahl der Benutzer, die Zugriff auf diese Daten, desto größer müssen
die Vorteile des Zwischenspeicherns durch Verringerung der Latenz und Streit Handhabung zugeordnet werden
große Mengen von gleichzeitigen Anforderungen in den ursprünglichen Datenspeicher. Z. B. eine Datenbank
kann eine begrenzte Anzahl von gleichzeitigen Verbindungen, aber Abrufen von Daten von einem gemeinsamen unterstützen
Cache, anstatt der zugrunde liegenden Datenbank ermöglicht eine Clientanwendung auf diese Daten zugreifen
auch wenn die Anzahl der verfügbaren Verbindungen derzeit erschöpft ist. Zusätzlich, wenn die
Datenbank nicht mehr verfügbar, Clientanwendungen möglicherweise weiterhin mithilfe der
Daten, die im Cache gehalten werden.

Sie sollten das Zwischenspeichern von Daten, die gelesen wird häufig aber das ist selten geändert
(die Daten hat einen hohen Anteil an Lesevorgänge verglichen, um Vorgänge zu schreiben). Jedoch
Verwenden Sie nicht den Cache als autorisierende Speicher kritische Informationen; sollten Sie
Stellen Sie sicher, dass alle Änderungen, die Ihre Anwendung nicht leisten kann zu verlieren immer gespeichert sind ein
permanente Datenspeicher. Auf diese Weise wenn der Cache nicht verfügbar ist, kann Ihre Anwendung
weiterhin mit dem Datenspeicher zu betreiben und Sie verlieren nicht wichtig
Informationen.

### Arten von Strategien für die Bevölkerung von Daten und cache

Der Schlüssel für einen Cache effektiv nutzen liegt bei der Bestimmung der am besten geeigneten Daten zu
Cache und Cache-es zu gegebener Zeit. Die Daten können der Cache auf hinzugefügt werden
Nachfrage zum ersten Mal, dass sie von einer Anwendung abgerufen werden, so dass die Anwendung muss
Holen Sie die Daten nur einmal speichern und nachfolgende Zugriffe aus den Daten erfüllt werden können
mithilfe des Caches.

Alternativ kann ein Cache teilweise oder vollständig mit Daten im Voraus gefüllt werden,
Wann startet die Anwendung in der Regel (ein Konzept, bekannt als Aussaat). Es kann jedoch
nicht ratsam sein, implementieren seeding für einen großen Cache, wie dieser Ansatz durchsetzen kann
eine plötzliche, hohe Last auf den ursprünglichen Datenspeicher beim Start der Anwendung ausgeführt wird.

Oft hilft eine Analyse der Verwendungsmuster um zu entscheiden, ob Sie ganz oder teilweise
Auffüllen Sie, ein Cache, und die Daten zu wählen, die zwischengespeichert werden soll. Zum Beispiel, es
wahrscheinlich wäre den Cache mit der statischen Benutzerprofildaten für Seeding sinnvoll
Kunden, die Anwendung regelmäßig (vielleicht jeden Tag), nicht aber für
Kunden, die die Anwendung nur einmal pro Woche verwenden.

In der Regel Zwischenspeichern funktioniert gut mit Daten, ist unveränderlich oder Änderungen
selten. Beispiele sind Referenzinformationen wie Produkt- und Preispolitik
Informationen in einer e-Commerce-Anwendung oder freigegebenen statische Ressourcen, die kostspielig sind
zu konstruieren. Einige oder alle diese Daten können in den Cache bei Anwendung geladen werden
Start zur Beanspruchung der Ressourcen zu minimieren und eine bessere Performance. Es kann auch sein
angemessen, einen Prozess im Hintergrund aktualisiert, die in regelmäßigen Abständen Referenzdaten
im Cache um sicherzustellen, es ist auf dem neuesten Stand, oder wird der Cache aktualisiert wenn verweisen
Datenänderungen.

Zwischenspeicherung kann weniger nützlich für dynamische Daten sein, obwohl es gibt einige Ausnahmen zu
Diese Überlegung (siehe Abschnitt Caching hoch dynamischen Daten später in diesem
Anleitungen für weitere Informationen). Wenn die ursprünglichen Daten regelmäßig, entweder ändert
die zwischengespeicherte Informationen kann sehr schnell veraltet werden oder den Aufwand zu halten
der Cache mit den ursprünglichen Datenspeicher synchronisiert reduziert die Wirksamkeit der
Zwischenspeichern. Beachten Sie, dass ein Cache nicht enthalten die vollständigen Daten für eine
Entität. Beispielsweise, wenn ein Datenelement eine mehrwertige Objekt z. B. eine Bank darstellt
Kunden mit einem Namen, der Adresse und der Kontostand, können einige dieser Elemente
bleiben statisch (Name und Anschrift), während andere (z. B. Kontostand)
möglicherweise mehr Dynamik. In diesen Fällen könnte es die statische cachen sinnvoll sein.
Teile der Daten und nur abrufen (oder berechnen) die restliche Informationen als
und wenn es erforderlich ist.

Analyse von Performance-Tests und Nutzung sollten durchgeführt werden, um festzustellen, ob
Pre Bevölkerung oder on-Demand-Laden von Cache oder eine Kombination aus beidem, ist
entsprechenden. Die Entscheidung sollte basiert auf einer Kombination von der Volatilität und
Nutzungsverhalten der Daten. Cache-Auslastung und Performance-Analyse ist
besonders wichtig in Anwendungen, die hohe Belastungen auftreten und muss
hoch skalierbar. Beispielsweise kann in hochgradig skalierbare Szenarien sinnvoll
Saatgut, den Cache, um die Belastung für den Datenspeicher zu Spitzenzeiten zu reduzieren.

Zwischenspeicherung kann auch verwendet werden, Wiederholung von Berechnungen, wie die Anwendung ist zu vermeiden
ausgeführt. Wenn eine Operation Daten transformiert oder eine komplizierte Berechnung führt
Sie können die Ergebnisse der Operation im Cache speichern. Wenn die gleiche Berechnung
erforderlich ist danach die Anwendung einfach die Ergebnisse abrufen können
der Cache.

Eine Anwendung in einem Cache gespeicherten Daten ändern kann, jedoch sollten Sie die
Cache als vorübergehende Datenspeicher, der jederzeit verschwinden könnte. Nicht speichern
wertvolle Daten nur im Cache, stellen sondern sicher, dass Sie die Informationen pflegen
in den ursprünglichen Datenspeicher sowie. Auf diese Weise, wenn der Cache werden sollte
nicht verfügbar, minimieren Sie die Wahrscheinlichkeit eines Datenverlustes.

### Hochdynamische Daten zwischenspeichern

Speichern von Informationen, die Änderungen schnell in einem permanenten Datenspeicher auferlegen können
ein Overhead auf dem System. Betrachten Sie beispielsweise ein Gerät, das kontinuierlich berichtet
Status oder einige andere Messung. Entscheidet sich eine Anwendung nicht für das Zwischenspeichern
Daten auf der Grundlage, dass die zwischengespeicherte Informationen fast immer veraltet, dann werden
die gleiche Überlegung könnte wahr beim Speichern und Abrufen von Informationen sein.
aus dem Datenspeicher; in der Zeit zu speichern und diese Daten abrufen müssen es
geändert. Sollten Sie in einer Situation wie dieser die Vorteile der dynamischen speichern
Informationen direkt im Cache anstelle der persistenten Daten speichern. Wenn die
Daten ist unkritisch und erfordert keine überwacht werden sollen, dann spielt es keine Rolle
Wenn die gelegentliche Änderung verloren geht.

### Verwalten von Daten Ablauf in einem cache

In den meisten Fällen ist in einem Cache gespeicherten Daten eine Kopie der Daten in den Originaldaten
speichern. Die Daten in den ursprünglichen Datenspeicher möglicherweise ändern, nachdem es verursacht zwischengespeichert wurde,
die zwischengespeicherten Daten veralten. Viele Systeme Zwischenspeichern können Sie konfigurieren die
Cache-Daten ungültig werden und reduzieren den Zeitraum für den Daten veraltet sein können.

Zwischengespeicherte Daten nach Ablauf wird es aus dem Cache entfernt, und die Anwendung muss
Abrufen der Daten aus dem ursprünglichen Datenspeicher (es kann neu geholt setzen
Informationen in den Cache). Sie können eine Standardrichtlinie Ablauf festlegen Wenn Sie
Konfigurieren Sie den Cache. In viele Cache-Dienste können Sie den Ablauf festlegen.
Zeitraum für einzelne Objekte, wenn Sie sie programmgesteuert im Cache speichern
(einige Caches aktivieren Sie das Ablaufdatum angeben, als absoluter Wert, oder
als gleitende Wert bewirkt, dass das Element aus dem Cache entfernt werden, wenn es nicht
innerhalb des angegebenen Zeitraums abgerufen. Diese Einstellung überschreibt alle Cache-weite
Ablaufrichtlinie, aber nur für die angegebenen Objekte.

> [AZURBLAU. HINWEIS] Prüfen Sie der Gültigkeitszeitraum für den Cache und die Objekte, die es sorgfältig enthält. Wenn Sie es zu kurz machen, Objekte läuft zu schnell, und Sie reduzieren die Vorteile der Verwendung des Caches. Wenn Sie die Zeit zu lang machen, riskieren Sie die Daten veraltet.

Es ist auch möglich, dass der Cache füllen könnte, wenn Daten zulässig ist, bleiben
wohnhaft seit langem. In diesem Fall irgendwelche Anforderungen zum Hinzufügen neuer Elemente zu den
Cache kann dazu führen, dass einige Elemente gewaltsam entfernt werden, in einem Prozeß bekannt als
Zwangsräumung. Cache-Dienstleistungen vertreiben in der Regel Daten über einen Least recently used (LRU)
Grundlage, aber Sie können in der Regel überschreiben diese Politik und verhindern, dass Elemente werden
vertrieben. Jedoch, wenn Sie diesen Ansatz riskieren Sie Ihren Cache mehr als die
Speicher zur Verfügung, und eine Anwendung hat versucht, die ein Element hinzufügen
im Cache werden mit einer Ausnahme fehlschlagen.

Einige Zwischenspeicherung Implementierungen können zusätzliche Auslagerungsrichtlinien vorsehen. Diese
in der Regel umfassen die zuletzt verwendeten Politik (in der Erwartung, die der
Daten werden nicht erforderlich wieder), First-in-First-Out-Politik (älteste Daten sind
entfernt zuerst), oder ausdrückliche Entfernung basierend auf eine ausgelöste Ereignis (wie z. B. die
Daten geändert wird).

### Daten in einem clientseitigen Cache ungültig zu machen

In einem clientseitigen Cache gespeicherten Daten gilt im Allgemeinen als außerhalb der
der Schirmherrschaft des Dienstes Bereitstellen der Daten an dem Client; ein service
nicht direkt zwingen einen Client hinzufügen oder Entfernen von Informationen aus einem
clientseitigen Cache. Dies bedeutet, dass es möglich, dass ein Client ist, verwendet
eine schlecht konfigurierte Cache (z. B. Ablauf sind Richtlinien nicht
ordnungsgemäß implementiert) weiterhin verwenden veraltete Informationen zwischengespeichert
Wenn die Informationen in der ursprünglichen Datenquelle lokal geändert hat.

Wenn Sie eine Webanwendung erstellen, die Daten über eine HTTP-dient
Anschluss, zwingen Sie implizit einen Webclient (z. B. einen Browser oder
Web-Proxy) um die neuesten Informationen zu holen, wenn eine Ressource aktualisiert wird
durch den URI der Ressource ändern. Web-Clients in der Regel verwenden Sie den URI
als Schlüssel im clientseitigen Cache, Veränderung des URIs einer Ressource
bewirkt, dass den Web-Client ignoriert alle zuvor zwischengespeicherte Version der eine
Ressource und stattdessen die neue Version zu holen.

## Verwalten von Parallelität in einem cache

Caches sind oft von mehreren Instanzen gemeinsam genutzt werden soll ein
Anwendung. Jede Anwendungsinstanz lesen und Ändern von Daten in
der Cache. Folglich die gleiche auftretenden Parallelitätsprobleme mit
alle freigegebenen Datenspeicher gelten auch für einen Cache. In einer situation
wo eine Anwendung muss im Cache gespeicherten Daten zu bearbeiten, können Sie
müssen Sie sicherstellen, dass Aktualisierungen von einer Instanz der Anwendung vorgenommen
Überschreiben Sie die von einer anderen Instanz vorgenommenen Änderungen nicht blind.

Abhängig von der Art der Daten und die Wahrscheinlichkeit von Kollisionen,
Sie können annehmen, dass einer der beiden Ansätze zur Parallelität:

- __Optimistisch.__ Die Anwendung überprüft, um ob die Daten im Cache geändert hat, da sie unmittelbar vor der Aktualisierung es abgerufen wurde, zu sehen. Wenn die Daten unverändert sind, kann die Änderung vorgenommen werden. Andernfalls muss die Anwendung entscheiden, ob es zu aktualisieren (die Geschäftslogik, die diese Entscheidung Laufwerke werden anwendungsspezifische). Dieser Ansatz eignet sich für Situationen, wo Updates unregelmäßig sind, oder wo Kollisionen unwahrscheinlich sind.
- __Pessimistisch.__ Die Anwendung sperrt die Daten im Cache, wenn sie es verhindern, dass eine weitere Instanz ändern die Daten abruft. Dieser Prozess stellt sicher, dass Kollisionen können nicht auftreten, aber andere Instanzen, die die gleichen Daten verarbeiten müssen blockieren könnte. Eingeschränkte Parallelität kann die Skalierbarkeit der Lösung beeinträchtigen und sollten nur für kurzlebige Operationen verwendet werden. Dieser Ansatz eignet sich für Situationen möglicherweise wo Kollisionen sind wahrscheinlicher, vor allem, wenn eine Anwendung mehrere Elemente im Cache aktualisiert und muss sicherstellen, dass diese Änderungen konsistent angewendet werden.

### Implementierung von Hochverfügbarkeit und Skalierbarkeit und Leistung zu verbessern

Ein Cache sollte nicht das primäre Repository Daten; Dies ist die Rolle
von den ursprünglichen Datenspeicher, der aus dem Cache gefüllt wird. Die
ursprünglichen Datenspeicher muss dafür sorgen das Fortbestehen der
Daten.

Achten Sie darauf, keine kritische Abhängigkeiten von der Verfügbarkeit einführen
eines gemeinsamen Cache-Dienstes in Ihre Lösungen. Eine Anwendung sollte
weiterhin funktionieren wenn der Service ist des gemeinsamen Caches
steht nicht zur Verfügung; die Anwendung sollte nicht hängen oder während der Wartezeit fehl
für den Cache-Dienst wieder aufzunehmen. Daher muss die Anwendung sein.
bereit, die Verfügbarkeit des Dienstes Cache zu erkennen und fallen zurück
auf die ursprünglichen Daten zu speichern, wenn der Cache nicht zugegriffen werden kann. Die
[Schutzschalter Muster](http://msdn.microsoft.com/library/dn589784.aspx) eignet sich für den Umgang mit diesem Szenario. Die
Service-Bereitstellung des Caches kann wiederhergestellt werden, und einmal wird es
verfügbaren Cache kann werden aufgefüllt wie Daten Form gelesen werden die
ursprünglichen Datenspeicher, nach einer Strategie, wie z. B. die [Cache-Aside-Muster](http://msdn.microsoft.com/library/dn589799.aspx).

Jedoch fallen wieder in den ursprünglichen Datenspeicher, wenn der Cache ist
vorübergehend nicht verfügbar ist kann eine Skalierbarkeit auf das System auswirken;
während der Datenspeicher ist genesen Wesen den ursprünglichen Datenspeicher
überschwemmt werden könnte, mit Anforderungen für Daten, was zu Timeouts und
fehlgeschlagene Verbindungen. Eine Strategie, die man beachten sollte, ist die
Implementieren Sie einen lokalen privaten Cache in jeder Instanz einer Anwendung
zusammen mit den freigegebenen Zwischenspeichern, die alle Anwendungsinstanzen
Zugang. Wenn die Anwendung ein Elements abruft, können ihn zuerst überprüfen
in seinem lokalen Cache, dann der gemeinsame Cache und schließlich das original
Datenspeicher. Lokale Cache gefüllt werden kann mithilfe der Daten in der
Shared-Cache oder der Datenbank, wenn der gemeinsame Cache nicht verfügbar ist.
Dieser Ansatz erfordert sorgfältige Konfiguration, um zu verhindern, dass die lokale
Cache zu veraltet in Bezug auf den gemeinsamen Cache, aber es
dient als Puffer, wenn der gemeinsame Cache nicht erreichbar ist. Abbildung 3
Diese Struktur zeigt.

![Verwenden einen lokalen privaten Cache mit einem gemeinsamen cache_](media/best-practices-caching/Caching3.png)
_Abbildung 3: Verwenden einen lokalen privaten Cache mit einem gemeinsamen cache_

Um große Caches zu unterstützen, die relativ langlebige Daten, einige
Cache-Services bieten eine hohe Verfügbarkeit-Option, die implementiert
automatisches Failover, wenn der Cache nicht mehr verfügbar ist. Dieser Ansatz
gewöhnlich bezieht die zwischengespeicherten Daten gespeichert auf einem primären replizieren
Cache-Server zu einem sekundären Cacheserver, und die Umstellung auf die
sekundären Server, wenn der primäre Server ausfällt oder Konnektivität ist
verloren. Um die Wartezeit auf mehrere Schreiben zugeordnet zu reduzieren
Reiseziele, wenn Daten in den Cache, auf dem primären geschrieben werden
Server, kann die Replikation auf den sekundären Server auftreten.
asynchron.  Dieser Ansatz führt zu der Möglichkeit, dass einige
zwischengespeicherter Informationen möglicherweise verloren, wenn der ausfällt, aber die
Anteil dieser Daten sollte klein sein im Vergleich zu der gesamten
Größe des Caches.

Wenn ein gemeinsamer Cache groß ist, kann es vorteilhaft für Partition werden die
zwischengespeicherte Daten über Knoten reduzieren die Möglichkeiten von Konflikten und
zur Verbesserung der Skalierbarkeit. Viele gemeinsame Caches unterstützen die Möglichkeit
dynamisch hinzufügen (und entfernen) Knoten und Ausgleich die Daten über
Partitionen. Dieser Ansatz kann bedeuten, Clusterbildung, wobei die
Client-Anwendungen als Auflistung von Knoten vorgestellt wird eine
nahtlose, einzelne Cache, aber intern die Daten wird gestreut
zwischen Knoten nach einige vordefinierte Vertriebsstrategie
die verteilt die Last gleichmäßig. Die [Daten Partitionieren Leitliniendokument](http://msdn.microsoft.com/library/dn589795.aspx)
auf der Microsoft enthält Webseite Weitere Informationen über mögliche
Partitionierungsstrategien.

Cluster kann auch weitere Verfügbarkeit des Caches hinzufügen; Wenn ein
Knoten ausfällt, ist der Rest des Caches noch zugänglich.
Clusterbildung wird häufig in Verbindung mit der Replikation verwendet.
und Failover; Jeder Knoten repliziert werden kann und das Replikat
schnell online geschaltet, wenn der Knoten ausfällt.

Viele lesen und schreiben, dass wahrscheinlich einzelne Daten umfassen wird
Werten oder Objekten. Jedoch möglicherweise gelegentlich
Speichern oder Abrufen von großen Datenmengen schnell notwendig.
Aussaat einen Cache beispielsweise schreiben Hunderte einbeziehen könnten oder
Tausende von Artikeln für den Cache oder Anwendung müssen
eine große Anzahl verwandter Elemente aus dem Cache als abrufen
Teil der gleichen Anforderung. Viele große Caches bieten batch
Operationen für diese Zwecke, eine Client-Anwendung zu aktivieren
eine große Anzahl von Elementen in einer einzelnen Anforderung Verpacken und
Zusammenhang mit der Durchführung einer Vielzahl-Overhead reduzieren
der kleine Anfragen.

## Zwischenspeichern und eventuelle Konsistenz

Das Cache-Aside-Muster hängt von der Instanz der Anwendung
Auffüllen des Caches, die Zugang zu den neuesten und
konsistenten Version der Daten. In einem System, die implementiert
eventuelle Konsistenz (z. B. ein replizierter Datenspeicher) kann dies
nicht der Fall sein. Eine Instanz einer Anwendung ändern könnte eine
Daten Posten und die zwischengespeicherte Version des jeweiligen Elements für ungültig erklären. Ein weiterer
Instanz der Anwendung möglicherweise versuchen, dieses Element zu lesen
Cache, wodurch ein Cache-Miss, so dass es liest die Daten aus der
Daten speichern und zum Cache hinzugefügt. Jedoch, wenn die Daten speichern
wurde nicht vollständig mit den anderen Replikaten synchronisiert die
Anwendungsinstanz lesen konnte, und füllen Sie den Cache mit der
der alte Wert.

Weitere Informationen zum Behandeln von Datenkonsistenz, finden Sie unter der
Konsistenz-Anleitung-Seite auf der Microsoft-Website.

### Schutz von zwischengespeicherten Daten

Unabhängig von der Cache-Dienst verwenden Sie, sollten Sie
So schützen Sie die Daten in den Cache von nicht autorisierten
Zugang. Es gibt zwei Hauptprobleme:

- Der Datenschutz der Daten im Cache.
- Daten wie es fließt zwischen den Cache und die
  Cache-Anwendung.

Um Daten im Cache zu schützen, kann der Cache-Dienst implementieren.
Authentifizierungsmechanismus erfordern, dass Anwendungen
sich identifizieren und Bewilligung Regelung, die
legt fest, welche Identitäten Daten im Cache zugreifen können und
Operationen (lesen und schreiben), die diese Identitäten sind
durchführen darf. Senkung der Gemeinkosten zugeordnet
Lesen und Schreiben von Daten, nachdem eine Identität gewährt wurde
Schreiben und/oder Lese-Zugriff auf den Cache, den Identität verwenden können
alle Daten im Cache. Wenn Sie benötigen, um den Zugriff zu beschränken
Teilmengen der zwischengespeicherten Daten, können Sie:

- (Unter Verwendung von verschiedenen Cache Cache in Partitionen aufgeteilt
  Servern) und gewähren Sie nur Zugriff auf Identitäten für die
  Partitionen, die sie sollten die Möglichkeit nutzen, oder
- Verschlüsseln Sie die Daten in jeder Teilmenge mit verschiedenen Schlüsseln
  und nur die Verschlüsselungsschlüssel zu Identitäten, die
  sollten Sie Zugriff auf jede Teilmenge haben. Eine Client-Anwendung
  noch können alle Daten im Cache abrufen,
  aber es werden nur die Daten für die It zu entschlüsseln
  die Schlüssel hat.

Zum Schützen der Daten als es fließt in und aus dem Cache Sie
die Sicherheitsfeatures, die vom Netz abhängig sind
Infrastruktur, die Clientanwendungen zum Herstellen verwenden der
-Cache. Wenn der Cache mit einem vor-Ort-Server implementiert wird
innerhalb einer Organisation, die die Client-Anwendungen hostet,
dann kann die Isolierung des Netzwerks selbst nicht erforderlich
sollten Sie zusätzlichen Maßnahmen treffen. Wenn der Cache entfernt befindet und
erfordert eine TCP oder HTTP-Verbindung über ein öffentliches Netzwerk (z. B.
wie das Internet) sollten Sie die Implementierung von SSL.

## Überlegungen zum Implementieren von caching mit Microsoft Azure

Azurblau bietet die Azure-Redis-Cache. Dies ist eine Implementierung
der Open source-Redis-Cache, der läuft als Dienst in einer
Azurblaue Datacenter. Es bietet eine Zwischenspeicherungsdienst, die sein kann
abgerufen von jeder Azure Anwendung, ob die Anwendung
als Cloud-Service, eine Website oder innen implementiert ein
Azurblaue virtuelle Maschine. Caches können vom Client freigegeben werden
Anwendungen, die die entsprechende Zugriffstaste zu haben.

Redis ist ein Hochleistungs-Zwischenspeicherung-Lösung, die bietet
Verfügbarkeit, Skalierbarkeit und Sicherheit. Es läuft in der Regel
als Dienst auf einem oder mehreren dedizierten Maschinen verteilt und
versucht, so viele Informationen wie möglich im Gedächtnis zu speichern
schnellen Zugriff zu gewährleisten. Diese Architektur soll vermitteln
geringe Latenz und hoher Durchsatz von reduzieren die Notwendigkeit
Führen Sie langsame i/o-Operationen.

Der Azure Redis-Cache ist kompatibel mit vielen der verschiedenen
APIs, die von Clientanwendungen verwendet. Wenn Sie vorhandene müssen
Anwendungen, die bereits Redis mit lokalen, verwenden die
Azurblaue Redis-Cache bietet einen schnellen Migrationspfad zu Zwischenspeichern
in der Wolke.

> [AZURBLAU. HINWEIS] Azurblau bietet auch der Cache-Dienst verwaltet. Dies
  Service basiert auf dem Microsoft AppFabric Cache-Engine. Es
  können Sie einen verteilten Cache zu erstellen, der gemeinsam genutzt werden können
  von lose gekoppelten Anwendungen. Der Cache wird auf gehostet.
  Hochleistungs-Server, die in einer Azure Datencenter.
  Jedoch, diese Option wird nicht mehr empfohlen und ist nur
  zur Verfügung gestellt, um vorhandene Anwendungen zu unterstützen, die gebaut wurden
  um es zu verwenden. Verwenden Sie für alle Neuentwicklungen die Azure Redis
  Stattdessen zwischengespeichert werden.
>
> Darüber hinaus unterstützt das Azure-Rolle Zwischenspeichern. Dieses feature
  können Sie einen Cache zu erstellen, die spezifisch für eine Cloud-Service.
  Der Cache wird gehostet von Instanzen einer Rolle Web oder Arbeitnehmer und
  kann nur als Teil der gleichen Rollen zugegriffen werden
  Wolke-Service-Bereitstellung-Einheit (eine Bereitstellungseinheit ist ein Satz
  der Rolleninstanzen bereitgestellt als Cloud-Service zu einer bestimmten
  Region). Der Cache ist gruppiert, und alle Instanzen der
  Rolle innerhalb der gleichen Bereitstellungseinheit, die den Cache zu hosten
  werden Sie Teil der gleichen Cache-Cluster. Vorhandene Anwendungen
  die Verwendung in Rolle zwischenspeichern kann weiterhin tun, aber
  Migration zu den Azure-Redis-Cache kann weitere Vorteile bringen.
  Weitere Informationen dazu Azure Redis Cache verwenden
  oder eine in-Rolle cache, besuchen Sie die Seite
  [Die Azure-Cache-Angebot ist das richtige für mich?](http://msdn.microsoft.com/library/azure/dn766201.aspx) auf der Microsoft-Website.


### Features von Redis

Redis ist mehr als eine einfache Cache-Server; Es bietet eine verteilte im Speicher
Datenbank mit einer umfangreichen Befehlssatz, der viele gängige Szenarien unterstützt,
Siehe den Abschnitt Anwendungsfälle für Redis Zwischenspeichern, die später in diesem
Dokuments. Dieser Abschnitt fasst einige der wichtigsten Features, die Redis
bietet.

### Redis als Datenbank im Arbeitsspeicher

Redis unterstützt sowohl Lese- und Schreiboperationen. Anders als viele Caches (die als transitorische Datenspeichern anzusehen ist), schreibt nach Systemausfall per in gespeichert werden regelmäßig lokale Snapshot-Datei oder in einer Protokolldatei Anhängen nur geschützt werden können. Alle Schreibvorgänge sind asynchron und blockieren nicht Kunden Daten lesen und schreiben. Wann Redis gestartet, es liest die Daten aus der Snapshot oder Log-Datei und verwendet diese zum Erstellen von in-Memory-Cache. Weitere Informationen finden Sie unter [REDIS Persistenz](http://redis.io/topics/persistence) auf der Redis-Website.

> [AZURBLAU. HINWEIS] Redis garantiert nicht, dass alle Schreibvorgänge im Ereignisprotokoll gespeichert wird
  ein schwerwiegender Fehler, sondern im schlimmsten Fall sollte man nur ein paar Sekunden verlieren.
  im Wert von Daten. Denken Sie daran, dass ein Cache nicht als fungieren soll ein
  maßgebende Datenquelle, und es liegt in der Verantwortung der Anwendungen
  Nutzung des Cache sicherzustellen, dass kritischen Daten erfolgreich zu gespeichert ist ein
  entsprechenden Datenspeicher. Weitere Informationen finden Sie unter das Cache-Aside-Muster.

#### Redis-Datentypen

Redis ist ein Schlüssel-Wert-Speicher, wo Werte, einfache Typen oder komplexe Datenstrukturen wie Hashes, Listen enthalten kann, und legt diese fest. Es unterstützt eine Reihe von atomare Operationen auf diese Datentypen. Schlüssel kann permanent oder Umbauten eine begrenzte Zeit zu leben, an welcher, die Stelle der Schlüssel und den entsprechenden Wert automatisch aus dem Cache entfernt werden. Weitere Informationen über Redis-Schlüssel und Werte finden Sie auf der Seite [Eine Einführung in die Datentypen Redis und Abstraktionen](http://redis.io/topics/data-types-intro) auf der Redis-Website.

#### Redis-Replikation und clustering

Redis unterstützt Meister/untergeordnet-Replikation zur Sicherstellung der Verfügbarkeit und Durchsatz zu pflegen; Schreibvorgänge auf einem Redis-master-Knoten repliziert werden, auf einen oder mehr untergeordnete Knoten und Lesevorgänge können durch den Master oder die Untergebenen serviert werden. Im Falle einer Netzwerk-Partition können Untergebenen weiterhin Daten dienen und dann transparent synchronisieren mit dem Master, wenn die Verbindung wiederhergestellt ist. Für weitere Informationen besuchen Sie die [Replikation](http://redis.io/topics/replication) Seite der Redis-Website.

Redis bietet auch die Clusterbildung, sodass Sie transparent Partitionieren von Daten in Scherben serverübergreifend und verteilt die Last. Dieses Feature verbessert die Skalierbarkeit, da neue Redis-Server hinzugefügt werden können und die Daten neu partitioniert wie die Größe des Caches erhöht. Darüber hinaus kann jeder Server im Cluster repliziert werden, mithilfe der Meister/untergeordnet Replikation um Verfügbarkeit auf jedem Knoten im Cluster zu gewährleisten. Weitere Informationen über clustering und Splitter, die [Redis Cluster Tutorial-Seite](http://redis.io/topics/cluster-tutorial) auf der Redis-Website.

> [AZURBLAU. HINWEIS] Azurblaue Redis Cache unterstützt nicht derzeit clustering. Wollen Sie einen Redis-Cluster zu erstellen, können Sie Ihre eigenen benutzerdefinierten Redis-Server erstellen. Weitere Informationen finden Sie im Abschnitt Erstellen einer Custom Redis Cache weiter unten in diesem Dokument.

### Redis Speichernutzung

Ein Redis-Cache hat eine endliche Größe abhängig von verfügbaren Ressourcen auf dem Hostcomputer. Wenn Sie einen Redis-Server konfigurieren, können Sie die maximale Größe des Speichers angeben, die sie verwenden kann. Ein Schlüssel in einem Redis-Cache kann mit eine Ablaufzeit konfiguriert werden, nach der automatisch aus dem Cache entfernt wird. Dieses Feature können verhindern, dass den Cache im Arbeitsspeicher, die mit alten oder veraltete Daten gefüllt.

Da der Speicher voll ist, kann Redis automatisch Schlüssel und deren Werte entfernen von nach einer Reihe von Richtlinien. Der Standardwert ist LRU (zuletzt verwendet), aber Sie können auch auswählen, andere Politikbereiche wie Schlüssel nach dem Zufallsprinzip entfernen oder Deaktivieren von Vertreibung insgesamt (in diesem Fall Versuche, Hinzufügen von Elementen zum Cache fehl, wenn es voll ist). Die Seite [Redis verwenden als LRU-Cache](http://redis.io/topics/lru-cache) finden Sie weitere Informationen.

### Redis Transaktionen und Chargen

Redis enables a client application to submit a series of operations that read and write data in the cache as an atomic transaction. All of the commands in the transaction are guaranteed to be executed sequentially and no commands issued by other concurrent clients will be interwoven between them. However, these are not true transactions as a relational database would perform them. Transaction processing consists of two stages; command queuing and command execution. During the command queuing stage, the commands that comprise the transaction are submitted by the client. If some sort of error occurs at this point (such as a syntax error, or the wrong number of parameters) then Redis will refuse to process the entire transaction and discard it. During the execution phase, Redis performs each queued command in sequence. If a command fails during this phase Redis will continue with the next queued command and it does not roll back the effects of any commands that have already been executed. This simplified form of transaction helps to maintain performance and avoid performance problems caused by contention. Redis does implement a form of optimistic locking to assist in maintaining consistency. For detailed information about transactions and locking with Redis, visit the [Transaktionsseite](http://redis.io/topics/transactions) auf der Redis-Website.

Redis unterstützt auch nicht transaktionale Batchverarbeitung von Anforderungen. Das Redis-Protokoll, die Clients zum Senden von Befehlen an einen Redis-Server ermöglicht einem Client, eine Reihe von Vorgängen im Rahmen der gleichen Anforderung zu senden. Dadurch kann die um Paketfragmentierung im Netzwerk zu verringern. Wenn der Batch verarbeitet wird, wird jeder Befehl ausgeführt. Im Gegensatz zu einer Transaktion wenn eine dieser Befehle aufgrund eines fehlerhaften werden sie abgelehnt werden aber die restlichen Befehle ausgeführt werden. Es gibt auch keine Garantie auf die Reihenfolge, in der die Befehle im Batch verarbeitet werden.

### Redis Sicherheit

Redis konzentriert sich rein auf die Bereitstellung von schnellen Zugriffs auf Daten und soll in einer vertrauenswürdigen Umgebung ausgeführt und nur von vertrauenswürdigen Clients zugegriffen werden. Redis unterstützt nur eine eingeschränkte Sicherheit-Modelle auf Passwort-Authentifizierung basieren (es ist möglich, Authentifizierung vollständig zu entfernen, obwohl dies nicht empfohlen wird). Alle authentifizierten Clients teilen das gleiche globale Passwort und haben Zugriff auf dieselben Ressourcen. Wenn Sie umfassenderen Login-Sicherheit benötigen, müssen Sie Ihre eigenen Security-Layer vor dem Redis-Server implementieren und alle Clientanforderungen sollten durch diese zusätzliche Schicht übergeben; Redis sollte nicht direkt auf nicht vertrauenswürdigen oder nicht authentifizierte Clients verfügbar gemacht werden.

Sie können den Zugriff auf Befehle deaktivieren sie oder umbenennen sie (und, die neuen Namen nur privilegierte Kunden) einschränken.

Redis unterstützt nicht direkt jede Form der Verschlüsselung der Daten, so dass alle Codierung von Clientanwendungen ausgeführt werden muss. Darüber hinaus bietet Redis keine Form der Transportsicherheit, also wenn Sie benötigen, um Daten zu schützen, wie es über das Netzwerk fließt Sie einen SSL-Proxy implementieren sollten.

Weitere Informationen finden Sie auf der [Redis Sicherheit](http://redis.io/topics/security) Seite der Redis-Website.

> [AZURBLAU. HINWEIS] Azurblaue Redis Cache bietet eine eigene Security-Layer durch die Clients eine Verbindung herstellen; die zugrunde liegenden Redis
  Server werden nicht in das öffentliche Netz verfügbar gemacht.

### Nutzung des Azure Redis-cache

Die Azure-Redis-Cache ermöglicht den Zugriff auf Redis-Servern, auf Servern unter einer Azure Datencenter gehostet; Es wirkt wie eine Fassade, die Zugangskontrolle und Sicherheit bietet. Sie können einen Cache über das Azure Management Portal bereitstellen. Das Portal bietet eine Reihe von vordefinierten Konfigurationen reichen von einem 53GB Cache läuft als ein dedizierter Dienst, der unterstützt, dass die SSL-Kommunikation (für Privacy) und Meister/untergeordnet Replikation mit einem SLA 99,9 % Verfügbarkeit, hinunter zu einer 250MB cache-ohne Replikation (keine Verfügbarkeit-Garantien) auf gemeinsam genutzte Hardware ausgeführt.

Über die Azure Management-Portal können auch die Politik der Vertreibung des Caches zu konfigurieren und Steuern den Zugriff auf den Cache durch Hinzufügen von Benutzern zu Rollen zur Verfügung gestellt; Eigentümer, Mitwirkende, Leser. Diese Rollen definieren die Vorgänge, die Mitglieder ausführen können. Z. B. Mitglieder der Eigentümerrolle haben vollständige Kontrolle über den Cache (einschließlich Sicherheit) und seinen Inhalt, Mitglieder der Rolle Mitwirkender können lesen und Schreiben von Informationen in den Cache und Mitglieder der Rolle Leser können nur Daten aus dem Cache abrufen.

Die meisten administrative Aufgaben werden durch das Azure Management Portal durchgeführt, und aus diesem Grund sind viele administrative Befehle zur Verfügung, in der Standardversion von Redis nicht verfügbar, einschließlich der Möglichkeit zum Ändern der Konfiguration programmgesteuert Herunterfahren der Redis-Server konfigurieren, zusätzliche Sklaven oder gewaltsam speichern Daten auf die Festplatte.

The Azure management portal includes a convenient graphical display that enables you to monitor the performance of the cache. For example, you can view the number of connections being made, the number of requests performed, the volume of reads and writes, and the number of cache hits versus cache misses. Using this information you can determine the effectiveness of the cache and if necessary switch to a different configuration or change the eviction policy. Additionally, you can create alerts that send email messages to an administrator if one or more critical metrics fall outside of an expected range. For example, if the number of cache misses exceeds a specified value in the last hour, an administrator could be alerted as the cache may be too small or data may be being evicted too quickly.

Sie können auch die CPU-, Speicher- und Netzwerkauslastung für den Cache überwachen.

> [AZURBLAU. HINWEIS] Azurblaue Redis Cache dient rein als ein Cache anstatt von einer Datenbank. Folglich implementiert es nicht aktuell Redis Persistenz.

Besuchen Sie für weitere Informationen und Beispiele, die zeigen, wie zum Erstellen und Konfigurieren einer Azure-Redis-Cache die Seite [Runde auf himmelblau Redis-Cache](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) auf dem Azure Blog.

## Zwischenspeicherung Session-State und HTML-Ausgabe

Wenn Sie Gebäude ASP.NET-Anwendungen Web mithilfe von Azure Webrollen ausführen, Sitzungszustandsinformationen und HTML-Ausgabe in einem Azure Redis-Cache können Sie speichern. Die Sitzungszustandsanbieter für Azure Redis Cache können Sie Session-Informationen zwischen verschiedenen Instanzen einer ASP.NET-Webanwendung teilen und ist sehr nützlich bei Web Bauernhof Situationen wo Client-Server-Affinität ist nicht verfügbar und Sitzungs-Daten im Arbeitsspeicher Zwischenspeichern nicht angebracht wäre.

Verwenden die Sitzungszustandsanbieter mit Azure Redis Cache bietet mehrere Vorteile, einschließlich:

- Es kann Sitzungsstatus unter anderem eine große Anzahl von Instanzen einer ASP.NET Web-Anwendung, bietet verbesserte Skalierbarkeit teilen,
- Es unterstützt kontrollierte, gleichzeitiger Zugriff auf die gleichen Sitzungszustandsdaten für mehrere Leser und einen einzigen Schreiber und
- Es können Komprimierung Speicherplatz sparen und Verbesserung der Netzwerkleistung.

Für weitere Informationen besuchen Sie die [ASP.NET Sitzungszustandsanbieters für Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn690522.aspx) Seite auf der Microsoft-Website.

> [AZURBLAU. HINWEIS] Verwenden Sie nicht die Sitzungszustandsanbieter für Azure Redis Cache für ASP-Anwendungen, die außerhalb der Azure-Umgebung ausgeführt. Die Wartezeit für den Zugriff auf den Cache von außerhalb Azure kann die Leistungsvorteile der Zwischenspeicherung von Daten vermieden werden.

Ebenso können der Output Cache Provider für Azure Redis Cache Sie von einer ASP.NET Web-Anwendung generierten HTTP-Antworten zu speichern. Mit dem Output Cache Provider mit Azure Redis-Cache kann die Antwortzeiten von Anwendungen verbessern, die komplexe HTML-Ausgabe zu machen; Anwendungsinstanzen erzeugende ähnliche Antworten machen können verwenden freigegebene Ausgabe Fragmente in den Cache, anstatt diese HTML-Ausgabe neu generieren.  Für weitere Informationen besuchen Sie die [ASP.NET Output Cache Provider für Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn798898.aspx) Seite auf der Microsoft-Website.

## Erstellen eines benutzerdefinierten Redis-Caches

The Azure Redis cache acts as a façade to the underlying Redis servers. Currently it supports a fixed set of configurations but does not provide for Redis clustering. If you require an advanced configuration that is not covered by the Azure Redis cache (such as a cache bigger than 53GB) you can build and host your own Redis servers by using Azure virtual machines. This is a potentially complex process as you may need to create several VMs to act as master and subordinate nodes if you want to implement replication. Furthermore, if you wish to create a cluster, then you will need multiple masters and subordinate servers; a minimal clustered, replication topology that provides a high degree of availability and scalability comprises at least 6 VMs organized as 3 pairs of master/subordinate servers (a cluster must contain at least 3 master nodes). Each master/subordinate pair should be located close together to minimize latency, but each set of pairs can be running in different Azure datacenters located in different regions if you wish to locate cached data close to the applications that are most likely to use it. The page [Redis auf einem CentOS Linux VM in Azure ausgeführt](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) auf der Microsoft exemplarisch Webseite ein Beispiel zum Erstellen und Konfigurieren eines Redis-Knotens als einer Azure-VM ausgeführt.

Hinweis Wenn Sie Ihren eigenen Redis-Cache auf diese Weise implementieren, sind Sie verantwortlich für die Überwachung, Verwaltung und Sicherung des Dienstes.

## Partitionierung kesch Redis

Cache-Partitionierung beinhaltet den Cache auf mehreren Computern aufteilen. Diese Struktur bietet Ihnen mehrere Vorteile über mit einem einzigen Cache-Server, einschließlich:

- Erstellen einen Cache, ist viel größer als auf einem Server gespeichert werden können.
- Verteilen von Daten auf Servern, die Verfügbarkeit zu verbessern. Wenn ein Server ausfällt oder unzugänglich wird, steht nur die Daten, die sie hält; Weiterhin können die Daten auf den verbleibenden Servern zugegriffen werden. Für einen Cache, das ist nicht entscheidend wie die zwischengespeicherten Daten nur eine flüchtige Kopie der Daten in einer Datenbank gehalten und zwischengespeicherte Daten auf einem Server, der unzugänglich wird stattdessen auf einem anderen Server zwischengespeichert werden können.
- Breitete sich die Belastung über Server, wodurch die Leistung und Skalierbarkeit verbessert.
- Geolocating Daten schließen an die Benutzer, die Zugriff Verringerung der Latenz.

Für einen Cache ist die häufigste Form der Partitionierung Splitter. In dieser Strategie ist jeder Partition (oder Splitter) ein Redis-Cache aus eigenem Recht. Daten richtet sich an eine bestimmte Partition mithilfe von Splitter-Logik, die verschiedene Ansätze verwenden können, um die Daten zu verteilen. Die [Splitter-Muster](http://msdn.microsoft.com/library/dn589797.aspx) finden Sie weitere Informationen über die Umsetzung der Splitter.

Um in einem Cache Redis Partitionierung zu implementieren, können Sie eine der folgenden Vorgehensweisen annehmen:

- _Serverseitige Abfrage weiterleiten._ Bei diesem Verfahren eine Client-Anwendung sendet eine Anfrage an eines der
  Redis Servern, die den Cache (wahrscheinlich der nächste Server) bilden. Jede Redis-Server speichert
  Metadaten, die die Partition zu beschreiben, dass sie hält, und auch Informationen darüber, welche enthält
  Partitionen befinden sich auf anderen Servern. Der Redis-Server prüft die Anfrage des Clients und, wenn es
  gelöst werden können lokal die angeforderte Operation, sonst wird er weiter, führt die
  Anfrage an den entsprechenden Server. Dieses Modell wird von Redis Cluster implementiert und ist
  ausführlicher beschrieben, auf die [Redis Cluster-Lernprogramm](http://redis.io/topics/cluster-tutorial) Seite der Redis-Website. Redis Clusterbildung
  ist transparent und Client-Anwendungen und zusätzliche Redis Cluster Server hinzugefügt werden können
  (und die Daten neu partitioniert) ohne dass Sie die Clients neu konfigurieren.

  > [AZURBLAU. WICHTIG] Azurblaue Redis Cache unterstützt nicht derzeit Redis clustering. Wenn Sie möchten
  Implementieren Sie diesen Ansatz, dann Sie einen benutzerdefinierten Redis-Cache zu bauen sollte, wie oben beschrieben.

- _Client-seitige Partitionierung._ In diesem Modell enthält die Clientanwendung Logik (möglicherweise in
  Form einer Bibliothek), dass die Routen an den entsprechenden Redis-Server anfordert. Dieser Ansatz
  verwendbar mit Azure Redis Cache; Erstellen Sie mehrere Azure Redis Caches (eine für jedes Daten
  Partition) und die Client-seitige Logik, die die Anforderungen an die korrekte weiterleitet implementieren
  -Cache. Wenn das Partitionierungsschema ändert (wenn zusätzliche Azure Redis Caches erstellt werden,
  zum Beispiel), können Client-Anwendungen müssen neu konfiguriert werden.

- _Proxy unterstützt-Partitionierung._ In diesem System, Client-Anwendungen senden Anforderungen an eine
  zwischengeschalteten Proxy service, der versteht, wie Daten partitioniert werden und Routen
  die Anforderung an den entsprechenden Redis-Server. Dieser Ansatz kann auch mit Azure verwendet werden
  Redis Cache; der Proxy-Dienst könnte als eine Azure Cloud-Service implementiert werden. Dies
  Vorgehensweise erfordert ein zusätzliches Maß an Komplexität, den Service zu implementieren und
  Anforderungen dauert möglicherweise länger als die Verwendung von Client-seitige Partitionierung ausführen.

Die Seite [Partitionierung: wie Daten zwischen mehreren Redis-Instanzen geteilt](http://redis.io/topics/partitioning)
über die Redis bietet die Website Weitere Informationen zum Implementieren von Partitionierung mit Redis.

### Redis-Cache-Client-Anwendungen implementieren

Redis unterstützt Client-Anwendungen in zahlreichen Programmiersprachen geschrieben. Wenn Sie neue Anwendungen mit .NET Framework erstellen, wird empfohlen, die StackExchange.Redis-Client-Bibliothek zu verwenden. Diese Bibliothek bietet eine .NET Framework-Objektmodell, die die Details für die Verbindung zu einem Server Redis, Befehle senden und empfangen von Antworten abstrahiert. Es ist in Visual Studio verfügbaren als NuGet Paket. Diese gleiche Bibliothek können zum Herstellen einer Azure Redis-Cache oder einem benutzerdefinierten Redis-Cache auf einem virtuellen Computer gehostet.

Zum Herstellen einer Verbindung mit eines Redis-Servers verwenden Sie die statische `Connect` Methode der `ConnectionMultiplexer` Klasse. Die Verbindung, die diese Methode erstellt soll während der gesamten Lebensdauer der Clientanwendung verwendet werden, und die gleiche Verbindung kann von mehreren Threads gleichzeitig verwendet werden; nicht wieder verbinden Sie und trennen Sie jedes Mal, wenn Sie einen Redis-Vorgang ausführen, da dies die Leistung beeinträchtigen kann. Sie können die Verbindungsparameter, wie z.B. die Adresse der Redis-Host und das Kennwort angeben. Wenn Sie den Azure Redis-Cache verwenden, generiert das Kennwort ist dies entweder die primäre oder sekundäre Taste für den Azure-Redis-Cache mithilfe der Azure-Verwaltungsportal.

Nachdem Sie mit der Redis-Server verbunden haben, erhalten Sie einen Handle für die Redis-Datenbank, die als Cache dient. Die Redis-Verbindung bietet die `GetDatabase` Methode, dies zu tun. Sie können dann Elemente aus dem Cache abrufen und Speichern von Daten im Cache mithilfe der `StringGet` und `StringSet` Methoden. Diese Methoden erwarten einen Schlüssel als Parameter, und entweder das Element im Cache, der hat einen übereinstimmenden Wert (zurück`StringGet`) oder fügen Sie das Element mit diesem Schlüssel (Cache`StringSet`).

Je nach Standort der Redis-Server können viele Operationen Wartezeiten anfallen, während eine Anforderung an den Server übertragen wird und eine Antwort an den Client zurückgegeben. Die StackExchange-Bibliothek bietet asynchrone Versionen von vielen Methoden, die es macht, um Clientanwendungen reaktionsfähig bleiben zu helfen. Diese Methoden unterstützen die [Aufgabenbasierte asynchrone Muster](http://msdn.microsoft.com/library/hh873175.aspx) in das .NET Framework.

Der folgende Codeausschnitt zeigt eine Methode namens `RetrieveItem` Das zeigt ein Beispiel für eine Implementierung des Musters Cache übrigens auf der Grundlage von Redis und die StackExchange-Bibliothek. Die Methode nimmt einen String-Schlüssel-Wert, und versucht, das entsprechende Element aus dem Redis-Cache abrufen, durch Aufrufen der `StringGetAsync` Methode (die asynchrone Version von `StringGet`). Wenn das Element nicht gefunden wird, wird es geholt von der zugrunde liegenden Datenquelle mithilfe der `GetItemFromDataSourceAsync` Methode (die eine lokale Methode und nicht Teil der StackExchange-Bibliothek), und dann zum Cache hinzugefügt wird, mithilfe der `StringSetAsync` Methode, so dass sie das nächste Mal schneller abgerufen werden können.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Die `StringGet` und `StringSet` Methoden sind nicht auf Abrufen oder Speichern von String-Werte beschränkt; Sie können jedem Element serialisiert als ein Array von Bytes übernehmen. Möchten Sie ein .NET-Objekt speichern können als Bytestream zu serialisieren und die StringSet-Methode verwenden, um es in den Cache schreiben. Ebenso können Sie ein Objekt aus dem Cache gelesen werden mithilfe der StringGet-Methode und als .NET-Objekt zu deserialisieren. Der folgende Code zeigt eine Reihe von Erweiterungsmethoden für die IDatabase-Schnittstelle (die GetDatabase-Methode einer Redis-Verbindung gibt eine `IDatabase` -Objekt), und einige Beispiel-Code, der verwendet diese Methoden zum Lesen und schreiben ein BlogPost-Objekt im Cache:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Der folgende Code veranschaulicht eine Methode namens `RetrieveBlogPost` Diese Erweiterungsmethoden zum Lesen und Schreiben einer serialisierbaren verwendet `BlogPost` Objekt im Cache nach dem Cache-beiseite-Muster:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis unterstützt Befehl pipelining, wenn eine Clientanwendung mehrere asynchrone Anforderungen sendet. Redis Multiplext die Anfragen, die dieselbe Verbindung verwenden, anstatt empfangen und reagiert auf Befehle in einer strengen Reihenfolge. Dieser Ansatz hilft, die um Wartezeit zu verringern durch eine effizientere Nutzung des Netzes. Der folgende Codeausschnitt zeigt ein Beispiel, das die Informationen über zwei Kunden gleichzeitig abruft. Der Code sendet zwei Anforderungen und führt dann einige andere Verarbeitung (nicht abgebildet) vor dem warten auf die Ergebnisse zu erhalten. Die Wait-Methode des das Cache-Objekt ähnelt das .NET Framework Task.Wait-Methode:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

Die Seite [Entwickeln Sie für himmelblau Redis-Cache](http://msdn.microsoft.com/library/azure/dn690520.aspx) Microsoft bietet die Website Weitere Informationen zum Schreiben von Clientanwendungen, die den Azure Redis-Cache verwenden können. Weitere Informationen sind auf der [Einfache Verwendung-Seite](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) auf der Seite und der StackExchange.Redis-website [Rohrleitungen und Multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) auf dieselbe Website bietet weitere Informationen zu asynchronen Operationen und pipelining mit Redis und die StackExchange-Bibliothek.  Use-Cases für Redis Caching weiter unten im Abschnitt dieser Anleitung enthält Beispiele für einige der fortgeschritteneren Techniken, die Sie, um Daten in einem Redis-Cache anwenden können.

## Anwendungsfälle für die Zwischenspeicherung von Redis

The simplest use of Redis for caching concerns storing key/value pairs where the value is an uninterpreted string of arbitrary length that can contain any binary data (it is essentially  an array of bytes that can be treated as a string). This scenario was illustrated in the section Implementing Redis Cache Client Applications earlier in this guidance. You should note that keys also contain uninterpreted data, so you can use any binary information as the key, although the longer the key is the more space it will take to store, and the longer it will take to perform lookup operations. For usability and ease of maintenance design your keyspace carefully and use meaningful (but not verbose) keys. For example, use structured keys such as "customer:100" to represent the key for the customer with ID 100 rather than simply "100". This scheme enables you to easily distinguish between values that store different data types. For example, you could also use the key "orders:100" to represent the key for the order with ID 100.

Neben eindimensionalen Binärzeichenfolgen kann ein Wert im Schlüssel/Wert-Paar Redis auch halten besser strukturierte Informationen, einschließlich Listen, setzt (sortiert und Unsortiert) und hasht. Redis bietet einen umfangreichen Befehlssatz, der diese Typen bearbeiten können, und viele dieser Befehle sind .NET Framework-Anwendungen durch eine Client-Bibliothek wie StackExchange zur Verfügung. Die Seite [Eine Einführung in die Datentypen Redis und Abstraktionen](http://redis.io/topics/data-types-intro) über die Redis bietet die Website eine ausführlichere Übersicht über diese Typen und die Befehle, die Sie verwenden können, um sie zu bearbeiten.

In diesem Abschnitt werden einige häufige Anwendungsfälle für diese Datentypen und Befehle zusammengefasst.

### Durchführen von atomaren und Batch-Operationen

Redis unterstützt eine Reihe von atomaren Get- und Set-Operationen auf Zeichenfolgenwerte. Diese Operationen entfernen möglich Rennen gefahren, die auftreten können, wenn Sie separate verwenden `GET` und `SET` Befehle. Die verfügbaren Vorgänge umfassen:

- `INCR`, `INCRBY`, `DECR`, und `DECRBY` die führen atomare Inkrementieren und Dekrementieren Operationen auf
  ganzzahlige numerische Datenwerte. Die StackExchange-Bibliothek bietet überladene Versionen von der
  `IDatabase.StringIncrementAsync` und `IDatabase.StringDecrementAsync` Methoden ausführen
  Diese Vorgänge und die Rückkehr der resultierende Wert im Cache gespeichert. Der folgende code
  Codeausschnitt zeigt, wie diese Methoden:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET` die einen Schlüssel zugeordneten Wert abruft und ändert ihn in einen neuen Wert. Die
  StackExchange-Bibliothek stellt dieser Vorgang zur Verfügung, durch die `IDatabase.StringGetSetAsync`
  -Methode. Der folgende Codeausschnitt zeigt ein Beispiel dieser Methode. Dieser Code gibt den aktuellen
  Wert mit dem Schlüssel "-: Datenzähler" aus dem vorherigen Beispiel verbunden und setzt den Wert
  für diesen Schlüssel zurück auf NULL, alle als Teil des gleichen Vorgangs:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` und `MSET`, die zurückgeben oder eine Reihe von String-Werte als eine einzelne Operation geändert werden können. Die
  `IDatabase.StringGetAsync` und `IDatabase.StringSetAsync` Methoden sind überladen zur Unterstützung
  Diese Funktionalität, wie im folgenden Beispiel gezeigt:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

Sie können auch mehrere Operationen in einer einzigen Transaktion Redis kombinieren, wie im Abschnitt Redis Transaktionen und Batches in dieser Anleitung beschrieben. Die StackExchange-Bibliothek unterstützt Transaktionen durch die `ITransaction` Schnittstelle. Sie können ein ITransaction-Objekt zu erstellen, mithilfe der IDatabase.CreateTransaction-Methode und Befehle aus, um die Transaktion mithilfe der bereitgestellten Methoden aufrufen `ITransaction` Objekt. Die `ITransaction` Schnittstelle bietet Zugriff auf einen ähnlichen Satz von Methoden wie der `IDatabase` Schnittstelle, mit der Ausnahme, dass alle Methoden asynchron sind; Sie sind nur durchgeführt, wenn die `ITransaction.Execute` Methode wird aufgerufen. Von der Execute-Methode zurückgegebene Wert angibt, ob die Transaktion erfolgreich erstellt wurde (true) oder es fehlgeschlagen ist (false).

Der folgende Codeausschnitt zeigt ein Beispiel dieser inkrementiert bzw. dekrementiert zwei Zähler als Teil der gleichen Transaktion:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Denken Sie daran, dass im Gegensatz zu Transaktionen in relationalen Datenbanken Redis-Transaktionen sind. Die Execute-Methode einfach Warteschlange alle Befehle, die für die Ausführung die Transaktion bilden und wenn irgendwelche von ihnen fehlerhaft ist, dann ist die Transaktion abgebrochen. Wenn alle Befehle erfolgreich in der Warteschlange befinden, wird jeder Befehl asynchron ausgeführt werden. Wenn ein Befehl fehlschlägt, werden die anderen immer noch die Verarbeitung fortgesetzt. Möchten Sie überprüfen, ob ein Befehl erfolgreich ausgeführt wurde müssen Sie die Ergebnisse des Befehls rufen Sie mithilfe der Result-Eigenschaft des entsprechenden Vorgangs, wie im obigen Beispiel gezeigt. Lesen Sie das Ergebnis, die Eigenschaft blockiert wird, bis der Vorgang abgeschlossen ist.

Weitere Informationen finden Sie unter der [Transaktionen in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) Seite auf der Website StackExchange.Redis.

Für das Ausführen von Batchoperationen, können Sie die IBatch-Schnittstelle der StackExchange Bibliothek. Diese Schnittstelle bietet Zugriff auf einen ähnlichen Satz von Methoden wie die IDatabase-Schnittstelle, mit der Ausnahme, dass alle Methoden asynchron sind. Ein IBatch-Objekt mithilfe der IDatabase.CreateBatch-Methode erstellen und führen Sie dann den Batch mit der IBatch.Execute-Methode, wie im folgenden Beispiel gezeigt. Diesen Code einfach legt einen String-Wert, und inkrementiert bzw. dekrementiert die gleichen Indikatoren, die im vorherigen Beispiel verwendet und zeigt die Ergebnisse:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Es ist wichtig zu verstehen, dass im Gegensatz zu einer Transaktion, wenn ein Befehl in einem Batch fehlschlägt, weil es fehlerhaft ist die anderen Befehle noch ausführen können; die IBatch.Execute-Methode gibt keine Angabe von Erfolg oder Misserfolg zurück.

### Fire-and-forget-Cache-Vorgänge

Redis unterstützt Fire-and-forget-Operationen mithilfe von Befehlsflags. In diesem Fall der Client einfach initiiert eine Operation aber hat kein Interesse am Ausgang und wartet nicht auf den Befehl abgeschlossen werden. Das folgende Beispiel zeigt, wie den INCR-Befehl als eine Fire-and-forget-Operation durchführen:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Automatisch ablaufende Schlüssel

Wenn Sie ein Element in einem Redis-Cache speichern, können Sie einen Timeout angeben, nach dem das Element automatisch aus dem Cache entfernt wird. Sie können auch Abfragen, wieviel mehr Zeit ein Schlüssel hat, bevor es abläuft, mit der `TTL` Befehl; Dieser Befehl ist mit der IDatabase.KeyTimeToLive-Methode für StackExchange-Anwendungen zur Verfügung.

Der folgende Codeausschnitt zeigt ein Beispiel für eine Ablaufzeit von 20 Sekunden auf einem Schlüssel einstellen und Abfragen die verbleibende Gültigkeitsdauer des Schlüssels:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Sie können auch die Ablaufzeit zu einem bestimmten Datum und Uhrzeit festlegen, mithilfe des Befehls ablaufen, wie die KeyExpireAsync-Methode der StackExchange-Library:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Tipp:_ Sie können manuell ein Element aus dem Cache entfernen, mithilfe des DEL-Befehls, der die StackExchange-Bibliothek wie die IDatabase.KeyDeleteAsync-Methode zur Verfügung steht.

### Mithilfe von Tags zu um Kreuz-zwischengespeicherte Elemente Korrelat

Ein Redis-Satz ist eine Sammlung von mehreren Elementen, die einen einzigen Schlüssel besitzen. Sie können eine Gruppe erstellen, mithilfe des Befehls SADD. Sie können die Elemente in einem Satz abrufen, mithilfe des Befehls SMEMBERS. Die StackExchange-Bibliothek implementiert die SADD-Befehl über die IDatabase.SetAddAsync-Methode und der SMEMBERS-Befehl mit der IDatabase.SetMembersAsync-Methode. Sie können auch vorhandene Sätze neue Sets erstellen, mithilfe der SDIFF (Differenzmenge), SINTER (Schnittmenge) und SUNION (Vereinigungsmenge) Befehle kombinieren. Die StackExchange-Bibliothek vereint diese Vorgänge in der IDatabase.SetCombineAsync-Methode; der erste Parameter dieser Methode gibt die Set-Operation durchführen.

Die folgenden Code-Schnipsel zeigen, wie Sätze für schnell speichern und Abrufen von Sammlungen verwandter Elemente hilfreich sein können. Dieser Code verwendet den BlogPost-Typ, der in den Abschnitt Durchführung Redis Cache Clientanwendungen beschrieben. Ein BlogPost-Objekt enthält vier Felder — eine ID, einen Titel, eine Rangliste-Score und eine Sammlung von Tags. Der erste folgenden Codeausschnitt zeigt die Beispieldaten zum Auffüllen einer C#-BlogPost Objekte verwendet:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Können die Tags für jedes Objekt BlogPost als Gruppe in einem Redis-Cache gespeichert werden und jede Gruppe mit der ID der BlogPost. Dies ermöglicht es einer Anwendung um schnell alle Tags, die Zugehörigkeit zu einem bestimmten Blog-Eintrag zu finden. Um in die entgegengesetzte Richtung Suche aktivieren und alle Blog-Posts, die ein bestimmtes Tag gemeinsam zu finden, erstellen Sie eine andere Gruppe, die enthält der Blog-posts verweisen auf die Tag-ID im Schlüssel:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Diese Strukturen können Sie viele allgemeine Abfragen sehr effizient ausführen. Beispielsweise können Sie finden und anzeigen alle Tags für Blog-Post 1 wie folgt:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Finden Sie alle Tags, die gemeinsam Blog sind 1 und Blog Post 2 post durch Ausführen einer Schnittmenge-Operation, wie folgt:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Und finden Sie alle Blogbeiträge, die ein bestimmtes Tag enthalten:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Feststellung, die kürzlich verwendeten Elemente

A common problem required by many applications is to find the most recently accessed items. For example, a blogging site might want to display information about the most recently read blog posts. You can implement this functionality by using a Redis list. A Redis list contains multiple items that share the same key, but the list acts as a double-ended queue. You can push items on to either end of the list by using the LPUSH (left push) and RPUSH (right push) commands. You can retrieve items from either end of the list by using the LPOP and RPOP commands.  You can also return a set of elements by using the LRANGE and RRANGE commands. The code snippets below show how you can perform these operations by using the StackExchange library. This code uses the BlogPost type from the previous examples. As a blog post is read by a user, the title of the blog post is pushed onto a list associated with the key "blog:recent_Beiträge"in der Redis-Cache mithilfe der IDatabase.ListLeftPushAsync-Methode:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Mehr Blog-Posts gelesen werden, werden ihre Titel auf derselben Liste abgelegt. Die Liste trägt durch die Sequenz, in der sie hinzugefügt haben; die meisten vor kurzem gelesen, Blog-Posts sind Links am Ende der Liste (wenn die gleichen Blog-Post, dass mehr als einmal, es haben mehrere Einträge in der Liste gelesen wird). Sie können die Titel der vor kurzem meistgelesene Beiträge anzeigen, mit der IDatabase.ListRange-Methode. Diese Methode nimmt den Schlüssel, der die Liste, einen Startpunkt und Endpunkt enthält. Der folgende Code Ruft die Titel der 10 Blog-Posts (Artikel von 0 bis 9) am äußersten linken Ende der Liste:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Beachten Sie, dass ListRangeAsync keine Elemente aus der Liste entfernt werden; dazu können Sie die Methoden IDatabase.ListLeftPopAsync und IDatabase.ListRightPopAsync.

Damit die Liste nicht unbegrenzt wachsen, können Sie in regelmäßigen Abständen Elemente zu Keulen durch Verkürzen der List. Der Codeausschnitt unten, entfernt alle aber die 5 ganz links Elemente aus der Liste:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Umsetzung einer Rangliste

Standardmäßig werden die Elemente in einem Satz nicht in einer bestimmten Reihenfolge gehalten. Sie können einen geordneten Satz erstellen, mithilfe des Befehls ZADD (die IDatabase.SortedSetAdd-Methode in der StackExchange-Bibliothek). Die Elemente werden geordnet, mit einem numerischen Wert aufgerufen, eine Partitur, die als Parameter für den Befehl zur Verfügung gestellt. Der folgende Codeausschnitt hinzugefügt eine geordnete Liste den Titel von einem Blog-Eintrag. Im Beispiel hat jeder Blog-Eintrag auch ein Ergebnis-Feld, das Ranking von den Blog-Eintrag enthält.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Sie können den Blog-Post-Titel und Resultate zählen aufsteigend mit der IDatabase.SortedSetRangeByRankWithScores-Methode aufrufen:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURBLAU. HINWEIS] Die StackExchange-Bibliothek enthält auch die IDatabase.SortedSetRangeByRankAsync-Methode gibt die Daten in der Reihenfolge der Punktzahl, sondern die Resultate nicht zurück.

Sie können auch Abrufen von Elementen in absteigender Reihenfolge von Partituren und begrenzen die Anzahl der Elemente, die durch die Bereitstellung zusätzlicher Parameter der IDatabase.SortedSetRangeByRankWithScoresAsync-Methode zurückgegeben. Das nächste Beispiel zeigt die Titel und Resultate von der Top 10 Rang Blogbeiträge:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

Das nächste Beispiel verwendet die IDatabase.SortedSetRangeByScoreWithScoresAsync-Methode, die Sie verwenden können, um einzuschränken an diejenigen, die in einer bestimmten Punktzahl fallen zurückgeschickten Artikel Sortiment:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Messaging mit Kanälen

Abgesehen von fungiert als Datencache, enthält einen Redis-Server messaging über einen Hochleistungs-Verleger/Abonnent-Mechanismus. Client-Anwendungen können einen Kanal abonnieren, und andere Anwendungen oder Dienste können Nachrichten an den Kanal veröffentlichen. Abonnieren von Anwendungen erhalten Sie diese Nachrichten und verarbeiten Sie können.

Um Kanal abonnieren, bietet Redis SUBSCRIBE-Befehl. Dieser Befehl erwartet den Namen des einen oder mehrere Kanäle, auf denen die Anwendung Nachrichten akzeptieren. Die StackExchange-Bibliothek enthält die ISubscription-Schnittstelle, die ermöglicht eine .NET Framework Anwendung abonnieren und auf Kanäle veröffentlichen. Sie erstellen ein ISubscription-Objekt mithilfe der GetSubscriber-Methode der Verbindung mit der Redis-Server und dann auf Nachrichten auf einem Kanal mithilfe der SubscribeAsync-Methode dieses Objekts überwachen. Im folgenden Codebeispiel wird veranschaulicht, wie ein Channel mit dem Namen "Nachrichten: Informationen rund um" abonnieren:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Der erste Parameter an die Subscribe-Methode ist der Name des Kanals. Dieser Name entspricht den gleichen Konventionen wie Schlüssel im Cache und kann Binärdaten enthalten, obwohl es ratsam ist, die relativ kurze, aussagekräftige Zeichenfolgen verwenden, um gute Performance und Wartbarkeit zu gewährleisten. Beachten Sie auch, dass der Namespace von Sendern getrennt von von Schlüssel, verwendet ist, so können Sie Kanäle und Schlüssel, die den gleichen Namen haben, obwohl dies Ihr Anwendungscode möglicherweise schwieriger zu verwalten.

Der zweite Parameter ist ein Action-Delegaten. Dieser Delegat wird asynchron ausgeführt, sobald eine neue Meldung auf dem Kanal angezeigt wird. Dieses Beispiel zeigt einfach die Meldung auf der Konsole (die Nachricht wird den Titel des Blog-Eintrag enthalten).

Um einen Kanal zu veröffentlichen, kann eine Anwendung mit dem Befehl veröffentlichen Redis verwenden. Die StackExchange-Bibliothek enthält die IServer.PublishAsync-Methode, um diesen Vorgang auszuführen. Der nächste Codeausschnitt zeigt, wie eine Nachricht an den Kanal "Nachrichten: Informationen rund um" veröffentlichen:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Es gibt einige Punkte, die Sie über die Publish/subscribe-Mechanismus verstehen sollten:

- Können mehrere Abonnenten auf denselben Kanal abonnieren und sie erhalten alle die Nachrichten in den Channel veröffentlicht.
- Abonnenten erhalten nur Nachrichten, die veröffentlicht worden sind, nachdem sie abonniert haben. Kanäle werden nicht gepuffert, und einmal eine Nachricht veröffentlicht wird die Redis-Infrastruktur schiebt die Nachricht an jeden Abonnenten und wieder entfernt.
- Standardmäßig werden Nachrichten von Teilnehmern in der Reihenfolge empfangen, in denen sie gesendet werden. In einem hochaktiven System mit einer großen Anzahl
  von Nachrichten und viele Abonnenten und Verlage kann garantierter sequenzieller Zustellung von Nachrichten Systemleistung verlangsamen. If
  jede Nachricht ist unabhängig und die Reihenfolge ist unerheblich, Sie können parallele Verarbeitung aktivieren, indem die Redis-System, die helfen kann
  Verbesserung der Reaktionsfähigkeit. Sie können dies in einem StackExchange-Client erreichen, durch Festlegen der PreserveAsyncOrder für die Verbindung von
  der Abonnent auf False:
  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Ähnliche Muster und Anleitungen

Das folgende Muster kann auch relevant für Ihr Szenario sein, bei der Umsetzung in Ihren Anwendungen Zwischenspeichern:

- [Cache-Aside-Muster](http://msdn.microsoft.com/library/dn589799.aspx): Dieses Muster beschreibt, wie Daten auf Abruf in einem Cache aus einem Datenspeicher geladen. Dieses Muster hilft auch, um die Konsistenz zwischen der im Cache gespeicherten Daten und die Daten in den ursprünglichen Datenspeicher.
- Die [Splitter-Muster](http://msdn.microsoft.com/library/dn589797.aspx) enthält Informationen zum Implementieren von horizontalen Partitionierung zur Verbesserung der Skalierbarkeit beim Speichern und Zugriff auf große Datenmengen.

## Weitere Informationen

- Die [MemoryCache-Klasse](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) Seite auf der Microsoft-Website.
- Die [Microsoft Azure-Cache](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) Seite auf der Microsoft-Website.
- Die [Die Azure-Cache-Angebot ist das richtige für mich?](http://msdn.microsoft.com/library/azure/dn766201.aspx) Seite auf der Microsoft-Website.
- Die [Konfigurationsmodell](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) Seite auf der Microsoft-Website.
- Die [Aufgabenbasierte asynchrone Muster](http://msdn.microsoft.com/library/hh873175.aspx) Seite auf der Microsoft-Website.
- Die [Rohrleitungen und Multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) Seite auf dem StackExchange.Redis GitHub-Repo.
- Die [REDIS Persistenz](http://redis.io/topics/persistence) Seite der Redis-Website.
- Die [Replikation-Seite](http://redis.io/topics/replication) auf der Redis-Website.
- Die [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) Seite der Redis-Website.
- Die [Partitionierung: wie Daten zwischen mehreren Redis-Instanzen geteilt](http://redis.io/topics/partitioning) Seite der Redis-Website.
- Die Seite [Redis verwenden als LRU-Cache](http://redis.io/topics/lru-cache) auf der Redis-Website.
- Die [Transaktionsseite](http://redis.io/topics/transactions) auf der Redis-Website.
- Die [Redis Sicherheit](http://redis.io/topics/security) Seite der Redis-Website.
- Die Seite [Runde auf himmelblau Redis-Cache](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) auf dem Azure Blog.
- Die Seite [Redis auf einem CentOS Linux VM ausgeführt](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) in Azure auf der Microsoft-Website.
- Die [ASP.NET Sitzungszustandsanbieters für Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn690522.aspx) Seite auf der Microsoft-Website.
- Die [ASP.NET Output Cache Provider für Azure Redis Cache](http://msdn.microsoft.com/library/azure/dn798898.aspx) Seite auf der Microsoft-Website.
- Die Seite [Entwickeln Sie für himmelblau Redis-Cache](http://msdn.microsoft.com/library/azure/dn690520.aspx) am Standort himmelblau.
- Die Seite [Eine Einführung in die Datentypen Redis und Abstraktionen](http://redis.io/topics/data-types-intro) auf der Redis-Website.
- Die [Grundlegende Verwendung](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) Seite auf der Website StackExchange.Redis.
- Die [Transaktionen in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) Seite auf dem StackExchange.Redis-Repo.
- Die [Data-Partitionierungs-Handbuch](http://msdn.microsoft.com/library/dn589795.aspx) auf der Microsoft-Website.
