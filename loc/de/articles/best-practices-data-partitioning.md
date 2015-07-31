<properties
   pageTitle="Data partitioning guidance | Microsoft Azure"
   description="Guidance upon how to separate partitions to be managed and accessed separately."
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

# Daten-Partitionierungs-Anleitung

![](media/best-practices-data-partitioning/pnp-logo.png)

## Übersicht

In vielen großen Lösungen ist Daten in separaten Partitionen aufgeteilt, die separat abgerufen und verwaltet werden können. Die Partitionierungsstrategie muss sorgfältig gewählt werden, um die Vorteile zu maximieren bei gleichzeitiger Minimierung der negative Auswirkungen. Partitionierung kann dazu beitragen, Konflikte reduzieren verbessern die Skalierbarkeit und Leistung optimieren. Ein Nebeneffekt der Partitionierung ist, die auch einen Mechanismus für die Aufteilung der Daten durch das Muster der Nutzung bereitstellen können. Sie könnten ältere, nicht mehr aktiv (kalt) auf billigere Datenspeicher archivieren.

## Warum partitionieren Daten?

Die meisten Cloud-Anwendungen und Dienste speichern und Abrufen von Daten im Rahmen ihrer Tätigkeit. Das Design der Datenspeicher, die eine Anwendung verwendet haben die Leistung, Durchsatz und Skalierbarkeit eines Systems erheblich sind. Eine Technik, die häufig in Großanlagen angewendet wird ist es, die Daten in getrennten Partitionen unterteilen.

> Der Begriff _Partitionierung_ verwendet in dieser Anleitung bezieht sich auf den Prozess der physisch getrennten Datenspeichern Daten aufteilen. Dies ist nicht identisch mit dem SQL Server-Tabelle partitioniert wird, das ist ein anderes Konzept.

Partitionieren von Daten bieten eine Reihe von Vorteilen. Beispielsweise kann es zur angewendet werden:

- **Zur Verbesserung der Skalierbarkeit**. Aufstockung einer einzelnen Datenbank-System gelangen schließlich ein physischer Hardware-Limit. Division für mehrere Partitionen, von die jede auf einem separaten Server gehostet wird, kann das System fast unbegrenzt skalieren.
- **Verbessern der Leistung**. Datenzugriffsvorgänge auf jeder Partition erfolgt über eine kleinere Datenmenge. Sofern die Daten in geeigneter Weise partitioniert ist, ist dies sehr viel effizienter. Operationen, die mehr als eine Partition beeinflussen können parallel ausgeführt werden. Jede Partition kann in der Nähe der Anwendung anzubringen, die um Netzwerklatenz zu minimieren verwendet.
- **Verbesserung der Verfügbarkeit**. Trennung von Daten über mehrere Server hinweg vermeidet eine Fehlerquelle. Wenn ein Server ausfällt, oder wird derzeit geplante Wartungsarbeiten, nur die Daten in diesem Partition nicht verfügbar ist. Operationen auf anderen Partitionen können fortgesetzt werden. Erhöhung der Anzahl der Partitionen reduziert den relativen Einfluss von einem einzigen Serverausfall durch Reduzierung den Prozentsatz der Daten, die nicht verfügbar sein werden. Jede Partition replizieren kann die Chance auf eine einzelne Partition Fehler betreffen Operationen weiter reduziert werden. Es ermöglicht auch die Trennung von kritischen Daten, die kontinuierlich sein muss und hochverfügbare aus niedrigen Wertdaten (z. B. Log-Dateien), die hat geringere Verfügbarkeitsanforderungen an die.
- **Verbesserung der Sicherheit**. Je nach Art der Daten und wie sie partitioniert wird kann es möglich, sensible und nicht sensible Daten in verschiedene Partitionen und somit verschiedenen Servern oder Daten speichern zu trennen sein. Sicherheit kann dann speziell für die sensiblen Daten optimiert werden.
- **Die betrieblichen Flexibilität**. Partitionierung, bietet viele Möglichkeiten für feines tuning Operationen, administrative Effizienz zu maximieren und Kosten zu minimieren. Einige Beispiele sind verschiedene Strategien für die Verwaltung, Überwachung, Sicherung und Wiederherstellung zu definieren, und andere administrativen Aufgaben basierend auf die Bedeutung der Daten in jeder Partition.
- **Entsprechen Sie den Datenspeicher mit dem Muster des Gebrauches**. Partitionierung kann jede Partition auf eine andere Art von Datenspeicher, auf der Grundlage von Kosten und die eingebauten Funktionen, Daten Angebote speichern bereitgestellt werden. Beispielsweise könnte große binäre Daten in einem Blob-Datenspeicher gespeichert werden, während mehr strukturierte Daten in einer Dokumentendatenbank stattfinden konnte. Weitere Informationen finden Sie unter [Erstellung einer vielsprachigen Lösung](https://msdn.microsoft.com/library/dn313279.aspx) in den Mustern & Practices-Leitfaden [Datenzugriff für hochgradig skalierbaren Lösungen: mit SQL, NoSQL und Polyglot Persistenz](https://msdn.microsoft.com/library/dn271399.aspx) auf der Microsoft-Website.

Einige Systeme implementieren nicht partitionieren, da es einen Overhead eher als Vorteil angesehen wird. Häufige Gründe für diese Begründung enthalten:

- Viele Daten-Storage-Systeme unterstützen keine Verknüpfungen auf Partitionen, und es kann schwierig sein, in einem partitionierten System referenzielle Integrität zu erhalten. Es ist häufig erforderlich, die Verknüpfungen zu implementieren und Integritätsprüfungen im Anwendungscode (in der Partitionierungs-Schicht), was zusätzliche i/o und die Anwendungskomplexität führen kann.
- Wartung von Partitionen ist nicht immer einfache Aufgabe. In einem System, wo die Daten flüchtig ist, müssen Sie ausgleichen, Partitionen, in regelmäßigen Abständen, um Konflikte und Hot-Spots zu reduzieren.
- Einige gängige Tools funktionieren natürlich nicht mit partitionierten Daten.

## Entwerfen von Partitionen

Daten können auf unterschiedliche Weise partitioniert werden: horizontal, vertikal oder funktional. Die Strategie, die Sie wählen, hängt von der Grund zum Partitionieren von Daten und die Anforderungen der Anwendungen und Dienste, die die Daten genutzt werden.

> [AZURBLAU. HINWEIS] In dieser Anleitung beschriebenen Partitionierungsschemas werden in einer Weise erklärt, die unabhängig von der zugrunde liegenden Daten-Storage-Technologie. Sie können auf viele Arten von Datenspeichern, einschließlich relationale und NoSQL Datenbanken angewendet werden.

### Partitionierungsstrategien

Die drei typischen Strategien zum Partitionieren von Daten sind:

- **Horizontale Partitionierung** (häufig genannt _Splitter_). In dieser Strategie jede Partition ist ein Datenspeicher aus eigenem Recht, aber alle Partitionen haben das gleiche Schema. Jede Partition ist bekannt als ein _Scherbe_ und hält eine bestimmte Teilmenge der Daten, z. B. alle Aufträge für eine bestimmte Gruppe von Kunden in einer e-Commerce-Anwendung.
- **Die vertikale Partitionierung**. In dieser Strategie hält jede Partition eine Teilmenge der Felder für Elemente im Datenspeicher. Die Felder gliedern sich nach ihrer Muster des Gebrauches, wie häufig verwendete Felder in eine vertikale Partition und die weniger häufig verwendete Felder in einem anderen platzieren.
- **Funktionale Partitionierung**. In dieser Strategie werden die Daten aggregiert nach wie es im System von jedem berandeten Kontext verwendet wird. Zum Beispiel ein e-Commerce System implementiert Business-Funktionen für die Rechnungsstellung zu trennen, und Verwalten von Produktinventar könnte Rechnungsdaten speichern, in einer Partition und Produkt-Bestandsdaten in einem anderen.

Es ist wichtig zu beachten, dass die hier beschriebenen drei Strategien kombiniert werden können.  Sie sind nicht gegenseitig aus und berücksichtigen Sie sie alle beim Entwerfen eines Partitionierungsschemas. Beispielsweise könnten Sie Daten in Fragmente aufteilen und vertikale Partitionierung verwenden, um die Daten in jeder Splitter weiter unterteilen. Ebenso können die Daten in eine funktionale Partition in Fragmente aufgeteilt werden (die auch vertikal partitioniert werden kann).

Allerdings können die unterschiedlichen Anforderungen der einzelnen Strategien Potenzieren einer Zahl widersprüchliche Probleme, die Sie bewerten müssen und Balance beim eines Partitionierungsschemas zu entwerfen, das die gesamte Datenverarbeitung Leistungsziele für Ihr System erfüllt. In den folgenden Abschnitten Suche aller Strategien im Detail.

### Horizontale Partitionierung (Sharding)

Abbildung 1 zeigt eine Übersicht über horizontale Partitionierung oder Splitter. In diesem Beispiel gliedert sich Produkt-Bestandsdaten in Fragmente, die basierend auf den Product Key. Jeder Splitter enthält die Daten für einen zusammenhängenden Bereich von Scherbe Tasten (A-G und H-Z), alphabetisch.

![](media/best-practices-data-partitioning/DataPartitioning01.png)

_Abbildung 1. -Horizontal (Sharding) Daten anhand eines Schlüssels Partition-Partitionierung_

Splitter können Sie die Auslastung über mehrere Computer zu verteilen; Verringerung der Konflikte und Verbesserung der Leistung. Sie können das System heraus skalieren, durch Hinzufügen weiterer Fragmente, die auf zusätzlichen Servern.

The most important factor when implementing this partitioning strategy is the choice of sharding key. It can be difficult to change the key after the system is in operation. The key must ensure that data is partitioned so that the workload is as even as possible across the shards. Note that different shards do not have to contain similar volumes of data, rather the important consideration is to balance the number of requests; some shards may be very large but each item is the subject of a low number of access operations, while other shards may be smaller but each item is accessed much more frequently. It is also important to ensure that a single shard does not exceed the scale limits (in terms of capacity and processing resources) of the data store being used to host that shard.

Die Splitter-Regelung sollten auch vermeiden, Erstellen von Hotspots (oder heißen Partitionen) Performance und Verfügbarkeit beeinflussen können. Mithilfe eines Hashs einer Kunden-ID anstelle von den ersten Buchstaben des Namens des Kunden wird beispielsweise die unausgewogene Verteilung verhindern, die durch häufige und weniger häufige Anfangsbuchstaben entstehen würde. Dies ist eine typische Technik, die hilft, um die Daten auf Partitionen gleichmäßiger zu verteilen.

The sharding key you choose should minimize any future requirements to split large shards into smaller pieces, coalesce small shards into larger partitions, or change the schema that describes the data stored in a set of partitions. These operations can be very time consuming, and may require taking one or more shards offline while they are performed. If shards are replicated, it may be possible to keep some of the replicas online while others are split, merged, or reconfigured, but the system may need to limit the operations that can be performed on the data in these shards while the reconfiguration is taking place. For example, the data in the replicas could be marked as read-only to limit the scope of any inconsistences that could otherwise occur while shards are being restructured.

> Für detailliertere Information und Beratung über viele dieser Überlegungen und bewährte Techniken entwerfen Daten speichert diese implementieren Horizontale Partitionierung, finden Sie unter der [Splitter-Muster](http://aka.ms/Sharding-Pattern)

### Die vertikale Partitionierung

Die häufigste Verwendung für vertikale Partitionierung ist der i/o reduzieren und Leistung Kosten im Zusammenhang mit Abrufen der Elemente, die am häufigsten zugegriffen werden. Abbildung 2 zeigt ein Beispiel für die vertikale Partitionierung, auf dem verschiedene Eigenschaften für jedes Datenelement in verschiedenen Partitionen liegen im Überblick; Name, Beschreibung und Preisinformationen für Produkte sind häufiger als das Volumen im Lager oder das Datum der letzten bestellten abgerufen.

![](media/best-practices-data-partitioning/DataPartitioning02.png)

_Abbildung 2. -Vertikal Partitionieren von Daten durch die Muster des Gebrauches_

In diesem Beispiel fragt die Anwendung regelmäßig der Produktname, Beschreibung und Preis zusammen bei der Anzeige von Details der Produkte an Kunden. Der Lagerstand und Datum, wann das Produkt vom Hersteller zuletzt bestellt wurde, werden in einer separaten Partition gehalten, weil diese beiden Elemente häufig zusammen verwendet werden. Diese Partitionierungsschema hat den zusätzlichen Vorteil, dass die relativ langsamen Daten (Produktname, Beschreibung und Preis) von der dynamischen Daten (Lagerstand und Datum der letzten bestellten) getrennt sind. Eine Anwendung kann finden es vorteilhaft, die Ladenhüter-Daten im Arbeitsspeicher zwischengespeichert, wenn es häufig zugegriffen wird.

Ein weiteres typisches Szenario für diese Partitionierungsstrategie ist die Sicherheit vertraulicher Daten zu maximieren. Z. B. durch Speichern von Kreditkartennummern und die entsprechende Sicherheit Überprüfung Kartennummern in getrennten Partitionen.

Die vertikale Partitionierung kann auch den gleichzeitigen Zugriff benötigt, um die Daten reduzieren.

> Vertikale Partitionierung arbeitet auf der Entitätsebene innerhalb eines Datenspeichers, teilweise Normalisieren einer Entität um es aus zu brechen ein _Breite_ Element, das eine Reihe von _schmale_ Elemente. Es eignet sich ideal für spaltenorientiert Datenspeicher wie HBase und Cassandra. Wenn die Daten in einer Auflistung der Spalten nicht zu ändern ist, können Sie auch erwägen Spalte Läden in SQL Server.

### Funktionale Partitionierung

Bei Systemen, wo es einen beschränkten Kontext für jede unterschiedliche Business-Bereich oder Service in der Anwendung identifizieren kann, bietet funktionale Partitionierung eine Technik zur Verbesserung der Isolation und Daten Zugriff-Leistung. Eine andere häufige Verwendung der funktionalen Partitionierung ist Lese-/ Schreib-Daten von schreibgeschützte Daten für Berichtszwecke verwendet zu trennen. Abbildung 3 zeigt eine Übersicht über die funktionelle Partitionierung in der Bestandsdaten von Kundendaten getrennt.

![](media/best-practices-data-partitioning/DataPartitioning03.png)

_Abbildung 3. -Funktionell Partitionieren von Daten von berandeten Kontext oder Sub-Domain_

Diese Partitionierungsstrategie können um Daten Zugriff Konflikte in verschiedenen Teilen des Systems zu verringern.

## Entwerfen von Partitionen für Skalierbarkeit

Es ist wichtig zu prüfen, Größe und Auslastung für jede Partition und balancieren sie damit die Daten verteilt werden, um maximale Skalierbarkeit zu erzielen. Allerdings müssen Sie auch die Daten partitionieren, sodass die Skalierungsgrenzen einer einzelnen Partition-Filiale nicht überschritten wird.

Gehen Sie folgendermaßen vor, wenn die Partitionen für Skalierbarkeit zu entwerfen:

1. Analysieren die Anwendung, die Daten-Zugriffsmuster, z. B. Größe des von jeder Abfrage zurückgegebenen Resultsets zu verstehen, die Häufigkeit von Access, die Eigenlatenz und der Serverseite Verarbeitungsanforderungen zu berechnen. In vielen Fällen werden einige Hauptelemente die meisten Verarbeitungsressourcen fordern.
2. Auf der Grundlage der Analysis bestimmen die aktuelle und zukünftige Skalierbarkeit Ziele wie Datengröße und Arbeitsauslastung und Verteilung der Daten über die Partitionen auf das Skalierbarkeit-Ziel zu erreichen. In der horizontalen Partitionierung Strategie ist die Wahl des geeigneten Splitter-Schlüssels wichtig, um sicherzustellen, dass die Verteilung selbst ist. Weitere Informationen finden Sie unter der [Splitter-Muster](http://aka.ms/Sharding-Pattern).
3. Make sure that the resources available to each partition are sufficient to handle the scalability requirements in terms of data size and throughput. For example, the node hosting a partition might impose a hard limit on the amount of storage space, processing power, or network bandwidth that it provides. If the data storage and processing requirements are likely to exceed these limits it may be necessary to refine your partitioning strategy or split data out further. For example, one scalability approach might be to separate logging data from the core application features by using separate data stores to prevent the total data storage requirements exceeding the scaling limit of the node. If the total number of data stores exceeds the node limit, it may be necessary to use separate storage nodes.
4. Überwachung des Systems unter Verwendung zu überprüfen, ob die Daten verteilt werden, wie erwartet, und dass die Partitionen die Belastung verarbeiten können ihnen auferlegt. Es könnte möglich sein, dass die Nutzung nicht übereinstimmt, die durch die Analyse, dass es möglich ist, die Partitionen neu auszutarieren erwartet. Gelingt das, nicht ist es möglicherweise erforderlich, redesign einige Teile des Systems, das Gleichgewicht zu erhalten, das erforderlich ist.

Beachten Sie, dass einige Cloud-Umgebungen Zuteilung von Ressourcen in Bezug auf die Infrastruktur Grenzen, und Sie sicherstellen sollten, dass die Grenzen der Grenzen der eigenen ausgewählten genug Platz für alle geplanten Wachstum der Datenmenge in Bezug auf die Datenspeicherung, Rechenleistung und Bandbreite bieten. Beispielsweise wenn Sie Azure-Tabelle-Speicher verwenden, einen geschäftigen Scherbe erfordern mehr Ressourcen als eine einzelne Partition-Anforderungen zur Verfügung stehen (gibt es ein Limit, das Volumen der Anfragen, die in einem bestimmten Zeitraum durch eine einzelne Partition behandelt werden können — finden Sie auf die Seite [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft-Website für weitere Details). In diesem Fall müssen die Splitter neu partitioniert werden, um die Last zu verteilen. Überschreitet die Gesamtgröße oder Durchsatz dieser Tabellen Kapazität eines Speicher-Konto, ist es möglicherweise erforderlich, um zusätzlichen Speicher-Konten erstellen und die Tabellen auf diese Konten verteilt. Übersteigt die Anzahl der Konten der Lagerhaltung die Anzahl der Konten, die für ein Abonnement zur Verfügung stehen, kann es dann notwendig, mehrere Abonnements zu verwenden.

## Entwerfen von Partitionen für Abfrageleistung

Abfrageleistung kann oft mit kleineren Datenmengen und parallele Abfrageausführung gefördert werden. Jede Partition sollte einen kleinen Anteil der gesamten Datenmenge enthalten, und diese Reduktion des Volumens kann die Abfrageleistung verbessern. Partitionierung ist jedoch keine Alternative zum Entwerfen und Konfigurieren einer Datenbank entsprechend. Beispielsweise stellen Sie sicher, dass Sie die erforderlichen Indizes haben bei Verwendung eine relationale Datenbank.

Gehen Sie folgendermaßen vor, wenn die Partitionen für die Abfrageleistung zu entwerfen:

1. Die Anwendungsanforderungen und Leistung zu prüfen:
	- Verwenden Sie die Geschäftsanforderungen, kritische Fragen zu bestimmen, die immer schnell durchführen müssen.
	- Überwachen Sie das System um weitere Fragen zu identifizieren, die nur langsam durchgeführt.
	- Fest, welche Abfragen am häufigsten durchgeführt werden. Eine einzelne Instanz jeder Abfrage möglicherweise minimalen Kosten, jedoch der kumulative Verbrauch von Ressourcen könnten erheblich sein. Es kann vorteilhaft für die Daten, die durch diese Abfragen in eine eigene Partition oder sogar einen Cache abgerufen zu trennen sein.
2. Partitionieren Sie die Daten, die langsame Leistung verursacht. Stellen Sie sicher, dass Sie:
	- Einschränken Sie die Größe der einzelnen Partitionen so, dass die Abfrage Reaktionszeit innerhalb von Target.
	- Entwerfen Sie den Splitter-Schlüssel in einer Weise, dass die Anwendung die Partition leicht finden kann, wenn Sie Horizontale Partitionierung implementieren. Dies verhindert, dass die Abfrage benötigen jede Partition abgetastet.
	- Sollten Sie den Speicherort der einer Partition auf die Leistung von Abfragen. Wenn möglich versuchen Sie, Daten in Partitionen, die geographisch nahe dem Anwendungen und Benutzer, die darauf zugreifen.
3. Wenn eine Entität Durchsatz und Abfrage-Performance-Anforderungen hat, funktionale Partitionierung basierend auf diese Entität verwendet. Ist dies noch nicht in der Lage, die Anforderungen zu erfüllen, gelten Sie die horizontale Partitionierung sowie. In den meisten Fällen genügt ein einziges Partitionierungsstrategie, aber in einigen Fällen ist es effizienter, beide Strategien zu kombinieren.
4. Erwägen Sie asynchrone Abfragen, die über Partitionen zur Verbesserung der Leistung parallel verlaufen.

## Entwerfen von Partitionen für Verfügbarkeit

Partitionieren von Daten kann verbessert die Verfügbarkeit von Anwendungen sicherstellen, dass der gesamte Datenbestand keine Fehlerquelle dar und einzelne Untergruppen des Dataset unabhängig verwaltet werden können. Replizieren von Partitionen, die wichtige Daten enthalten, kann auch Verfügbarkeit verbessern.

Entwerfen und Implementieren von Partitionen, berücksichtigen Sie die folgenden Faktoren, die Verfügbarkeit beeinflussen:

- Wie wichtig sind die Daten für den Geschäftsbetrieb. Einige Daten können kritische Geschäftsinformationen wie Rechnungsdetails oder Bank-Transaktionen umfassen. Andere Daten möglicherweise einfach weniger kritisch betrieblichen Daten, z. B. Protokolldateien, Leistung Spuren und So weiter. Nach dem jeweiligen Datentyp ermitteln, zu berücksichtigen:
	- Speichern von wichtigen Daten in hoch verfügbaren Partitionen mit einem entsprechenden Backup-Plan.
	- Aufbau getrennte Verwaltung und Überwachung von Mechanismen oder Verfahren für die verschiedenen System jedes Dataset. Ort-Daten, die das gleiche Maß an Wichtigkeit in der gleichen Partition hat, so dass es kann gesichert, zusammen mit einer entsprechenden Frequenz. Partitionen mit Daten für Banktransaktionen müssen beispielsweise möglicherweise häufiger als holding Protokollierung oder Ablaufverfolgungsinformationen Partitionen gesichert werden.
- Wie einzelne Partitionen verwaltet werden können. Entwerfen von Partitionen, unabhängiges Management und Wartung zu unterstützen, bietet mehrere Vorteile. Zum Beispiel:
	- Wenn eine Partition ausfällt, kann es selbständig wiederhergestellt werden, ohne Instanzen von Anwendungen, die Daten in anderen Partitionen zugreifen.
	- Partitionieren von Daten nach geographischen Bereichen kann geplanten Wartungsaufgaben zu Spitzenzeiten für jeden Standort auftreten. Sicherstellen Sie, dass Partitionen nicht zu groß, um zu verhindern, dass geplante Wartungsarbeiten während dieses Zeitraums abgeschlossen werden.
- Ob kritische Daten auf Partitionen replizieren. Diese Strategie kann Verfügbarkeit und Leistung, verbessern, obwohl es auch Konsistenz Probleme führen kann. Es braucht Zeit für Änderungen an Daten in einer Partition mit jedes Replikatgruppe synchronisiert werden, und während dieser Zeit werden verschiedene Partitionen unterschiedliche Datenwerte enthalten.

## Probleme und Überlegungen

Mit Partitionierung erhöht die Komplexität der Gestaltung und Entwicklung des Systems. Es ist wichtig zu berücksichtigen, als ein wesentlicher Teil des Systementwurfs Partitionierung, selbst wenn das System zunächst nur eine einzelne Partition enthält. Adressierung der Partitionierung mit zunehmender Beiwerk beim Systemstart zu Leistung und Skalierbarkeitsprobleme leiden nur Komplexität als Sie wahrscheinlich jetzt haben ein live-System zu pflegen. Aktualisierung des Systems um Partitionierung in diesem Umfeld zu integrieren erfordert nicht nur die Datenzugriffslogik ändern, es kann auch beinhalten, Migration von bestehenden großen Datenmengen es auf Partitionen verteilt oft, während Benutzer erwarten weiterhin das System verwenden können.

In einigen Fällen ist Partitionierung nicht als wichtig, weil das Anfangsdataset klein ist und leicht von einem einzelnen Server behandelt werden kann. Das mag zutreffen, in ein System, die voraussichtlich nicht über seine ursprüngliche Größe skalieren, aber viele kommerzielle Systeme müssen, als die Zahl der Benutzer steigt erweitern zu können. Diese Erweiterung wird in der Regel durch einen Anstieg des Datenvolumens begleitet. Sie sollten auch wissen, dass die Partitionierung nicht immer eine Funktion der großen Datenspeicher ist. Z. B. möglicherweise ein kleinen Datenspeicher stark von Hunderten von gleichzeitigen Clients zugegriffen werden. Partitionieren von Daten in dieser Situation kann dazu beitragen, Konflikte reduziert und den Durchsatz verbessern.

Beim Entwerfen eines Partitionierungsschemas Daten, sollten Sie folgende Punkte beachten:

- Wo möglich, zusammenhalten Sie Daten für die häufigsten Datenbankoperationen in jeder Partition Kreuz-Partitionsdaten Zugriffsoperationen zu minimieren. Abfragen über Partitionen kann mehr Zeit in Anspruch als nur innerhalb einer einzelnen Partition Abfragen, aber optimieren Partitionen für eine Gruppe von Abfragen beeinträchtigen könnte andere Mengen von Abfragen. Um die Abfragezeit auf Partitionen zu minimieren, wo dies nicht vermieden werden kann, parallele Abfragen über die Partitionen ausführen und die Ergebnisse innerhalb der Anwendung zu aggregieren. Jedoch kann dieser Ansatz nicht in einigen Fällen, z. B. Wann ist es notwendig ein Ergebnis aus einer Abfrage abrufen und verwenden Sie diese in der nächsten Abfrage möglich sein.
- Wenn Abfragen nutzen relativ statischen Verweis Daten wie Postleitzahl Tabellen oder Produktlisten, betrachten Sie diese Daten in alle Partitionen reduzieren die Notwendigkeit für ein separates Suchvorgang in anderen Partition repliziert. Dieser Ansatz kann auch verringern die Wahrscheinlichkeit, dass die Referenzdaten, die immer ein "heißer" Dataset, die der stark befahrenen aus im gesamten System, wird, es zwar gegen Aufpreis Synchronisieren von Änderungen, die auftreten, auf diese Referenzdaten zugeordnet.
- Wo möglich, Minimierung Anforderungen für referenzielle Integrität in vertikaler und funktionale Partitionen. Bei diesen Systemen ist die Anwendung selbst verantwortlich für die Erhaltung der referenziellen Integrität über Partitionen, wenn Daten aktualisiert und verbraucht ist. Abfragen, die Daten für mehrere Partitionen verknüpfen müssen laufen langsamer als Abfragen, die Daten nur innerhalb der gleichen Partition zu verknüpfen, weil die Anwendung in der Regel aufeinander folgenden Abfragen basierend auf eine Taste und dann auf einen Fremdschlüssel durchführen muss. Stattdessen sollten Sie replizieren oder de normalisieren die relevanten Daten. Um die Abfragezeit zu minimieren, in denen Verknüpfungen Kreuz-Partition erforderlich sind, parallele Abfragen über die Partitionen ausführen und die Daten innerhalb der Anwendung zu verbinden.
- Betrachten Sie die Wirkung, die das Partitionierungsschema auf die Konsistenz der Daten auf Partitionen hätte. Sie sollten beurteilen, ob starke Konsistenz tatsächlich erforderlich ist. Stattdessen ist ein gemeinsames Vorgehen in der Wolke eventuelle Konsistenz zu implementieren. Die Daten in jeder Partition werden separat aktualisiert, und die Anwendungslogik kann Verantwortung dafür, dass die Updates, die alle erfolgreich abgeschlossen – sowie für die Widersprüche, die durch Abfragen von Daten während der Ausführung einer schließlich konsistenten Operation entstehen können. Finden Sie weitere Informationen zum Implementieren von eventuellen Konsistenz die Konsistenz. (#insertlink)
- Betrachten Sie, wie Abfragen werden die richtige Partition zu suchen. Eine Abfrage scannen muss werden alle Partitionen, die erforderlichen Daten dort zu finden eine erhebliche Auswirkungen auf die Leistung, auch wenn Sie mehrere parallele Abfragen verwenden. Abfragen mit der vertikalen und funktionale Partitionierungsstrategien können natürlich die Partitionen angeben. Jedoch kann bei der Verwendung von horizontalen Partitionierung (Sharding) Auffinden eines Elements schwierig sein weil jeder Splitter dasselbe Schema hat. Eine typische Lösung für Splitter ist eine Karte, die zum Nachschlagen des Splitter-Speicherorts für bestimmte Elemente der Daten verwendet werden kann. Diese Karte kann in der Splitter-Logik der Anwendung durchgeführt oder vom Datenspeicher beibehalten, wenn transparente Splitter unterstützt werden.
- Wenn Sie eine horizontale Partitionierung Strategie verwenden, betrachten Sie regelmäßig Neugewichtung der Fragmente um die Daten gleichmäßig zu verteilen, durch Größe und Arbeitsaufwand zu minimieren, Hotspots, Abfrageleistung maximieren und physischen Speichereinschränkungen zu umgehen. Allerdings ist dies eine komplexe Aufgabe, die oft die Verwendung eines benutzerdefinierten Tools oder Prozess erfordert.
- Jede Partition replizieren bietet zusätzlichen Schutz vor Ausfällen. Wenn ein einzelnes Replikat fehlschlägt, können Abfragen in Richtung einer Arbeitskopie gerichtet werden.
- Wenn Sie die physikalischen Grenzen einer Partitionierung Strategie erreicht haben, müssen Sie die Skalierbarkeit auf eine andere Ebene zu erweitern. Beispielsweise wenn die Partitionierung auf Datenbankebene ist kann es bedeuten suchen oder Partitionen in mehreren Datenbanken replizieren. Wenn die Partitionierung bereits auf Datenbankebene ist und körperliche Einschränkungen ein Problem sind, kann es bedeuten, Auffinden oder Replizieren von Partitionen in mehreren hosting-Accounts.
- Vermeiden Sie Transaktionen, die Daten in mehrere Partitionen zugreifen. Einige Daten Shops implementieren Transaktionskonsistenz und Integrität für Vorgänge ändern, Daten, aber nur, wenn es eine einzelne Partition liegt. Wenn Sie die Transaktionsunterstützung für mehrere Partitionen benötigen, müssen Sie wahrscheinlich dies als Teil der Anwendungslogik zu implementieren, da die meisten Partitionierungs-Systeme keine nativen Unterstützung bieten.

Alle Datenspeicher erfordern einige Betriebsmanagement und Überwachungstätigkeit. Die Aufgaben reichen von Laden von Daten, sichern und Wiederherstellen von Daten, Daten reorganisieren und sicherstellen, dass das System korrekt und effizient arbeitet.

Betrachten Sie die folgenden Faktoren, die die Betriebsführung beeinflussen:

- Prüfen Sie, wie Sie implementieren werden, entsprechende Management und operativen Aufgaben, wenn die Daten partitioniert ist, z. B. Backup und Wiederherstellung und Archivierung von Daten, Überwachung des Systems und andere Verwaltungsaufgaben. Pflege Folgerichtigkeit bei Sicherungs-und Wiederherstellungsvorgängen kann beispielsweise eine Herausforderung sein.
- Wie die Daten in mehrere Partitionen geladen und wie neue sein können möglicherweise Daten aus anderen Quellen hinzugefügt werden. Einige Tools und Dienstprogramme unterstützen möglicherweise nicht sharded Datenoperationen wie das Laden von Daten in die richtige Partition, und dies kann auf Verlangen erstellen und erhalten neue Tools und Dienstprogramme.
- Wie werden die Daten archiviert und gelöscht regelmäßig (vielleicht monatlich) übermäßige Wachstum von Partitionen zu verhindern. Es kann zum Umwandeln der Daten entsprechend ein anderes Archiv-Schema erforderlich sein.
- Betrachten Sie einen regelmäßigen Prozess zur Datenintegritätsproblemen z. B. Daten in einer Partition zu suchen, die Informationen in einem anderen verweist ausführen, aber diese Information fehlt. Der Prozess könnte entweder versuchen, diese Probleme automatisch zu beheben oder eine Warnung zu einem Bediener, die Probleme zu beheben, manuell auslösen. Z. B. in einer e-Commerce-Anwendung Bestellinformationen in eine Partition stattfinden könnte aber die Einzelposten, die jede Bestellung darstellen könnte in einem anderen gemacht werden. Der Prozess der Bestellung muss zum Hinzufügen von Daten um Partitionen zu stören. Wenn dieser Prozeß, es nicht Einzelposten für gespeichert werden konnte, die gibt es keine entsprechenden Reihenfolge.

Verschiedene Datenspeicherungstechnologien bieten in der Regel eigene Funktionen zur Partitionierung Unterstützung. Die folgenden Abschnitte fassen die Optionen von Datenspeichern üblicherweise von Azure-Anwendungen implementiert und beschreiben Überlegungen zum Entwerfen von Anwendungen, die diese Funktionen am besten nutzen können.

## Partitionierungsstrategien für Azure SQL-Datenbank

Azure SQL-Datenbank ist eine relationale Datenbank-as-a-Service, der in der Cloud ausgeführt wird. Es basiert auf Microsoft SQL Server. Eine relationale Datenbank teilt Informationen in Tabellen, und jede Tabelle enthält Informationen über Entitäten als eine Reihe von Zeilen. Jede Zeile enthält Spalten, die die Daten für die einzelnen Felder einer Entität. Die [Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336279.aspx) Seite auf der Microsoft-Website enthält ausführliche Dokumentationen zum Erstellen und Verwenden von SQL-Datenbanken.

## Horizontale Partitionierung mit elastischen Skala

Eine einzelne SQL-Datenbank verfügt über eine Begrenzung der Datenmenge, die sie enthalten, und Durchsatz wird eingeschränkt durch architektonische Faktoren und die Anzahl der gleichzeitigen Verbindungen, die es unterstützt. Azure SQL-Datenbank bietet elastische Skala um horizontale Skalierung für eine SQL-Datenbank zu unterstützen. Mit elastischen Skala, Sie können Ihre Daten partitionieren, in Fragmente, die verteilt über mehrere SQL-Datenbanken, und Sie können hinzufügen oder Entfernen von Scherben, da das Volumen der Daten, die Sie behandeln müssen, wächst und schrumpft. Mit elastischen Skala kann auch helfen, um Konflikte zu verringern, durch die Verteilung der Last auf Datenbanken.

> [AZURBLAU. HINWEIS] Elastische Skala ist derzeit in der Vorschau ab Januar 2015. Es ist ein Ersatz für Azure SQL Datenbank Verbände die zurückgezogen werden wird. Azure SQL Datenbank Föderation Altanlagen zu elastischen Skala migriert werden können, mithilfe der [Verbände-Migrationssoftware](https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1). Alternativ können Sie Ihren eigenen Splitter-Mechanismus implementieren, wenn Ihr Szenario nicht natürlich auf die Funktionen von elastischen Skala sich eignet.

Jeder Splitter wird als eine SQL-Datenbank implementiert. Ein Splitter aufnehmen kann mehrere Datasets (genannt ein _shardlet_), und jede Datenbank verwaltet Metadaten, die die Shardlets beschreiben, die es enthält. Ein Shardlet kann ein einzelnes Datenelement, oder es kann sein, dass eine Gruppe von Elementen, die den gleichen Schlüssel Shardlet teilen. Beispielsweise wenn Sie Splitter-Daten in eine mandantenfähige Anwendung sind, der Shardlet-Schlüssel konnte die Mieter-ID sein und alle Daten für ein bestimmtes Mieter würde als Teil des gleichen Shardlet gehalten werden. Daten für andere Mieter würden in verschiedenen Shardlets statt.

Es ist die Programmierung ein Datasets mit einem Shardlet Schlüssel zuordnen. Eine separate SQL-Datenbank fungiert als ein Splitter-Weltkarte-Manager, der eine Liste der Datenbanken (Fragmente) enthält, die das gesamte System zusammen mit Informationen über die Shardlets in jeder Datenbank umfassen. Eine Clientanwendung, die auf Daten zugreift, verbindet zuerst die Splitter-Weltkarte-Manager-Datenbank, eine Kopie des Splitter-Map (Inserat Scherben und Shardlets) erhalten, die es lokal zwischengespeichert. Die Anwendung dann anhand dieser Informationen zum Weiterleiten von Datenanforderungen die entsprechenden Splitter. Diese Funktionalität verbirgt sich hinter einer Reihe von APIs der Azure SQL Datenbank elastische Skala Client Bibliothek, als NuGet Paket verfügbar. Die Seite [Übersicht über die elastische Skala von himmelblau SQL-Datenbank](sql-database-elastic-scale-introduction.md) Microsoft bietet die Website eine umfassendere Einführung in elastischen Skala.

> [AZURBLAU. HINWEIS] Sie können die Splitter-Weltkarte-Manager-Datenbank zur Verringerung der Latenz und bessere Verfügbarkeit replizieren. Wenn Sie die Datenbank mithilfe einer der Prämie implementieren können Preisgestaltung Stufen Sie active Geo-Replikation um kontinuierlich Daten in Datenbanken in verschiedenen Regionen kopieren konfigurieren. Erstellen Sie eine Kopie der Datenbank in den einzelnen Regionen, in denen Benutzer basieren, und konfigurieren Sie die Anwendung zum Herstellen einer Verbindung mit dieser Kopie, die Splitter-Karte zu bekommen.

> Ein alternativer Ansatz ist Azure SQL Data Sync oder eine Azure Data Factory-Pipeline zum Replizieren der Splitter-anzeigen-Manager-Datenbank über die verschiedenen Regionen verwendet. Diese Form der Replikation läuft regelmäßig und ist besser geeignet, wenn die Splitter-Karte selten ändert. Darüber hinaus muss die Splitter-anzeigen-Manager-Datenbank nicht mit eine Premium Preise Ebene erstellt werden.

Elastische Skala sieht zwei Programme für die Zuordnung von Daten zu Shardlets und Speicherung in Scherben:

- Eine Liste der Splitter Karte beschreibt eine Zuordnung zwischen Tastendruck und eine Shardlet. Beispielsweise in einem multi-Tenant-System konnte die Daten für jeden Mieter einen eindeutigen Schlüssel zugeordnet und gespeichert werden in eigenen Shardlet. Zur Gewährleistung von Datenschutz und Isolation (um zu verhindern, dass ein Mieter erschöpfen Speicherressourcen Daten für andere Benutzer verfügbar) konnte jede Shardlet innerhalb seiner eigenen Splitter gezogen werden.

![](media/best-practices-data-partitioning/PointShardlet.png)

_Abbildung 4. -Mit Hilfe einer Liste Splitter Karte zum Mieter separate Fragmente speichern_

- Der Bereich Shard Karte beschreibt eine Zuordnung zwischen einer Reihe von zusammenhängenden Schlüsselwerte und eine Shardlet. Im beschriebenen, als eine Alternative zur Implementierung von dedizierten Shardlets mandantenfähige-Beispiel, können Sie die Daten für eine Reihe von Mietern (jeweils mit ihren eigenen Schlüssel) innerhalb der gleichen Shardlet gruppieren. Diese Regelung ist weniger teuer als die erste (Mieter teilen Daten-Speicher-Ressourcen), aber an der Gefahr des reduziert, Datenschutz und Isolation.

![](media/best-practices-data-partitioning/RangeShardlet.png)

_Abbildung 5. -Mit Hilfe einer Reihe Splitter Karte zum Speichern von Daten für eine Reihe von Mietern in eine Scherbe_

Beachten Sie, dass eine einzelne Scherbe die Daten für mehrere Shardlets enthalten kann. Beispielsweise können Sie die Liste Shardlets zum Speichern von Daten für unterschiedliche nicht zusammenhängende Mieter in die gleichen Splitter. Sie können auch Bereich Shardlets und Liste Shardlets in die gleiche Splitter mischen, obwohl sie durch verschiedene Karten in der Scherbe-Weltkarte-Manager-Datenbank behandelt werden (die Splitter-Weltkarte-Manager-Datenbank kann mehrere Splitter Karten enthalten). Abbildung 6 zeigt dieser Ansatz.

![](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Abbildung 6. -Karten Umsetzung mehrere Splitter_

Das Partitionierungsschema, das Sie implementieren kann haben einen erheblichen Einfluss auf die Leistung Ihres Systems und beeinflussen auch die Rate an der Fragmente haben hinzugefügt oder entfernt werden oder die Daten, die über Scherben neu partitioniert. Bei der Verwendung von elastischen Skala zum Partitionieren von Daten, sollten Sie folgende Punkte beachten:

- Group data that is used together into the same shard and avoid operations that need to access data held in multiple shards. Bear in mind that with Elastic Scale a shard is a SQL database in its own right, and Azure SQL Database does not support cross-database joins; these operations have to be performed on the client-side. Also remember that with Azure SQL Database referential integrity constraints, triggers, and stored procedures in one database cannot reference objects in another, so don't design a system that has dependencies between shards. However, a SQL database can contain tables holding copies of reference data frequently used by queries and other operations, and these tables do not have to belong to any specific shardlet. Replicating this data across shards can help to remove the need to join data that spans databases. Ideally, such data should be static or slow-moving to minimize the replication effort and reduce the chances of it becoming stale.

	> [AZURBLAU. HINWEIS] Obwohl Azure SQL-Datenbank datenbankübergreifende Verknüpfungen nicht unterstützt, können die elastische Scale-API Sie Kreuz-Splitter-Abfragen ausführen, die die Daten in alle Shardlets, die auf einer Scherbe-Karte verweist transparent durchlaufen kann. Die elastische Skala API Pausen Kreuz-Splitter fragt nach unten in eine Reihe einzelner Abfragen (eine für jede Datenbank) und führt dann die Ergebnisse zusammen. Weitere Informationen finden Sie unter der [Multi-Splitter-Abfragen](sql-database-elastic-scale-multishard-querying.md) Seite auf der Microsoft-Website.

- In Shardlets, die gehören zur gleichen Splitter Karte gespeicherten Daten sollten das gleiche Schema haben. Beispielsweise erstellen Sie keiner Liste-Splitter-Karte, die auf einigen Shardlets mit Daten der Mieter und andere Shardlets, die Produktinformationen enthalten. Diese Regel wird nicht erzwungen durch elastische Skala, aber Datenmanagement und Abfragen wird sehr kompliziert, wenn jedes Shardlet ein anderes Schema hat. In dem eben erwähnten Beispiel sollten Sie zwei Liste Scherbe Maps Erstellen; eine verweisende Mieter-Daten und der andere Punkt zu Produktinformationen. Beachten Sie, dass die Daten gehören verschiedene Shardlets in die gleiche Splitter gespeichert werden können.

	> [AZURBLAU. HINWEIS] Die Kreuz-Splitter-Abfragefunktionalität der elastischen Skala API hängt jedes Shardlet in der Scherbe-Karte mit dem gleichen Schema ab.
- Transaktionsvorgängen sind nur für Daten innerhalb der gleichen Splitter und nicht über Scherben unterstützt. Transaktionen können Shardlets erstrecken, solange sie die gleichen Splitter gehören. Daher, wenn Ihre Geschäftslogik Transaktionen durchführen muss, entweder speichern Sie die betroffenen Daten in derselben Scherbe oder implementieren Sie eventuelle Konsistenz. Weitere Informationen finden Sie unter Anleitung die Datenkonsistenz.
- Platzieren Sie Fragmente in der Nähe der Benutzer, die die Daten in diesen Scherben zugreifen (Geo-suchen Sie Fragmente). Diese Strategie hilft, die um Wartezeit zu verringern.
- Zu vermeiden, dass eine Mischung aus sehr aktiv (Hotspots) und relativ inaktiv Scherben. Versuchen Sie und verteilen Sie die Last gleichmäßig über Scherben. Dies erfordert möglicherweise Hashalgorithmen Shardlet Schlüssel.
- Wenn Sie Geo-Ortung Fragmente sind, stellen Sie sicher, dass die Hash-Schlüssel zuordnen Shardlets gehalten in Scherben, die nah an die Benutzer, die Zugriff auf diese Daten gespeichert.
- Derzeit legen nur eine beschränkte SQL-Daten, die als Shardlet Schlüssel unterstützt werden; _Int, Bigint, Varbinary,_ und _uniqueidentifier_. Die SQL-Anweisungen _int_ und _bigint_ Typen entsprechen den _int_ und _lange_ Datentypen in C#, und haben die gleichen Ranges. Die SQL-Anweisungen _varbinary_ Typ kann behandelt werden, mit einer _Byte_ Arrays in c# und SQL _uniqueidentier_ Typ entspricht der _GUID_ .NET Framework-Klasse.

Wie der Name schon sagt, können elastische Skala ein System zum Hinzufügen und Entfernen von Scherben, da das Datenvolumen schrumpft und wächst. Die APIs in der Azure SQL Datenbank elastische Scale-Clientbibliothek ermöglicht es einer Anwendung zum Erstellen und Löschen von Scherben dynamisch (und transparent die Splitter-Karte Updatemanager), aber entfernen ein Splitter ist ein destruktiver Vorgang auch erforderlich löschen alle Daten in diesem Splitter. Wenn eine Anwendung eine Scherbe in zwei getrennten Fragmente aufgeteilt oder Fragmente zusammen kombinieren, bietet elastische Skala einen separaten Split/Zusammenführen-Service. Dieser Dienst führt in eine Wolke-hosted Service (der Entwickler muss diese Wolke gehosteten Dienst erstellen) und sorgt für sichere Migration von Daten zwischen Scherben. Weitere Informationen finden Sie im Thema [Aufteilen und Zusammenfügen mit elastischen Skala](sql-database-elastic-scale-overview-split-and-merge.md) auf der Microsoft-Website.

## Partitionierungsstrategien für Azure Storage

Azure Storage bietet drei Abstraktionen zur Verwaltung von Daten:

- Tabelle-Lagerung, die skalierbare Struktur Speicher implementiert. Eine Tabelle enthält eine Auflistung von Entitäten, von die jeder einen Satz von Eigenschaften und Werte enthalten kann.
- BLOB-Speicher, Speicher für große Objekte und Dateien versorgt.
- Speicher-Warteschlangen, die zuverlässige asynchrones messaging zwischen Anwendungen zu unterstützen.

Tabelle und Blob-Speicher sind im wesentlichen Schlüssel / Wert-Speicher für strukturierte und unstrukturierte Daten bzw. optimiert werden. Speicher-Warteschlangen bieten einen Mechanismus zum Erstellen von lose gekoppelter, skalierbarer Anwendungen. Tabelle Speicher, Blob Storage und Storage-Warteschlangen werden im Kontext eines Kontos Azure Speicher erstellt. Azure Storage-Konten unterstützen drei Formen der Redundanz:

- Lokal redundante Speicherung, die drei Kopien von Daten in einem einzigen Rechenzentrum verwaltet. Diese Form der Redundanz schützt gegen Hardwarefehler aber nicht gegen eine Katastrophe, die das gesamte Rechenzentrum umfasst.
- Zone-redundante Speicherung der unterhält drei Kopien der Daten verteilt in verschiedenen Rechenzentren innerhalb der gleichen Region (oder über zwei geografisch enger Regionen). Diese Form der Redundanz schützt vor Katastrophen, die innerhalb eines einzigen Datencenters vorkommen, jedoch nicht schützen gegen groß angelegte Netzwerk trennt, die eine ganze Region betreffen. Beachten Sie, dass die Zone-redundante Speicherung nur z.z. nur für Block-Blobs vorhanden ist.
- Geo-redundante Speicherung, die sechs Kopien von Daten verwaltet; drei Exemplare in einer Region (Ihre jeweilige Region) und ein weiterer drei Exemplare in einer abgelegenen Region. Diese Form der Redundanz bietet höchste Ausfallschutz.

Microsoft veröffentlichte Skalierbarkeit Ziele für Azure Storage-Konten; finden Sie auf der Seite [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft-Website. Derzeit das Konto Gesamtspeicherkapazität (die Größe der Daten in Tabelle Speicher, Blob-Speicher und hervorragende Nachrichten im Speicher Warteschlange statt) kann nicht mehr als 500TB. Die maximale Anforderungsrate (vorausgesetzt eine 1KB Entität, Blob oder Nachrichtengröße) ist 20K pro Sekunde. Wenn Ihr System diese Grenzen überschreiten dürfte, dann erwägen Sie, Partitionierung die Last über verschiedene Speicher-Konten; ein einzelnes Azure Abonnement kann bis zu 100 Speicher-Konten erstellen. Beachten Sie jedoch, dass diese Grenzen im Laufe der Zeit ändern können.

## Azure-Tabelle Speicher-Partitionierung

Azure-Tabelle Speicher ist ein Schlüssel-Wert gespeichert, die sich an die Partitionierung. Alle Entitäten in einer Partition gespeichert sind, und Partitionen von Azure-Tabelle Speicher intern verwaltet werden. Jede Entität in einer Tabelle gespeichert, muss einen zweiteiligen Schlüssel bestehend aus bereitstellen:

- Die Partition-Taste. Dies ist ein String-Werte, die bestimmt, auf welcher Partition Azure-Tabelle Speicher die Entität stattfinden wird. Alle Personen mit der gleichen Partitionsschlüssel werden in der gleichen Partition gespeichert.
- Die Zeile-Taste. Dies ist ein weiterer Zeichenfolgenwert, der die Einheit innerhalb der Partition kennzeichnet. Alle Einheiten innerhalb einer Partition sind lexikalisch, sortiert in aufsteigender Reihenfolge, diesen Schlüssel. Die Tastenkombination für die Partition-Schlüssel/Zeile muss für jede Entität eindeutig sein und darf 1KB Länge nicht überschreiten.

Der Rest der Daten für eine Entität besteht aus Anwendung definierten Feldern. Keine bestimmten Schemas erzwungen werden, und jede Zeile kann einen anderen Satz von der Anwendung definierten Felder enthalten. Die einzige Einschränkung ist, dass die maximale Größe einer Entität (einschließlich der Partition und Zeile Schlüssel) derzeit 1 MB. Die maximale Größe einer Tabelle ist 200TB, obwohl diese Zahlen in die Zukunft (Überprüfung der Seite ändern kann [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft-Website die neuesten Informationen über diese Grenzen. Wenn Sie versuchen, die Entitäten zu speichern, die diese Kapazität überschreiten, dann erwägen Sie, aufteilen in mehrere Tabellen; Verwenden Sie die vertikale Partitionierung und unterteilen Sie die Felder in den Gruppen, die am ehesten gemeinsam zugegriffen werden.

Abbildung 7 zeigt die logische Struktur eines Beispiel-Lagerung-Kontos (Contoso Daten) für eine fiktive e-Commerce-Anwendung. Die Storage-Konten enthält drei Tabellen (Kundeninformation, Produktinfo und Bestellinfo), und jede Tabelle mehrere Partitionen hat. In der Kundeninfo-Tabelle die Daten partitioniert ist, nach der Stadt, in der der Kunde ansässig ist, und der Schlüssel Zeile enthält die Kunden-ID. In der Produkt-Info-Tabelle werden die Produkte nach Produktkategorie partitioniert und der Zeile Schlüssel enthält die Produktnummer. In der Bestellinfo-Tabelle werden die Aufträge durch das Datum partitioniert, auf dem sie platziert wurden und der Zeile Schlüssel angegeben das der Zeitpunkt die Bestellung. Beachten Sie, dass alle Daten ist geordnet nach der Zeile Schlüssel in jeder Partition.

![](media/best-practices-data-partitioning/TableStorage.png)

_Abbildung 7. -Tabellen und Partitionen in einem Beispiel-Speicher-Konto_

> [AZURBLAU. HINWEIS] Azure-Tabelle Speicher hat jede Entität außerdem ein Timestamp-Feld hinzugefügt. Das Timestamp-Feld wird betreut von Tabelle Speicher und aktualisiert jedes Mal, wenn die Entität geändert und wieder auf eine Partition geschrieben. Der Tisch-Speicherdienst verwendet dieses Feld, um vollständige Parallelität (jedes Mal, wenn eine Anwendung einer Entität zurück in Tabelle Speicher, schreibt der Speicherdienst Tabelle vergleicht den Wert der Zeitstempel in der Entität geschrieben wird mit dem Wert in Tabelle Speicher, und wenn sie unterschiedlich sind eine andere Anwendung muss haben geändert die Entität da es abgerufen wurde und der Schreiben fehlschlägt) zu implementieren. Dieses Feld sollte nicht geändert werden, in Ihrem eigenen Code, und weder sollten Sie einen Wert für dieses Feld angeben, wenn Sie eine neue Entität erstellen.

Azure-Tabelle Speicher verwendet die Partition-Taste zum Speichern der Daten bestimmen. Wenn eine Entität mit einem Partitionsschlüssel zuvor ungenutzte zu einer Tabelle hinzugefügt wird, wird Azure-Tabelle Speicher eine neue Partition für diese Entität erstellen. Andere Personen mit dem gleichen Partitionsschlüssel werden in der gleichen Partition gespeichert. Dieser Mechanismus wird effektiv eine automatische Skalierung-Strategie implementiert. Jede Partition wird auf einem Server in einer Azure Datencenter (um sicherzustellen, dass Abfragen, die Abrufen von Daten aus einer einzelnen Partition schnell ausgeführt) gespeichert werden, aber verschiedene Partitionen können auf mehrere Server verteilt werden. Darüber hinaus kann ein einzelner Server mehrere Partitionen hosten, wenn diese Partitionen in der Größe begrenzt sind.

Beim Entwerfen Ihrer Einheiten zur Azure-Tabelle Speicher, sollten Sie folgende Punkte beachten:

- Die Auswahl der Partition Key und Zeile Schlüsselwerte sollte übrigens angetrieben werden, in die die Daten zugegriffen wird. Wählen Sie eine Partition Schlüssel/Zeile-Tastenkombination, die die Mehrheit Ihrer Abfragen unterstützt. Die effizientesten Abfragen werden Daten durch Angabe der Partition und die Zeile Schlüssel abrufen. Abfragen, die Partitionsschlüssel und eine Reihe von Zeilenschlüsseln angeben können zufrieden sein, durch eine einzelne Partition Scannen; Dies ist relativ schnell, da die Daten in der Zeile Schlüsselreihenfolge gehalten werden. Abfragen, die mindestens den Partitionsschlüssel nicht angeben können Azure-Tabelle Speicher Scannen jede Partition für die Daten verlangen.

	> [AZURBLAU. TIPP] Wenn eine Entität mit einem natürlichen Schlüssel hat, als des Partition-Schlüssels verwenden Sie, und geben Sie eine leere Zeichenfolge als die Zeile-Taste. Wenn eine Entität einen zusammengesetzten Schlüssel, bestehend aus zwei Eigenschaften hat, wählen Sie die langsamste verändernde Eigenschaft als Partitionsschlüssel und das andere als Zeile Schlüssel. Wenn ein Unternehmen mehr als zwei wichtige Eigenschaften verfügt, verwenden Sie eine Verkettung der Eigenschaften, um die Partition und Zeile Schlüssel bereitzustellen.

- Wenn Sie regelmäßig Abfragen, die Daten mit anderen Bereichen als der Partition und Zeile Schlüssel suchen ausführen, sollten Sie implementieren die [Index-Tabelle-Muster](https://msdn.microsoft.com/library/dn589791.aspx).
- If you generate partition keys using a monotonic increasing or decreasing sequence (such as "0001", "0002", "0003", …) and each partition only contains a limited amount of data, then Azure table storage may physically group these partitions together on the same server. This mechanism assumes that the application is most likely to perform queries across a contiguous range of partitions (range queries) and is optimized for this case. However, this approach can lead to hotspots focused on a single server as all inserts of new entities will likely be concentrated at one or other end of the contiguous ranges. It can also reduce scalability. To spread the load more evenly across servers, consider hashing the partition key to make the sequence more random.
- Azure-Tabelle Speicher unterstützt Transaktionsvorgängen für Entitäten, die auf die gleiche Partition gehören. Dies bedeutet, dass eine Anwendung mehrere Insert, Update, löschen, ersetzen oder Zusammenführungsoperationen als atomare Einheit (vorbehaltlich der Transaktion nicht darunter mehr als 100 Einheiten und die Nutzlast der Anforderung nicht mehr als 4 MB groß) durchführen kann. Vorgänge, die sich über mehrere Partitionen sind nicht transaktionale und möglicherweise müssen Sie eventuelle Konsistenz zu implementieren, wie von der Daten-Konsistenz-Anleitung beschrieben. Besuchen Sie für weitere Informationen zu Tabelle Speicher und Transaktionen, die [Entitätsgruppe Transaktionen](https://msdn.microsoft.com/library/azure/dd894038.aspx) Seite auf der Microsoft-Website.
- Geben Sie besonders auf die Granularität des Schlüssels Partition:
	- Mit der gleichen Partitionsschlüssel für jede Entität bewirkt, dass den Tisch-Speicherdienst zu eine einzige große Partition statt auf einem Server verhindern, dass es aus skalieren und sich so die Belastung auf einem einzelnen Server zu erstellen. Dieser Ansatz eignet sich daher nur bei Systemen, die eine kleine Anzahl von Einheiten zu verwalten. Dieser Ansatz wird jedoch sichergestellt, dass alle Entitäten Entität Gruppe Transaktionen teilnehmen können.
	- Mit einem einzigartigen Partitionsschlüssel für jede Entität bewirkt, dass den Tisch-Speicherdienst eine separate Partition für jede Entität, die möglicherweise eine große Anzahl von kleinen Partitionen (abhängig von der Größe der Einheiten) zu erstellen. Dieser Ansatz ist besser skalierbar als mit einem einzelnen Partition-Schlüssel, aber Entität Transaktionen sind nicht möglich und Abfragen, die mehr als eine Entität zu holen können lesen aus mehr als einem Server beinhalten. Wenn die Anwendung führt könnte jedoch Bereichsabfragen, danach eine monotone Folge verwenden, um die Partition-Schlüssel generieren helfen, diese Abfragen zu optimieren.
	- Freigabe der Partition-Taste über eine Teilmenge der Instanzen können Sie gruppieren mit Bezug von Entitäten in der gleichen Partition. Operationen mit verknüpften Entitäten mit Entität Transaktionen ausgeführt werden können, und Abfragen, die eine Reihe von verknüpften Entitäten zu holen können durch den Zugriff auf einen einzelnen Server erfüllt werden.

Weitere Informationen zum Partitionieren von Daten im Speicher der Azure-Tabelle finden Sie im Artikel [Entwerfen eine skalierbare Partitionierungsstrategie für Azure-Tabelle Speicher](https://msdn.microsoft.com/library/azure/hh508997.aspx) auf der Microsoft-Website.

## Partitionierung von Azure Blob-Speicher

Himmelblau-Blob-Speicher können Sie große binäre Objekte, Größe für Block Blobs von derzeit bis zu 200 GB oder 1 TB für Seite Blobs zu halten (die aktuellsten Informationen finden Sie auf der [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) Seite auf der Microsoft-Website). Verwenden Sie hoch-oder herunterladen große Datenmengen schnell Block Blobs in Szenarien wie streaming, wo Sie Sie brauchen. Verwenden Sie Seite Blobs für Anwendungen, die eine zufällige anstatt seriellen Zugriff auf Teile der Daten erfordern.

Jede Blob (Block oder Seite) ist in einem Container in einer Azure Speicher-Konto statt. Container können Sie Gruppieren verwandte Blobs, die die gleichen Sicherheitsanforderungen zusammen haben, obwohl diese Gruppierung logisch anstatt physische ist. Innerhalb eines Containers hat jede Blob einen eindeutigen Namen.

BLOB-Speicher wird automatisch anhand des Namens Blob verteilt. Jede Blob findet in einer eigenen Partition, und Blobs im selben Container teilen Sie eine Partition nicht. Diese Architektur ermöglicht Azure Blob-Speicher auf die serverübergreifend transparent Lastenausgleich beim verschiedenen Blobs in einem Behältnis auf verschiedenen Servern verteilt werden können.

Die Aktionen des Schreibens einer einzigen Block (Block Blob) oder Seite (Seite Blob) sind atomar, Operationen, die Blöcke, Seiten oder Blobs umfassen jedoch nicht. Wenn Sie benötigen, um Konsistenz zu gewährleisten, bei der Ausführung von Schreib-Operationen zwischen Blöcken, Seiten und Blobs, musst du eine Schreibsperre herausnehmen, mithilfe einer Blob-Lease.

Azure-Blob-Speicher unterstützt Übertragungsraten von bis zu 60MB pro Sekunde oder 500 Anfragen pro Sekunde für jeden Blob. Wenn Sie absehen, übertrifft diese Grenzen können und die Blob-Daten relativ statisch ist, dann prüfen Sie, replizieren Blobs mit dem Azure Content Delivery Network (CDN). Weitere Informationen finden Sie auf der Seite [Verwendung von CDN für Azure](cdn-how-to-use.md) auf der Microsoft-Website. Weitere Anleitungen und Überlegungen finden Sie im Artikel Content Delivery Network (CDN).

## Partitionierung von Azure Speicher Warteschlangen

Azurblaue Speicher Warteschlangen können Sie implementieren, asynchrones messaging zwischen Prozessen. Ein himmelblau Speicher-Konto kann eine beliebige Anzahl von Warteschlangen enthalten, und jede Warteschlange kann eine beliebige Anzahl von Nachrichten enthalten. Die einzige Einschränkung ist das Platzangebot in der Speicher-Konto. Die maximale Größe einer einzelnen Nachricht beträgt 64KB. Wenn Nachrichten größer als dies erforderlich ist, dann erwägen Sie, Azure Service Bus Warteschlangen statt.

Each storage queue has a unique name within the storage account in which it is contained. Azure partitions queues based on the name, and all messages for the same queue are stored in the same partition, controlled by a single server. Different queues can be managed by different servers to help balance the load. The allocation of queues to servers is transparent to applications and users. In a large scale application, don't use the same storage queue for all instances of the application as this approach may cause the server hosting the queue to become a hotspot; use different queues for different functional areas of the application. Azure storage queues do not support transactions, so directing messages to different queues should have little impact on messaging consistency.

Eine Warteschlange Azure Speicher kann bis zu 2000 Nachrichten pro Sekunde verarbeiten.  Wenn Sie Nachrichten mit einer größeren Geschwindigkeit als diese verarbeiten müssen sollten Sie mehrere Warteschlangen zu erstellen. Erstellen Sie beispielsweise in einer globalen Anwendung getrennte Lagerung Warteschlangen in getrennten Lagerung Konten Anwendungsinstanzen läuft in jeder Region zu behandeln.

## Partitionierungsstrategien für Azure Service Bus

Azurblaue Service Bus verwendet eine Nachricht-Broker, um Nachrichten an eine Warteschlange Service Bus oder ein Thema zu behandeln. Standardmäßig werden alle Nachrichten an eine Warteschlange oder ein Thema von der gleichen Nachricht Makler Prozess behandelt. Diese Architektur kann eine Beschränkung auf der Gesamtdurchsatz der Nachrichtenwarteschlange stellen. Aber Sie können auch Partitionieren eine Warteschlange oder ein Thema bei der Erstellung durch Festlegen der _EnablePartitioning_ Eigenschaft der Warteschlange oder Thema Beschreibung zur _wahr_. A partitioned queue or topic is divided up into multiple fragments, each of which is backed by a separate message store and message broker. Service Bus takes responsibility for creating and managing these fragments. When an application posts a message to a partitioned queue or topic, Service Bus assigns the message to a fragment for that queue or topic. When an application receives a message from a queue or subscription, Service Bus checks each fragment for the next available message and then passes it to the application for processing. This structure helps to distribute the load across message brokers and message stores, increasing scalability and improving availability; if the message broker or message store for one fragment is temporarily unavailable, Service Bus can retrieve messages from one of the remaining available fragments.

Service Bus weist eine Meldung zu einem Fragment wie folgt:

- Wenn die Meldung zu einer Sitzung gehört, alle Nachrichten mit dem gleichen Wert für die _ SessionId_  Eigenschaft an das gleiche Fragment gesendet werden.
- Wenn die Nachricht nicht zu einer Sitzung gehört, aber der Absender, einen Wert für angegeben wurde die _PartitionKey_ Eigenschaft, dann alle Nachrichten mit dem gleichen _PartitionKey_ Wert sind an den gleichen Fragment.

	> [AZURBLAU. HINWEIS] Wenn die _SessionId_ und _PartitionKey_ Eigenschaften angegeben werden, dann müssen sie auf den gleichen Wert, die sonst, den die Nachricht abgelehnt wird, festgelegt werden.
- Wenn die _SessionId_ und _PartitionKey_ Eigenschaften für eine Nachricht sind nicht angegeben, aber Duplikaterkennung aktiviert ist, die _MessageId_ Eigenschaft wird verwendet. Alle Nachrichten mit dem gleichen _MessageId_ werden dem gleichen Fragment geleitet.
- Wenn keine Nachrichten enthalten eine _SessionId, PartitionKey,_ oder _MessageId_ Eigentum, dann Service Bus weist Nachrichten Fragmente im Round-Robin. Wenn ein Fragment nicht verfügbar ist, wird Service Bus zur nächsten übergehen. Auf diese Weise bewirkt eine vorübergehende Störung der messaging-Infrastruktur nicht den Nachricht Sendevorgang zum Scheitern verurteilt.

Sie sollten die folgenden Punkte bei der Entscheidung und wie, oder ob, Partition eine Nachrichtenwarteschlange Service Bus oder Thema:

- Service Bus Warteschlangen und Themen werden im Rahmen eines Service Bus-Namespace erstellt. Service Bus erlaubt derzeit bis zu 100 partitionierte Warteschlangen oder Themen pro Namespace.
- Jeden Service Bus-Namespace erlegt Quoten auf die Ressourcen zur Verfügung, z. B. die Anzahl der Abonnements pro Thema, die Anzahl der gleichzeitigen Senden und empfangen von Anforderungen pro Sekunde, und die maximale Anzahl gleichzeitiger Verbindungen, die hergestellt werden kann. Diese Kontingente sind auf der Microsoft-Website auf der Seite dokumentiert. [Service Bus Quoten](https://msdn.microsoft.com/library/azure/ee732538.aspx). Wenn Sie erwarten, diese Werte überschreiten dass, dann erstellen Sie zusätzliche Namespaces mit ihren eigenen Themen und Warteschlangen, und verteilt die Arbeit auf diese Namespaces. Z. B. in einer globalen Anwendung erstellen Sie separaten Namespaces in jeder Region und konfigurieren Sie Anwendungsinstanzen um Warteschlangen und Themen in der nächstgelegenen Namespace zu verwenden.
- Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Dies kann sein ein _SessionId, PartitionKey,_ oder _MessageId_. Alle Nachrichten, die als Teil der gleichen Transaktion gesendet werden müssen den gleichen Partitionsschlüssel angeben, da sie von der gleichen Nachricht Makler Prozess behandelt werden müssen. Sie können keine Nachrichten an unterschiedliche Warteschlangen oder Themen innerhalb derselben Transaktion senden.
- Sie können nicht konfigurieren eine partitionierte Warteschlange oder ein Thema automatisch gelöscht werden, wenn es im Leerlauf befindet.
- Wenn Sie plattformübergreifende oder Hybrid-Lösungen erstellen, können nicht Sie derzeit partitionierte Warteschlangen und Themen mit der Advanced Message Queuing Protocol (AMQP) verwenden.

## Partitionierungsstrategien für Azure DocumentDB

Himmelblau DocumentDB ist eine NoSQL-Datenbank, die Dokumente speichern kann. Ein Dokument in DocumentDB ist eine Darstellung JSON serialisiert ein Objekt oder andere Daten. Keine festen Schemas werden erzwungen, außer dass jedes Dokument, eine eindeutige ID. enthalten muss

Dokumente sind in Sammlungen organisiert. Eine Auflistung können Sie Beiträge zu diesem Thema zusammen zu gruppieren. Beispielsweise in einem System, die Blog-Postings verwaltet, konnte Sie den Inhalt der einzelnen Blog-posts als Dokument in einer Auflistung zu speichern, und erstellen Sammlungen für jeden Gegenstand. Alternativ in eine mandantenfähige Anwendung z. B. ein System, das verschiedene Autoren kann Beiträge zum Steuern und verwalten Sie ihren eigenen Blog, konnte durch die Partitionierung Blogs von Autor und erstellen Sie eine separate Sammlung für jeden Autor. Der Speicher Speicherplatz für Sammlungen kann ist elastisch und schrumpfen oder wachsen, je nach Bedarf.

Dokument-Sammlungen bieten einen natürlichen Mechanismus zum Partitionieren von Daten innerhalb einer Datenbank. Intern eine DocumentDB Datenbank kann mehrere Server erstrecken, und DocumentDB kann versuchen, die Last zu verteilen, indem Sammlungen auf Servern verteilt. Die einfachste Möglichkeit zum Implementieren von Splitter ist zum Erstellen einer Auflistung für jede Scherbe.

> [AZURBLAU. HINWEIS] Jede DocumentDB ist zugewiesene Ressourcen im Hinblick auf eine _Leistungsstufe_. Ein Leistungsniveau mit zugeordnet ist ein _Anfrage-Einheit_ Ratenlimit (RU). RU-Ratenlimit gibt die Menge der Ressourcen, die werden für diese Auflistung reserviert und steht für exklusive Verwendung durch diese Auflistung. Die Kosten einer Auflistung richtet sich nach der Performance-Level für diese Auflistung ausgewählt; Je höher die Leistung Level (und RU Ratenlimit) je höher die Ladung. Sie können das Leistungsniveau einer Auflistung mithilfe von Azure Verwaltungsportal anpassen. Weitere Informationen finden Sie auf der Seite [Leistungsstufen im DocumentDB](documentdb-performance-levels.md) auf der Microsoft-Website.

Alle Datenbanken werden im Rahmen eines DocumentDB-Konto erstellt. Ein einziges DocumentDB-Konto kann mehrere Datenbanken enthalten, und gibt an in welcher Region die Datenbanken erstellt werden. Jedes Konto DocumentDB erzwingt auch eigene Zugriffskontrolle. Können Sie DocumentDB Konten zu Geo-suchen Sie Fragmente (Sammlungen in Datenbanken) in der Nähe der Benutzer, die darauf zugreifen müssen, und Einschränkungen zu erzwingen, damit nur diejenigen Benutzer darauf zugreifen können.

Jedes DocumentDB-Konto verfügt über ein Kontingent, das begrenzt die Anzahl der Datenbanken und Sammlungen, die es enthalten kann und die Menge des verfügbaren Dokument. Diese Grenzwerte können geändert werden, aber werden auf der Seite beschrieben [DocumentDB Grenzen und Quoten](documentdb-limits.md) auf der Microsoft-Website. Es ist theoretisch möglich, dass wenn Sie ein System einführen, wo alle Scherben zu derselben Datenbank gehören, könnte Sie die Speichergrenze für die Kapazität des Kontos erreichen. In diesem Fall müssen Sie zusätzliche DocumentDB Konten und Datenbanken erstellen und verteilen Sie die Scherben auf diese Datenbanken. Selbst wenn Sie aller Wahrscheinlichkeit nach die Speicherkapazität einer Datenbank getroffen werden, ist ein guter Grund für die Verwendung mehrerer Datenbanken jedoch jede Datenbank verfügt über einen eigenen Satz von Benutzern und Berechtigungen. Sie können diesen Mechanismus verwenden, um Zugang zu Sammlungen auf einer Basis pro Datenbank zu isolieren.

Abbildung 8 veranschaulicht die übergeordnete Struktur der DocumentDB Architektur.

![](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Abbildung 8. -Die Struktur des DocumentDB_

Es ist die Verantwortung der Clientanwendung zu leiten Sie Anforderungen an die entsprechenden Splitter in der Regel durch die Implementierung eigener Zuordnungsmechanismus basierend auf einige Attribute der Daten, die den Splitter-Schlüssel definieren. Abbildung 9 zeigt zwei DocumentDB Datenbanken, jeweils zwei Sammlungen als Fragmente enthalten. Die Daten vom Mieter ID sharded und enthält die Daten für eine bestimmte Mieter. Die Datenbanken werden in separaten Konten, DocumenDB erstellt, die in der gleichen Region wie den Mietern befinden deren Daten, die sie enthalten. Die Routinglogik in der Clientanwendung verwendet die Mieter-ID als Schlüssel Scherbe.

![](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Abbildung 9. -Durchführung von Splitter mit Azure DocumentDB_

Partitionieren von Daten mit DocumentDB entscheiden, sollten Sie folgende Punkte beachten:

- Mit einer DocumentDB-Datenbank verfügbaren Ressourcen sind abhängig von den Einschränkungen der Quote des DocumentDB-Kontos. Jede Datenbank kann eine Anzahl von Sammlungen halten (auch hier gibt es ein Limit) und jeder Sammlung ein Leistungsniveau, das regelt das RU-Ratenlimit (reservierte Durchsatz) für diese Auflistung zugeordnet ist. Weitere Informationen finden Sie auf der [DocumentDB Grenzen und Quoten](documentdb-limits.md) Seite auf der Microsoft-Website.
- Jedes Dokument muss ein Attribut haben, die verwendet werden können, um das Dokument innerhalb der Auflistung eindeutig zu identifizieren, in dem es gehalten wird. Dies unterscheidet sich von der Splitter-Schlüssel definiert, in welcher Sammlung das Dokument gehalten wird. Eine Sammlung kann eine große Anzahl von Dokumenten in der Theorie nur begrenzt durch die maximale Länge der Dokument-ID enthalten. Die Dokument-ID kann bis zu 255 Zeichen sein.
- Alle Vorgänge in einem Dokument werden im Rahmen einer Transaktion ausgeführt, die sich auf die Auflistung bezieht, in dem das Dokument enthalten ist. Wenn ein Vorgang fehlschlägt, wird die Arbeit, die er durchgeführt hat ein Rollback ausgeführt.  Während ein Dokuments unterliegt einer Operation ist, unterliegen alle vorgenommenen Änderungen auf Snapshot-Isolation. Dieser Mechanismus gewährleistet, dass, wenn, beispielsweise eine Anforderung zum Erstellen eines neuen Dokuments scheitert, ein anderer Benutzer die Datenbank gleichzeitig Abfragen keine partielle Dokument angezeigt wird, das dann entfernt wird.
- DocumentDB Abfragen sind auch auf der Ebene beschränkt. Eine einzige Abfrage kann nur Daten aus einer Auflistung abrufen. Benötigen Sie zum Abrufen von Daten aus mehreren Auflistungen müssen Sie jede Sammlung einzeln Abfragen und die Ergebnisse im Anwendungscode zusammenführen.
- DocumentDB supports programmable items that can all be stored in a collection alongside documents: stored procedures, user-defined functions, and triggers (written in JavaScript). These items can access any document within the same collection. Furthermore, these items execute either inside the scope of the ambient transaction (in the case of a trigger that fires as the result of a create, delete, or replace operation performed against a document), or by starting a new transaction (in the case of a stored procedure that is executed as the result of an explicit client request). If the code in a programmable item throws an exception, the transaction is rolled back. You can use stored procedures and triggers to maintain integrity and consistency between documents, but these documents must all be part of the same collection.
- Sie sollten sicherstellen, dass die Auflistungen, die Sie beabsichtigen, in den Datenbanken in eine DocumentDB Konto halten unwahrscheinlich, dass der Durchsatz innerhalb der Grenzwerte durch die Leistungsstufen der Sammlungen definiert sind. Diese Grenzwerte werden beschrieben, auf die [DocumentDB die entsprechenden Anforderungen verwalten](documentdb-manage.md) Seite auf der Microsoft-Website. Wenn Sie erwarten, diese Grenzen zu erreichen, prüfen Sie auf Datenbanken auf verschiedenen DocumentDB-Konten zur Verringerung der Belastung pro Sammlung Sammlungen aufteilen.

## Partitionierungsstrategien für himmelblau-Suche

Die Fähigkeit, Daten zu suchen ist oft die primäre Methode der Navigation und Erforschung bereitgestellt durch viele Web-Anwendungen, ermöglicht Benutzern, Ressourcen (z. B. Produkte in einer e-Commerce-Anwendung) basierend auf Kombinationen von Suchkriterien schnell zu finden. Der Azure-Suchdienst bietet Volltext-Suchfunktionen über Webinhalte und umfasst Features wie Typ-ahead, vorgeschlagene Abfragen auf der Grundlage von in der Nähe von Übereinstimmungen und facettierten Navigation. Eine vollständige Beschreibung dieser Funktionen ist verfügbar auf der [Himmelblau-Suche-Übersicht](https://msdn.microsoft.com/library/azure/dn798933.aspx) Seite auf der Microsoft-Website.

Der Suchdienst speichert durchsuchbare Inhalte als JSON-Dokumente in einer Datenbank. Sie definieren die Indizes, die geben die durchsuchbaren Felder in diesen Dokumenten und bieten diese Definitionen mit dem Suchdienst. Wenn ein Benutzer eine Suchanfrage absendet, verwendet den Suchdienst die entsprechenden Indizes, um passenden Elemente zu finden.

Um Konflikte zu verringern, kann die Speicherung von den Suchdienst verwendet unterteilt werden in bis zu 1, 2, 3, 4, 6 oder 12 Partitionen, und die einzelnen Partitionen können repliziert werden bis zu 6 Mal. Das Produkt der Anzahl der Partitionen, multipliziert mit der Anzahl von Replikaten heißt die _Suche Einheit_ (SU). Eine einzelne Instanz der Suchdienst kann maximal 36 SUs enthalten (eine Datenbank mit 12 Partitionen unterstützt nur maximal 3 Replikate). Sie werden berechnet, für jedes SU, der zugeordnet wird, zu Ihrem Dienst. Wächst das Volumen der durchsuchbare Inhalte erhöht oder die Rate der Suchanfragen, können Sie eine vorhandene Instanz von Suchdienst für die zusätzliche Belastung die SUs hinzufügen. Der Suchdienst selbst übernimmt Verantwortung für gleichmäßiges Verteilen der Dokumente über die Partitionen und keine manuellen Partitionierungen Strategien werden derzeit unterstützt.

Jede Partition kann bis zu 15 Millionen Dokumente enthalten oder 300 GB Speicherplatz (je niedriger, abhängig von der Größe Ihrer Dokumente und Indizes ist) zu besetzen. Sie können bis zu 50 Indizes erstellen. Die Erbringung der Leistung variiert je nach Komplexität der Dokumente, die verfügbaren Indizes und die Auswirkungen der Netzwerklatenz. Im Durchschnitt sollte ein einzelnes Replikat (1SU) 15 Abfragen pro Sekunde (QPS), verarbeiten, obwohl Sie durchführen sollten, Benchmarken mit Ihren eigenen Daten, eine präzisere Maß für den Durchsatz zu erhalten. Weitere Informationen finden Sie unter der [Grenzen und Einschränkungen (himmelblau-Such-API)]( https://msdn.microsoft.com/library/azure/dn798934.aspx) Seite auf der Microsoft-Website.

> [AZURBLAU. HINWEIS] Sie können einen begrenzten Satz von Datentypen in durchsuchbare Dokumente speichern; Streicher, boolesche Werte, numerische Daten, Datetime-Daten und einige geografischen Daten. Weitere Einzelheiten finden Sie in der [Unterstützte Datentypen (himmelblau-Suche)]( https://msdn.microsoft.com/library/azure/dn798938.aspx) Seite auf der Microsoft-Website.

Sie haben nur begrenzte Kontrolle über wie der Azure-Suchdienst für jede Instanz des Dienstes Daten partitioniert. Allerdings können in einem globalen Umfeld möglicherweise zur Verbesserung der Leistung und Reduzierung von Latenz und Streit weiter durch die Partitionierung des Service selbst mit einem der folgenden Strategien:

- Erstellen Sie eine Instanz des Suchdienstes in den einzelnen geografischen Regionen und stellen sicher, dass Client-Anwendungen auf gerichtet sind die nächstgrößere verfügbare Instanz. Diese Strategie erfordert, dass alle Updates durchsuchbare Inhalte für alle Instanzen des Dienstes in einer fristgerechten Weise repliziert werden.
- Erstellen Sie zwei-Ebenen-Suchdienst; ein lokaler Dienst in jeder Region mit den Daten von Benutzern in dieser Region am häufigsten zugegriffen und einen weltweiten Service, der alle Daten umfasst. Benutzer können Anfragen an den lokalen Dienst (für schnellen, aber begrenzte Ergebnisse) oder an den globalen Dienst (für langsamer aber vollständigere Ergebnisse) verweisen. Dieser Ansatz eignet sich am besten, wenn es gibt eine erhebliche regionale Unterschiede in den Daten gesucht.

## Strategien für Azure Redis Cache-Partitionierung

Azurblaue Redis Cache bietet einen gemeinsamen Cache Dienst in der Wolke, die auf den Datenspeicher der Redis-Schlüssel/Wert basiert. Wie der Name andeutet, Azure Redis Cache ist als eine Zwischenspeicherung Lösung gedacht und sollten daher nur verwendet werden für die Abhaltung von transienter Daten nicht als einen dauerhaften Datenspeicher; Anwendungen, die Azure Redis Cache nutzen sollte weiterhin funktionieren, wenn der Cache nicht verfügbar ist. Himmelblau Redis Cache unterstützt Primär/Sekundär-Replikation, um hohe Verfügbarkeit, doch derzeit beschränkt die maximale Cachegröße auf 53GB. Wenn Sie mehr Platz als dies benötigen, müssen Sie zusätzliche Cache erstellen. Für weitere Informationen besuchen Sie die [Microsoft Azure-Cache](http://azure.microsoft.com/services/cache/) Seite auf der Microsoft-Website.

Partitioning a Redis data store involves splitting the data across instances of the Redis service. Each instance constitutes a single partition. Azure Redis Cache abstracts the Redis services behind a façade and does not expose them directly. The simplest way to implement partitioning is to create multiple Azure Redis caches and spread the data across them. You can associate each data item with an identifier (a partition key) that specifies in which cache it should be stored. Your client application logic can use this identifier to route requests to the appropriate partition. This scheme is very simple, but if the partitioning scheme changes (if additional Azure Redis Caches are created, for example), client applications may need to be reconfigured.

Native Redis (not Azure Redis Cache) supports server-side partitioning based on Redis clustering. In this approach, the data is divided evenly across servers by using a hashing mechanism. Each Redis server stores metadata that describes the range of hash keys that the partition holds, and also contains information about which hash keys are located in the partitions on other servers. Client applications simply send requests to any of the participating Redis servers (probably the closest server).The Redis server examines the client request and if it can be resolved locally it will perform the requested operation, otherwise it will forward the request on to the appropriate server. This model is implemented by using Redis clustering, and is described in more detail on the [Redis Cluster-Lernprogramm](http://redis.io/topics/cluster-tutorial) Seite der Redis-Website. Redis clustering für Client-Anwendungen transparent ist und zusätzliche Redis-Server können Cluster (und die Daten neu partitioniert) hinzugefügt werden, ohne dass Sie die Clients neu konfigurieren.

> [AZURBLAU. WICHTIG] Azurblaue Redis Cache unterstützt nicht derzeit Redis clustering. Wollen Sie diesen Ansatz mit Azure zu implementieren, müssen Sie Ihre eigenen Redis-Server implementieren, Redis auf eine Reihe von Azure virtuelle Computer installieren und konfigurieren sie manuell. Die Seite [Redis auf einem CentOS Linux VM in Azure ausgeführt](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) auf der Microsoft exemplarisch Webseite ein Beispiel zum Erstellen und Konfigurieren eines Redis-Knotens als einer Azure-VM ausgeführt.

Die Seite [Partitionierung: wie Daten zwischen mehreren Redis-Instanzen geteilt](http://redis.io/topics/partitioning) über die Redis bietet die Website Weitere Informationen zum Implementieren von Partitionierung mit Redis. Der Rest dieses Abschnitts wird vorausgesetzt, dass Sie Client- oder Proxy-gestützte Partitionierung implementieren.

Bei der Entscheidung, wie Daten mit Azure Redis Cache zu partitionieren, sollten Sie folgende Punkte beachten:

- Azurblaue Redis Cache soll nicht als permanente Datenspeicher, so zu handeln, was Partitionierungsschema Sie implementieren Anwendungscode sollten bereit sein, zu akzeptieren, dass die Daten nicht im Cache gefunden werden und wurde von anderer Seite abgerufen werden.
- Zusammenhalten von Daten, auf die häufig zugegriffen werden in der gleichen Partition. Redis ist ein leistungsfähiges Schlüssel/Wert-Speicher, der bietet mehrere hochgradig optimierte Mechanismen zur Strukturierung von Daten, angefangen bei einfachen Zeichenketten (eigentlich Binärdaten bis zu 512MB Länge) um aggregierte Typen, z. B. Listen (die als Warteschlangen und stapelt fungieren können), Sets (bestellt und ungeordnet) und hackt (die Verwandte Bereichen, wie z. B. die Elemente, die die Felder in einem Objekt stellen gruppieren können). Die Aggregattypen aktivieren Sie viele ähnliche Werte mit dem gleichen Schlüssel zuordnen; Redis Schlüssel identifiziert eine Liste, Menge oder Hash anstatt die Datenelemente, die es enthält. Diese Typen sind alle verfügbaren mit Azure Redis Cache und werden beschrieben durch die [Datentypen](http://redis.io/topics/data-types) Seite der Redis-Website. Beispielsweise könnten Teil eines Ecommerce-Systems, das die Bestellungen der Kunden nachverfolgt, die Details jedes einzelnen Kunden in einem Redis-Hash mit der Kunden-ID eingegeben gespeichert werden Jeder Hash konnte eine Sammlung von Bestellnummern für den Kunden halten. Eine separate Redis-Gruppe halten konnten die Aufträge wieder strukturiert als Hashes verschlüsselt unter Verwendung der Bestell-ID.  Abbildung 10 zeigt diese Struktur. Beachten Sie, dass die Redis keine Form der referenziellen Integrität implementiert wird, so es dem Entwickler Verantwortung ist, die Beziehungen zwischen Kunden und Bestellungen zu halten.

![](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Abbildung 10. -Vorgeschlagene Struktur in Redis-Speicher für die Aufzeichnung von Kundenbestellungen und die dazugehörigen details_

> [AZURBLAU. HINWEIS] In Redis alle Tasten sind binäre Datenwerte (z.B. Redis Strings) und können bis zu 512MB an Daten, enthalten, so dass theoretisch einen Schlüssel fast keine Informationen enthalten kann. Jedoch sollten Sie eine konsistente Benennungskonvention für Schlüssel erlassen, das ist Beschreibung der Art der Daten und identifiziert, die die Entität, aber das ist nicht übermäßig lang. Ein gemeinsamer Ansatz ist Schlüssel des Vordrucks "Entität verwenden_Typ: ID", z. B."Kunde: 99", um den Schlüssel für Kunden mit der ID-99 anzugeben.

- Sie können die vertikale Partitionierung durch Speichern von Informationen in unterschiedlichen Aggregationen in derselben Datenbank implementieren. Z. B. in einer e-Commerce-Anwendung, die Sie häufig speichern konnte zugegriffen Informationen zu Produkten in einem Redis-Hash und der weniger häufig verwendeten detaillierte Informationen in einem anderen. Beide Hashwerte konnte verwenden dieselbe Produkt-ID als Teil des Schlüssels, z. B. "Produkt:_NN_"wo _NN_ ist die Product ID für Produktinformationen und "Produkt_Informationen: _NN_"für die detaillierten Daten. Diese Strategie kann helfen, um das Datenvolumen zu verringern, die meisten Abfragen abrufen dürften.
- Repartitioning a Redis data store is a complex and time-consuming task. Redis clustering can repartition data automatically, but this facility is not available with Azure Redis Cache. Therefore, when you design your partitioning scheme, you should endeavor to leave sufficient free space in each partition to allow for expected data growth over time. However, remember that Azure Redis Cache is intended to cache data temporarily, and that data held in the cache can have a limited lifetime specified as a time-to-live (TTL) value. For relatively volatile data the TTL should be short, but for static data the TTL can be a lot longer. You should avoid storing large amounts of long-lived data in the cache if the volume of this data is likely to fill the cache. You can specify an eviction policy that causes Azure Redis Cache to remove data if space is at a premium.

	> [AZURBLAU. HINWEIS] Azurblaue Redis-Cache können Sie die maximale Größe des Caches (ab 250 MB auf 53 GB) angeben, durch Auswahl der entsprechenden Preisgestaltung Ebene. Jedoch sobald eine Azure Redis Cache erstellt worden, können nicht Sie erhöhen (seine Größe oder verringern).

- Redis Batches und Transaktionen erstrecken nicht mehrere Verbindungen, so dass alle Daten, die von einem Batch oder einer Transaktion betroffen in der gleichen Datenbank (Splitter) stattfinden sollte.

	> [AZURBLAU. HINWEIS] Eine Folge von Operationen in einer Redis-Transaktion ist nicht notwendigerweise atomar. Die Befehle, die eine Transaktion bilden sind überprüft und in der Warteschlange vor der Ausführung und die gesamte Warteschlange wird verworfen, wenn ein Fehler, während dieser Phase auftritt. Jedoch sobald die Transaktion erfolgreich übermittelt wurde, werden die Warteschlange Befehle nacheinander ausgeführt. Wenn jeder Befehl nur fehlschlägt, dass der Befehl abgebrochen wird; alle vorherigen und nachfolgenden Befehle in der Warteschlange werden durchgeführt. Wenn du atomare Operationen durchführen müssen. Weitere Informationen finden Sie auf der [Transaktionen](http://redis.io/topics/transactions) Seite der Redis-Website.

- Redis unterstützt eine begrenzte Anzahl von atomare Operationen und sind die einzigen Operationen dieser Art, die mehrere Schlüssel und Werte unterstützen, MGET (das eine Auflistung von Werten für eine angegebene Liste der Schlüssel zurückgibt) und MSET (die eine Auflistung von Werten für eine angegebene Liste der Schlüssel speichern kann). Benötigen Sie diese Vorgänge verwenden, müssen der Schlüssel-Wert-Paare von den Befehlen MSET und MGET verwiesen innerhalb derselben Datenbank gespeichert werden.

## Neugewichtung der Partitionen

Wie ein System reift, und das Muster der Nutzung besser verstanden wird, ist es möglich, dass es möglicherweise erforderlich, das Partitionierungsschema anzupassen. Dies könnte aufgrund einzelner Partitionen einen unverhältnismäßig hohen Volumen des Verkehrs anzuziehen und zu heiß, zu übermäßigen Konflikten führt. Darüber hinaus Sie möglicherweise das Datenvolumen in einige Partitionen unterschätzt haben wodurch Sie die Grenzen der Speicherkapazität in diese Partitionen nähern. Unabhängig von der Ursache, ist es manchmal notwendig, Partitionen, um die Belastung gleichmäßiger verteilt auszugleichen.

In einigen Fällen können Daten-Storage-Systeme, die nicht öffentlich den Weg offen legen in dem die Daten auf Servern verteilt werden automatisch ausgleichen, Partitionen innerhalb der Grenzen der verfügbaren Ressourcen. In anderen Situationen ist die Wiederherstellung des Gleichgewichts eine Verwaltungsaufgabe, die aus zwei Phasen besteht:

1. Ermittlung der neuen Partitionierungsstrategie zu prüfen, welche Partitionen werden aufgeteilt (oder evtl. kombiniert) müssen können, und wie Daten zu diesen neuen Partitionen aufgeteilt werden, entwerfen neue Partition Schlüssel.
2. Migrieren Sie die betroffenen Daten von der alten Partitionierungsschema, des neuen Satzes von Partitionen.

> [AZURBLAU. HINWEIS] Die Zuordnung der DocumentDB Sammlungen zu Servern ist transparent, aber erreichen Sie möglicherweise noch die Speichergrenzwerte für die Kapazität und Durchsatz eines DocumentDB ein, in diesem Fall musst du Ihr Partitionierungsschema neu zu gestalten und die Daten migrieren.

Abhängig von der Daten-Storage-Technologie und das Design des Speichersystems Daten können Sie möglicherweise zum Migrieren von Daten zwischen Partitionen während sie (online-Migration) verwendet werden. Wenn dies nicht möglich ist, müssen Sie die betroffenen Partitionen vorübergehend nicht verfügbar machen, während die Daten verlegt (offline-Migration) ist.

## Offline-migration

Offline-Migration ist wohl der einfachste Ansatz, da die Chancen der auftretenden Konflikte reduziert; die zu migrierenden Daten sollten nicht ändern, während es ist verschoben und neu strukturiert.

Im Prinzip umfasst dabei folgende Schritte:

1. Markieren Sie die offline-Splitter,
2. Split/Zusammenführen und die Daten in die neue Splitter verschieben,
3. Die Daten zu überprüfen,
4. Die neuen Splitter online zu bringen,
5. Die alte Splitter zu entfernen.

Um einige Verfügbarkeit zu erhalten, könnte es möglich, die ursprüngliche Splitter in Schritt 1 als Nichtverfügbar machen schreibgeschützt kennzeichnen. Dies würde ermöglichen Anwendungen, die Daten zu lesen, während es verschoben wird, aber nicht ändern.

## Online-migration

Online-Migration ist zum Ausführen komplexer aber weniger störende Benutzer Daten während des gesamten Verfahrens verfügbar bleibt. Der Prozess ist vergleichbar mit denen von offline-Migration, außer dass die ursprüngliche Splitter nicht offline (Schritt 1) gekennzeichnet ist. Je nach der Granularität des Migrationsprozesses (Einzelteil durch Einzelteil oder Scherbe für Scherbe) müssen der Datenzugriffscode in den Clientanwendungen Lesung behandeln und Schreiben von Daten an zwei Standorten (die ursprüngliche Splitter und die neuen Splitter) statt

Ein Beispiel für eine Lösung, die online-Migration unterstützt, finden Sie unter der [Split/Zusammenführen Service für elastische Skala](sql-database-elastic-scale-overview-split-and-merge.md), online auf der Microsoft-Website dokumentiert.

## Ähnliche Muster und Anleitungen

Die folgenden Muster können auch relevant für Ihr Szenario sein, wenn es um Strategien für die Implementierung der Datenkonsistenz:

- Die Daten Konsistenz Anleitung Seite, auf der Microsoft-Website Beschreibt Strategien zur Wahrung der Konsistenz in einer verteilten Umgebung z. B. der Wolke.
- Die [Daten-Partitionierungs-Anleitung](https://msdn.microsoft.com/library/dn589795.aspx) Seite auf der Microsoft-Website bietet eine allgemeine Übersicht der Gestaltung von Partitionen in einer verteilten Lösung verschiedene Kriterien erfüllen.
- Die [Splitter-Muster](https://msdn.microsoft.com/library/dn589797.aspx), auf der Microsoft-Website beschrieben, fasst einige gemeinsamen Strategien für Splitter-Daten.
- Die [Index-Tabelle-Muster](https://msdn.microsoft.com/library/dn589791.aspx) beschrieben auf der Microsoft Webseite Sekundärindizes über Daten erstellen veranschaulicht. Dieser Ansatz ermöglicht Anwendungen das schnelle Abrufen von Daten mithilfe von Abfragen, die nicht den Primärschlüssel einer Auflistung verweisen.
- Die [Materialisierte Ansicht Muster](https://msdn.microsoft.com/library/dn589782.aspx) diskutiert auf der Microsoft Webseite beschreibt die vordefinierten Ansichten zu generieren, die Daten zur Unterstützung der schnellen Abfragevorgänge zusammenfassen. Diese Vorgehensweise kann hilfreich sein in einem partitionierten Datenspeicher wenn die Partitionen mit Daten zusammengefasst werden auf mehrere Standorte verteilt sind.
- Im Artikel Content Delivery Network (CDN) Anleitungen zusätzliche zum Konfigurieren und Verwenden von CDN mit Azure.

## Weitere Informationen

- Die [Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336279.aspx) Seite auf der Microsoft-Website finden Sie ausführliche Dokumentationen beschreiben, wie erstellen und Verwenden von SQL-Datenbanken.
- Die Seite [Übersicht über die elastische Skala von himmelblau SQL-Datenbank](sql-database-elastic-scale-introduction.md) Microsoft bietet die Website eine umfassende Einführung in elastischen Skala.
- Das Thema [Aufteilen und Zusammenfügen mit elastischen Skala](sql-database-elastic-scale-overview-split-and-merge.md) auf der Microsoft-Website enthält Informationen zur Verwendung der Split/Zusammenführen-Dienstes elastische Skala Fragmente zu verwalten.
- Die Seite [Himmelblau-Speicherskalierbarkeit und Performance-Ziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft dokumentiert Webseite Strombegrenzungen Azure Storage Dimensionierung und Durchsatz.
- Die [Entitätsgruppe Transaktionen](https://msdn.microsoft.com/library/azure/dd894038.aspx) Seite auf der Microsoft-Website bietet ausführliche Informationen zum Implementieren von Transaktionsvorgängen über Entitäten in Azure-Tabelle-Speicher gespeichert.
- Der Artikel [Entwerfen eine skalierbare Partitionierungsstrategie für Azure-Tabelle Speicher](https://msdn.microsoft.com/library/azure/hh508997.aspx) auf der Microsoft-Website enthält detaillierte Informationen zum Partitionieren von Daten im Speicher der Azure-Tabelle.
- Die Seite [Verwendung von CDN für Azure](cdn-how-to-use.md) auf der Microsoft beschreibt Webseite zum Replizieren von Daten in Azure Blob Storage mithilfe von Azure Content Delivery Network (CDN) statt.
- Die Seite [DocumentDB Grenzwerte für die Preview-Version](documentdb-limits.md) auf der Microsoft beschreibt die Webseite der Strombegrenzungen und Quoten für Microsoft DocumentDB.
- Die Seite [DocumentDB Kapazität und Performance zu verwalten](documentdb-manage.md) auf der Microsoft-Website enthält Informationen wie Azure DocumentDB Ressourcen, um Datenbanken reserviert.
- Die [Himmelblau-Suche-Übersicht](https://msdn.microsoft.com/library/azure/dn798933.aspx) Seite auf der Microsoft-Website enthält eine vollständige Beschreibung der Funktionen mit den Azure-Suchdienst zur Verfügung.
- Die [Grenzen und Einschränkungen (himmelblau-Such-API)](https://msdn.microsoft.com/library/azure/dn798934.aspx) Seite auf der Microsoft-Website enthält Informationen über die Kapazität jeder Instanz der Azure-Suchdienst.
- Die [Unterstützte Datentypen (himmelblau-Suche)](https://msdn.microsoft.com/library/azure/dn798938.aspx) Seite auf der Microsoft-Website fasst die Datentypen, die Sie in durchsuchbare Dokumente und Indizes verwenden können.
- Die [Microsoft Azure-Cache](http://azure.microsoft.com/services/cache.md) Seite auf der Microsoft-Website enthält eine Einführung in Azure Redis Cache.
- Die Seite [Partitionierung: wie Daten zwischen mehreren Redis-Instanzen geteilt](http://redis.io/topics/partitioning) über die Redis bietet die Website Informationen zum Implementieren der Partitionierung mit Redis.
- Die Seite [Redis auf einem CentOS Linux VM in Azure ausgeführt](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) auf der Microsoft exemplarisch Webseite ein Beispiel zum Erstellen und Konfigurieren eines Redis-Knotens als einer Azure-VM ausgeführt.
- Die [Datentypen](http://redis.io/topics/data-types) Seite der Redis-Website beschreibt die Datentypen, die Redis und Azure Redis Cache verfügbar sind.
