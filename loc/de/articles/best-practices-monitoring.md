<properties
   pageTitle="Monitoring and diagnostics guidance | Microsoft Azure"
   description="Best practices for monitoring distributed applications in the cloud."
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

# Überwachung und Diagnose-Leitlinien

![](media/best-practices-monitoring/pnp-logo.png)

## Übersicht
Verteilte Anwendungen und Service in der Cloud ausgeführt sind, naturgemäß, komplexe Teile der Software, die viele bewegliche Teile umfassen. In einer Produktionsumgebung ist es wichtig, die Art und Weise zu verfolgen, in der Benutzer Ihr System, Spur Ressourcenauslastung nutzen, und allgemein die Gesundheit und Leistungsfähigkeit Ihres Systems zu überwachen. Diese Informationen können verwendet werden als diagnostische Hilfsmittel zu erkennen und beheben Sie Probleme, und auch auf mögliche Probleme vor Ort zu helfen und sie zu verhindern.

## Überwachung und Diagnose-Szenarien
Überwachung ermöglicht Ihnen einen Einblick in wie gut ein System funktioniert, und ist maßgeblich an der Aufrechterhaltung Quality-of-Service-Ziele. Einige typische Szenarien für das Sammeln von monitoring-Daten umfassen:

- Sicherstellen, dass das System gesund bleibt.
- Verfolgen die Verfügbarkeit des Systems und seiner Bestandteile.
- Sicherstellen der Systemleistung um sicherzustellen, dass der Durchsatz des Systems als das Volumen der Arbeit steigt unerwartet nicht beeinträchtigt.
- Gewährleisten, daß das System erfüllt alle SLAs mit den Kunden vereinbarten.
- Schutz der Privatsphäre und die Sicherheit des Systems, Benutzer und deren Daten.
- Verfolgen die Vorgänge, die Überwachung durchgeführt werden oder aufsichtsrechtliche Zwecke.
- Überwachung der täglichen Gebrauch von dem System und Hilfe Aufspüren von Trends, die zu Problemen führen könnte, wenn sie nicht behandelt werden.
- Nachverfolgen von Problemen, die auftreten, vom ersten Bericht durch Analyse der möglichen Ursachen, Berichtigung, konsequente Softwareupdates und Bereitstellung.
- Operationen Ablaufverfolgung und Debuggen von Software-Releases.

> [AZURBLAU. HINWEIS] Diese Liste erhebt keinen Anspruch auf Vollständigkeit erhebt. Dieses Dokument konzentriert sich auf diese Szenarien wie die häufigsten Situationen zur Durchführung, Überwachung, aber möglicherweise gibt es auch andere, die weniger häufig oder für Ihre eigene Umgebung spezifisch sind.

In den folgenden Abschnitten werden diese Szenarien genauer beschrieben. Die Informationen für jedes Szenario ist im folgenden Format beschrieben:

- Eine kurze Übersicht über das Szenario.
- Die typischen Anforderungen dieses Szenarios.
- Die rohen Ausstattungsdaten erforderlich, um das Szenario und die möglichen Quellen dieser Informationen unterstützen.
- Wie können diese Rohdaten analysiert und kombiniert, um aussagekräftige Diagnoseinformationen zu generieren.

## Systemüberwachung
Ein System ist gesund, wenn es ausgeführt wird und Anforderungen verarbeiten kann. Die Gesundheitsberichterstattung soll generiert einen Snapshot der aktuellen Gesundheit des Systems, mit denen Sie überprüfen, ob alle Komponenten des Systems funktionieren wie erwartet.

### Anforderungen für die Gesundheitsüberwachung
Ein Operator sollte schnell (innerhalb von wenigen Sekunden) gewarnt, wenn ein Teil des Systems als ungesund ist. Der Betreiber sollte in der Lage, festzustellen, welche Teile des Systems sind normal funktioniert und welche Teile Probleme auftreten. Systemintegrität kann mit einem Ampelsystem hervorgehoben werden; rot für ungesunde (das System hat aufgehört), gelb für teilweise gesund (das System mit eingeschränkter Funktionalität ausgeführt wird), und grün für völlig gesund.

Eine umfassende Systemüberwachung ermöglicht einen Operator Drilldown durch das System um den Gesundheitszustand von Subsystemen und Komponenten anzuzeigen. Beispielsweise wenn das Gesamtsystem als teilweise fehlerfrei dargestellt ist, sollte der Betreiber möglicherweise vergrößern und bestimmen, welche Funktionen derzeit nicht verfügbar ist.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Die Rohdaten zur Systemüberwachung Unterstützung können infolge der generiert werden:

- Ausführung von Benutzeranforderungen Ablaufverfolgung. Diese Informationen können verwendet werden, um zu bestimmen, welche Anforderungen gelungen, die versagt haben und jeder Anforderung wie lange dauert.
- Synthetische Anwenderüberwachung. Dieser Vorgang simuliert die Schritte von einem Benutzer ausgeführt und eine vordefinierte Reihe von Schritten folgt. Die Ergebnisse der einzelnen Schritte sollten erfasst werden.
- Protokollieren von Ausnahmen, Fehler und Warnungen. Diese Informationen kann aufgrund der Trace-Anweisungen in den Anwendungscode eingebettet sowie das Abrufen von Informationen aus den Ereignisprotokollen Dienste verwiesen vom System erfasst werden.
- Überwachung der Integrität von Drittanbieter-Dienste vom System verwendet. Dies erfordert möglicherweise abrufen und Analysieren von Gesundheitsdaten, die von diesen Diensten geliefert könnte, und diese Informationen eine Vielzahl von Formaten.
- Endpunkt zu überwachen. Dieser Mechanismus wird in die Verfügbarkeitsüberwachung Abschnitt genauer beschrieben.
- Sammeln umgebende Leistungsinformationen wie Hintergrund CPU-Auslastung oder I/O (einschließlich Netzwerk) Aktivität.

### Analyse von Gesundheitsdaten
Der Schwerpunkt der Gesundheitsberichterstattung ist schnell angeben, ob das System ausgeführt wird. Heißen Analyse der sofortigen Daten kann eine Warnung auslösen, wenn eine kritische Komponente erkannt wird, um zu ungesund (es kann zum Beispiel eine aufeinanderfolgende Reihe von Pings, reagieren). Der Betreiber kann dann die entsprechende Korrekturmaßnahmen ergreifen.

A more advanced system might include a predictive element that performs a cold analysis over recent and current workloads to spot trends and determine whether the system is likely to remain healthy or whether additional resources are going to be required. This predictive element should be based on critical performance metrics, such as the rate of requests directed at each service or subsystem, the response times of these requests, and the volume of data flowing into and out of each service. If the value of any metric exceeds a defined threshold the system can raise an alert to enable an operator or auto-scaling (if available) to take the preventative actions necessary to maintain system health. These actions might involve adding resources, restarting one or more services that are failing, or applying throttling to lower-priority requests.

## Überwachung der Verfügbarkeit
Ein wirklich gesundes System erfordert, dass die Komponenten und Subsysteme, die das System bilden zur Verfügung stehen. Verfügbarkeitsüberwachung ist eng mit der Gesundheitsberichterstattung, aber Systemüberwachung eine sofortige Ansicht der aktuellen Gesundheit des Systems sieht, Verfügbarkeitsüberwachung befasst sich mit Überwachung der Verfügbarkeit des Systems und seiner Komponenten, Statistiken über die Uptime des Systems zu generieren.

In vielen Systemen werden einige Komponenten (z. B. eine Datenbank) mit integrierte Redundanz ermöglichen schnelles Failover im Falle einer schweren Störung oder Verlust der Konnektivität konfiguriert. Im Idealfall sollten Benutzer nicht bewusst sein, dass solch ein Fehler aufgetreten, aber aus einer Perspektive-Verfügbarkeitsüberwachung es notwendig ist, sammeln so viele Informationen wie möglich über solche Fehler zu versuchen, die Ursache zu ermitteln und Durchführen von Korrekturmaßnahmen zur Vorbeugung von wiederkehrenden.

Die erforderlichen Verfügbarkeit Daten möglicherweise abhängig von einer Reihe von untergeordneten Faktoren, von die viele spezifisch für die Anwendungs-, System- und Umgebung sein können. Ein effektives monitoring-System erfasst die Verfügbarkeitsdaten entspricht diese Low-Level-Faktoren und dann fasst sie um ein Gesamtbild des Systems zu geben. Beispielsweise kann in einem e-Commerce-System, die Business-Funktionen, die ein Kunde Bestellungen aufgeben kann abhängen, auf dem Repository in die Auftragsdetails gespeichert sind und das Zahlungssystem, das den Zahlungsverkehr für die Zahlung für diese Aufträge behandelt. Die Verfügbarkeit der Auftragserteilung Teil des Systems ist daher eine Funktion der Verfügbarkeit des Repositorys und die-Sub-Zahlungssystem.

### Anforderungen für die Überwachung der Verfügbarkeit
Ein Operator sollte auch in der Lage, die historische Verfügbarkeit jedes System- und Subsystem und verwenden diese Informationen, um Trends zu entdecken, die mindestens ein Subsystem in regelmäßigen Abständen fehlschlagen verursachen. (Dienste Starte nicht zu einem bestimmten Zeitpunkt des Tages, die Stoßzeiten Verarbeitung entspricht?)

Sowie die unmittelbare und historischen Blick auf die Verfügbarkeit von jedes Teilsystem eine Überwachungslösung sollte auch fähig schnell einen Operator, wenn eine Alarmierung oder mehr Dienste fehlschlagen oder Benutzer können keine Verbindung zu Diensten. Dies ist nicht einfach eine Frage der Überwachung der einzelnen Dienste, aber auch die Aktionen, die von jedem Benutzer durchgeführt werden, wenn diese Maßnahmen fehlschlagen, wenn sie versuchen, die Kommunikation mit einem Dienst untersuchen. In gewissem Umfang eine gewisse Konnektivitätsfehler ist normal und möglicherweise aufgrund vorübergehender Fehler, aber es kann nützlich sein, kann das System eine Warnung von der Anzahl der Verbindungen zu erhöhen, die während eines bestimmten Zeitraums zu einem angegebenen Subsystem Ausfällen.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Wie bei der Systemüberwachung, können die Rohdaten zur Verfügbarkeitsüberwachung Unterstützung generiert werden, weil synthetische Benutzer Überwachung und Protokollierung, Ausnahmen, Fehler und Warnungen werden können. Darüber hinaus Daten zur Verfügbarkeit erhalten Sie vom Endpunkt Überwachung durchführen. Die Anwendung kann aussetzen, einen oder mehrere Endpunkte der Gesundheit, jeder Test Zugang zu einem Funktionsbereich innerhalb des Systems. Das monitoring-System kann jeder Endpunkt nach einem festen Zeitplan ping und sammeln die Ergebnisse (Erfolg oder Scheitern).

Alle Timeouts und Netzwerkfehler Konnektivität und Verbindungsversuche wiederholen müssen aufgezeichnet werden. Alle Daten sollten mit einem Zeitstempel versehen werden.

<a name="analyzing-availablity-data"></a>
### Analyse von Daten zur Verfügbarkeit
Die Instrumentationsdaten aggregiert und korreliert, um folgenden Arten von Analysen zu unterstützen:

- Die sofortige Verfügbarkeit des Systems und Subsysteme.
- Die Ausfallraten für die Verfügbarkeit des Systems und Subsysteme. Idealerweise sollte der Luftfahrtunternehmer Fehler korrelieren mit bestimmten Tätigkeiten können; Was passierte, wenn das System versagt?
- Eine historische Ansicht der Ausfallraten des Systems oder alle Subsysteme über irgendwelche angegeben Zeitraum und das Laden auf dem System (Anzahl der Benutzeranforderungen z. B.) Wenn ein Fehler aufgetreten.
- Die Gründe für die Nichtverfügbarkeit des Systems oder alle Subsysteme. Z. B.-Dienst nicht ausgeführt, Verlust der Konnektivität, verbunden aber Timeout und verbunden aber wiederkehrenden Störungen.

Sie können die prozentuale Verfügbarkeit eines Dienstes über einen längeren Zeitraum mithilfe der Formel berechnen:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Dies ist nützlich für SLA Zwecke)[SLA-Überwachung](#SLA-monitoring) ist ausführlicher weiter unten in dieser Anleitung beschrieben). Die Definition der _Ausfallzeiten_ abhängig vom Dienst. Z. B. definiert Visual Studio Online Ausfallzeiten als der Zeitraum, während, den ein Kunde Versuche zum Herstellen einer Verbindung mit des Diensts länger als 120 Sekunden und alle grundlegenden Lese- und Schreib-Operationen fehlschlagen, dauert nachdem die Verbindung, innerhalb dieser Frist hergestellt ist.

## Performance-Überwachung
Als System wird platziert unter mehr Stress als das Volumen der Benutzer erhöhen und die Größe der Datensätze, dass diese Benutzer, die Zugriff wächst, der mögliche Ausfall einer oder mehrerer Komponenten wahrscheinlich wird. Häufig ist eine Abnahme der Leistung Komponentenausfall vorangestellt. Wenn Sie können erkennen Sie, solch eine Abnahme Sie präventive Maßnahmen Abhilfe zu können.

System-Performance ist eine Reihe von Faktoren abhängig. Jeder Faktor wird in der Regel mithilfe von Key Performance Indicators (KPIs), gemessen, z. B. die Anzahl der Datenbanktransaktionen pro Sekunde oder das Volumen der Netzwerkanforderungen, die in einem bestimmten Zeitrahmen erfolgreich bedient werden. Einige der diese KPIs möglicherweise als spezifische Performance-Maßnahmen zur Verfügung, während andere aus einer Kombination von Kennzahlen abgeleitet werden können.

> [AZURBLAU. HINWEIS] Bestimmung schlechte oder gute Leistung erfordert, dass Sie das Leistungsniveau verstehen, bei der das System in der Lage sein sollte. Dies erfordert das System zu beobachten, während sie bei einer typischen Auslastung funktioniert und erfassen die Daten für jeden KPI über einen längeren Zeitraum. Dabei wird z. B. das System unter simulierter Belastung in einer Testumgebung ausgeführt und die entsprechenden Daten zu sammeln, bevor Sie das System in einer Produktionsumgebung bereitstellen.

> Sie sollten außerdem sicherstellen, dass Überwachung Leistung Zwecken keine ungerechtfertigte Belastung für das System geworden. Möglicherweise können die dynamische Anpassung der detailliert über die Daten, die der Prozess für die Leistungsüberwachung sammelt.

### Anforderungen für die Leistungsüberwachung
Um die Systemleistung zu prüfen, müssten ein Operator in der Regel einschließlich Informationen finden Sie unter:

- Die Response-Raten für Benutzeranforderungen.
- Die Anzahl der gleichzeitigen Benutzeranforderungen.
- Die Menge des Netzwerkverkehrs.
- Die Preise verstehen sich bei welcher, die Business Transaktionen fertig gestellt sind.
- Die durchschnittliche Bearbeitungszeit für Anfragen.

Es kann auch hilfreich sein, Tools bereitstellen, mit die einen Operator vor Ort Korrelationen, wie z. B. helfen können:

- Die Anzahl der gleichzeitigen Benutzer im Vergleich verlangen Wartezeiten (wie lange dauert es, starten eine Anforderung verarbeitet, nachdem der Benutzer es geschickt hat).
- Die Anzahl der gleichzeitigen Benutzer gegen die durchschnittliche Antwortzeit (wie lange dauert es, um eine Anfrage zu erledigen, nachdem es mit der Verarbeitung begonnen hat).
- Das Volumen der Anfragen im Vergleich zur Anzahl von Verarbeitungsfehlern.

Sowie diese allgemeine Informationen zur funktionalen sollte ein Operator auch eine detaillierte Ansicht der Leistung für jede Komponente im System erhalten können. Diese Daten werden in der Regel mit niedriger Leistung Leistungsindikatoren tracking-Informationen bereitgestellt:

- Speicher-Auslastung
- Anzahl der Threads,
- CPU-Bearbeitungszeit
- Anfrage-Warteschlangenlänge
- Datenträger oder Netzwerk I/O Preise und Störungen,
- Anzahl der Bytes geschrieben oder gelesen,
- Middleware-Indikatoren, wie Warteschlangenlänge.

Alle Visualisierungen dürfte einen Operator um einen Zeitraum anzugeben; angezeigten Daten könnte eine Momentaufnahme der aktuellen Situation und/oder einen historischen Überblick über die Performance.

Ein Operator sollte eine Warnung, die Grundlage jeder Kennzahl für jeden gegebenen Wert während einer angegebenen Zeitintervalls auslösen können.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Die Leistung auf hohem Niveau-Daten (Durchsatz, Anzahl gleichzeitiger Benutzer, Anzahl der Geschäftsvorfälle, Fehlerraten, usw.) können mit der Überwachung der Fortschritte der Nutzer-Anforderungen, wie sie kommen und durch das System gehen gesammelt werden. Einbeziehung von Ablaufverfolgungsanweisungen an Schlüsselpunkten im Anwendungscode zusammen mit Timing-Informationen dazu. Alle Fehler, Ausnahmen und Warnungen sollten erfasst werden, mit genügend Daten, damit sie sich mit den Anträgen korreliert werden, die sie verursacht. Der IIS-Protokolldatei ist eine weitere nützliche Informationsquelle.

Wenn möglich, sollten Sie auch Performance-Daten für jede externe Systeme erfassen, die die Anwendung verwendet. Dieser externen Systeme möglicherweise eigene Leistungsindikatoren oder andere Features bieten, zum Anfordern von Leistungsdaten. Wenn dies nicht möglich ist, sollten Sie Informationen wie die Startzeit und Endzeit der jede Anforderung an ein externes System, zusammen mit dem Status des Vorgangs (Erfolg, Fehler oder Warnung) aufzeichnen. Beispielsweise können Sie einen Stoppuhr-Ansatz zur Zeit Anfragen; Starten Sie einen Zeitgeber ausgeführt, wenn die Anforderung gestartet wird und dann stoppen Sie den Timer, wenn die Anforderung abgeschlossen ist.

Low-Level-Performance-Daten für die einzelnen Komponenten in einem System möglicherweise durch Features wie Windows-Leistungsindikatoren und azurblauen Diagnose verfügbar.

### Analysieren von Leistungsdaten
Die Analyse-Arbeit besteht aus Aggregieren von Performance-Daten vom Benutzer Anfrage Typ (z. B. Hinzufügen eines Elements zu einem Einkaufswagen oder durchführen der Kaufabwicklung eines Ecommerce-Systems) und/oder Subsystem oder Dienst an den jede Anforderung gesendet wird.

Eine andere allgemeine Anforderung ist Leistungsdaten in ausgewählten Perzentile zusammenfassen. Beispielsweise bestimmen die Antwortzeiten für 99 % der Anfragen, 95 % der Anfragen und 70 % der Zugriffe. Es kann SLA-Ziele oder andere Ziele zu setzen, für jede Perzentil. Die laufenden Ergebnisse gemeldet werden sollte, in den beiden nahezu Echtzeit um sofortige Probleme zu erkennen, als auch über längere Zeit zu statistischen Zwecken aggregiert wird.

Im Falle Wartezeitprobleme Beeinträchtigung der Leistung sollte ein Betreiber schnell ermitteln der Ursache für den Engpass durch untersuchen die Latenz der einzelnen Schritte durchgeführt, die für jede Anforderung können. Die Performance-Daten müssen daher ein Mittel zur Korrelation von Leistungskennzahlen für jeden Schritt um sie zu binden, um eine bestimmte Anforderung bereitstellen.

Je nach den Anforderungen der Visualisierung kann es generieren und Speichern eines Datencubes, die mit Blick auf die Rohdaten ermöglichen komplexe Ad-hoc-Abfragen und Analyse von Performance-Informationen nützlich sein.

## Sicherheitsüberwachung
Alle kommerzielle Systemen, die sensible Daten enthalten, müssen eine Sicherheitsstruktur implementieren. Die Komplexität der Sicherheitsmechanismus ist in der Regel eine Funktion der Sensibilität der Daten. In einem System, die Benutzer authentifiziert werden muss, sollten Sie alle Login-Versuche, aufnehmen, ob sie scheitern oder gelingen. Darüber hinaus alle Operationen durchgeführt und die Details aller Ressourcen durch einen authentifizierten Benutzer zugegriffen protokolliert werden sollen. Wenn der Benutzer die Sitzung beendet und abmeldet, sollten diese Informationen auch aufgezeichnet werden.

Überwachung können zur Erkennung von Angriffen auf das System unterstützen. Beispielsweise eine große Anzahl von fehlgeschlagenen Anmeldeversuchen könnte einen Brute-Force-Angriff hinweisen, oder ein unerwarteter Anstieg der Anfragen wäre das Ergebnis einer DDoS-Attacke. Sie müssen bereit sein, um alle Anforderungen an alle Ressourcen unabhängig von der Quelle dieser Anforderungen zu überwachen; ein System mit einer Anmeldung-Schwachstelle kann versehentlich Ressourcen zur Außenwelt ohne aussetzen, die eigentlich ein Benutzer anmeldet.

### Anforderungen für die Sicherheitsüberwachung
Die wichtigsten Aspekte der Sicherheitsüberwachung sollte schnell einen Operator zu aktivieren:

- Erkennen Sie Einbruchsversuche, indem eine nicht authentifizierte Entität,
- Identifizieren Sie die Versuche von Entitäten auf Daten Vorgänge durchführen, für die sie kein Zugriff gewährt haben,
- Bestimmen, ob das System oder ein Teil des Systems, unter Beschuss von außen oder innen ist (z. B. ein böswilliger Benutzer-Authentifizierung möglicherweise versuchen werden, das System stürzen).

Um diese Anforderungen zu unterstützen, sollte ein Operator benachrichtigt werden:

- Jeder wiederholte fehlgeschlagene Anmeldeversuche, die von demselben Konto innerhalb einer bestimmten Zeitspanne gemacht.
- Wenn das gleiche Konto wiederholt authentifiziert versucht, eine verbotene Ressource zuzugreifen, während eines bestimmten Zeitraums.
- Wenn eine große Anzahl von nicht authentifizierten oder nicht autorisierte Zugriffe während eines bestimmten Zeitraums auftreten.

Die Angaben zu einem Betreiber sollte die Host-Adresse der Quelle für jede Anforderung enthalten. Wenn Sicherheitsverstöße regelmäßig aus einem bestimmten Adressbereich auftreten, könnten diese Hosts geblockt werden.

A key part in maintaining the security of a system is being able to quickly detect actions that deviate from the usual pattern. Information such as the number of failed and/or successful login requests can be displayed visually to help detect whether there is a spike in activity at an unusual time (such as users logging in at 3am and performing a large number of operations when their working day starts at 9am). This information can also be used to help configure time-based autoscaling, For example, if an operator observes that a large number of users regularly log in at a particular time of day, the operator can arrange to start additional authentication services to handle the volume of work, and then shut these additional services down when the peak has passed.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Sicherheit ist ein allumfassendes Aspekt von den meisten verteilten Systemen und relevanten Daten werden voraussichtlich an mehreren Punkten im gesamten System generiert werden. Sie sollten einen Security Information und Event Management (SIEM) Ansatz um die Sicherheitsinformationen, infolge von der Anwendung, Netzwerktechnik, Server, Firewalls, Antivirensoftware und andere Eindringen Prävention Elemente ausgelöste Ereignisse zu sammeln.

Sicherheitsüberwachung kann Daten aus Tools integrieren, die nicht Teil der Anwendung, z. B. Dienstprogramme, die identifizieren, Port Scan Aktivitäten durch externe Agenturen oder Netzwerk-Filter, die Versuche nicht authentifizierten Zugriff auf Ihre Anwendungen und Daten zu erkennen.

In allen Fällen müssen die gesammelten Daten kann ein Administrator bestimmen Sie die Art eines Angriffs und geeigneten Gegenmaßnahmen ergreifen.

### Analyse der Sicherheitsdaten
Ein Merkmal der Sicherheitsüberwachung ist die Vielfalt der Quellen, aus denen die Daten entsteht. Die verschiedenen Formate und Detailebene erfordern häufig komplexe Analyse der Daten erfasst werden, um es in einen kohärenten Thread Informationen zusammenzufassen. Abgesehen von den einfachsten Fall (z. B. eine große Anzahl von fehlgeschlagenen Anmeldungen oder wiederholte Versuche, nicht autorisierten Zugriff auf wichtige Ressourcen erkennen), es möglicherweise nicht möglich sein, komplexe automatisierte Verarbeitungen der Sicherheitsdaten auszuführen, und stattdessen möglicherweise besser zum Schreiben dieser Daten, aber sonst in seiner ursprünglichen Form, um ein sicheres Repository für manuelle Expertenanalyse ermöglichen mit einem Zeitstempel versehen.

<a name="SLA-monitoring"></a>

## SLA-Überwachung
Viele kommerzielle Systeme, die zahlende Kunden unterstützen mache Garantien über die Leistung des Systems in Form von SLAs. Im wesentlichen besagen SLAs, dass das System eine bestimmte Menge Arbeit innerhalb eines vereinbarten Zeitrahmens und ohne Verlust kritischer Daten verarbeiten kann. SLA-Überwachung befasst sich mit der Sicherstellung, dass das System messbar SLAs erfüllen kann.

> [AZURBLAU. HINWEIS] SLA-Überwachung ist eng mit der Leistungsüberwachung, aber während die Leistungsüberwachung betroffen ist, mit der sichergestellt wird, dass der Systemfunktionen _optimal_, SLA-Überwachung unterliegt, eine vertragliche Verpflichtung, die definiert, was _optimal_ eigentlich bedeutet.

SLAs werden häufig in Form von definiert:

- Verfügbarkeit des gesamten Systems. Beispielsweise kann eine Organisation garantieren, dass das System für 99,9 % der Zeit verfügbar ist; Dies entspricht nicht mehr als 9 Stunden Ausfallzeit pro Jahr oder ca. 10 Minuten pro Woche.
- Operativen Durchsatz. Dieser Aspekt wird häufig ausgedrückt als ein oder mehrere Schlüssel – Hochwassermarken, wie garantieren, dass das System in der Lage, bis zu 100.000 concurrent-User-Supportanfragen oder 10.000 gleichzeitige Geschäftstransaktionen zu behandeln.
- Operativen Antwortzeit. Das System kann auch Garantien hinsichtlich der Rate während, die, die Anforderungen verarbeitet werden, wie 99 % aller Geschäftsvorgänge innerhalb von 2 Sekunden abgeschlossen wird, und keine einzelne Transaktion dauert länger als 10 Sekunden.

> [AZURBLAU. HINWEIS] Einige Verträge für kommerzielle Systeme können auch gehören, SLAs betreffend Kunden-Support, wie alle Helpdesk-Anfragen eine Antwort innerhalb von 5 Minuten entlocken werden, und 99 % aller Probleme innerhalb eines Tages vollständig behandelt werden. Effektive [Issue-tracking](#issue-tracking) (später in diesem Abschnitt beschriebenen) ist der Schlüssel zur Erfüllung von SLAs wie diese.

### Anforderungen für SLA-Überwachung
Auf höchstem Niveau sollte ein Operator auf einen Blick feststellen, ob das System die vereinbarten SLAs oder nicht gerecht wird, und wenn nicht dann, drill-down und die zugrunde liegenden Faktoren um zu bestimmen, die Gründe für eine minderwertige Leistung zu überprüfen.

Typische allgemeine Indikatoren, die visuell dargestellt werden können gehören:

- Der Prozentsatz der Service-Verfügbarkeit.
- Die Anwendung-Durchsatz (gemessen in erfolgreiche Transaktionen und/oder Operationen pro Sekunde).
- Die Anzahl der erfolgreichen/fehlerhafte Anwendungsanforderungen.
- Die Anzahl der Anwendungs- und Fehlern, Ausnahmen und Warnungen.

Alle diese Indikatoren sollten durch einen angegebenen Zeitraum gefiltert werden können.

Eine Wolke-Anwendung wird wahrscheinlich eine Reihe von Subsystemen und Komponenten umfassen. Ein Operator sollte wählen einen High-Level-Indikator und sehen, wie sie von der Gesundheit der zugrunde liegenden Elemente besteht. Beispielsweise wenn die Verfügbarkeit des Gesamtsystems ein zulässiger Wert unterschreitet, sollte der Luftfahrtunternehmer möglicherweise vergrößern und bestimmen, welche Elemente Fehler in diesem Beitrag sind.

> [AZURBLAU. HINWEIS] Die Verfügbarkeit der Systeme muss sorgfältig definiert werden. In einem System, das Redundanz verwendet, um maximale Verfügbarkeit sicherzustellen, einzelne Instanzen von Elementen können fehlschlagen, aber das System funktionsfähig bleiben kann. Systemverfügbarkeit von Systemüberwachung vorgelegte sollte angeben, die aggregierte Uptime jedes Elements und nicht unbedingt, ob das System tatsächlich angehalten hat. Außerdem Fehler eventuell isoliert, so auch wenn ein bestimmtes System nicht verfügbar ist der Rest des Systems zur Verfügung, bleiben könnte zwar mit Funktionalität verringert (in einem e-Commerce-System ein Fehler im System verhindern möglicherweise ein Kunde Bestellungen, aber der Kunde möglicherweise trotzdem Produktkatalog durchsuchen.)

Alarmierungsbereich, sollte das System möglicherweise ein Ereignis auslösen, wenn einer der hochrangigen Indikatoren einen bestimmten Schwellenwert überschreitet. Die Low-Level Details der verschiedenen Faktoren, die den High-Level-Indikator enthalten sollte als kontextbezogene Daten an das Warnungssystem verfügbar.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Die Rohdaten zur Unterstützung von SLA-Überwachung erforderlich ist, erforderlich für die Leistungsüberwachung zusammen mit einigen Aspekten der Gesundheit und Verfügbarkeitsüberwachung ähnlich (siehe die Abschnitte für weitere Details). Sie können diese Daten durch aufzeichnen:

- Durchführung, Überwachung der Endpunkt.
- Protokollieren von Ausnahmen, Fehler und Warnungen.
- Ausführung von Benutzeranforderungen Ablaufverfolgung.
- Überwachung der Verfügbarkeit von Drittanbietern Dienste vom System verwendet.
- Verwenden von Performance-Kennzahlen und Leistungsindikatoren.

Alle Daten müssen zeitlich und mit einem Zeitstempel versehen werden.

### SLA-Datenanalyse
Die Ausstattungsdaten müssen ein Bild über die Gesamtleistung des Systems zu generieren und Drilldown zum Aktivieren der Prüfung der Leistungsfähigkeit der zugrunde liegenden Teilsysteme aggregiert werden. Beispielsweise sollten Sie in der Lage sein:

- Die Gesamtzahl der Benutzeranforderungen während eines bestimmten Zeitraums zu berechnen, und bestimmt den Erfolg und Misserfolg Zinsfuß dieser Anforderungen.
- Kombinieren Sie die Reaktionszeiten der Benutzeranforderungen, einen Gesamtüberblick über die Reaktionszeiten des Systems zu generieren.
- Analysieren Sie den Fortschritt der Benutzer Anforderungen Pause die gesamte Antwortzeit eine bestimmte Anforderung nach unten in die Ansprechzeiten der einzelnen Arbeitsaufgaben in dieser Anforderung.  
- Die allgemeine Verfügbarkeit des Systems als eine prozentuale Verfügbarkeit für einen bestimmten Zeitraum zu ermitteln.
- Analysieren Sie die prozentuale Zeit Verfügbarkeit der einzelnen der einzelnen Komponenten und Dienste im System. Das kann bedeuten, Analysieren von Protokollen, die Generierung von Leistungen Dritter.

Viele kommerzielle Systeme sind erforderlich, um echte Leistung Zahlen anhand der vereinbarten SLAs für einen bestimmten Zeitraum, in der Regel einen Monat zu melden. Diese Informationen können verwendet werden, um Kredite oder andere Formen der Rückzahlungen für Kunden zu berechnen, wenn die SLAs nicht während dieses Zeitraums erfüllt sind. Sie können die Verfügbarkeit für einen Dienst berechnen mithilfe der im Abschnitt beschriebenen Technik [Analyse von Daten zur Verfügbarkeit](#analyzing-availability-data).

Für interne Zwecke könnte eine Organisation auch Länge, Anzahl und Art der Vorfälle, die Dienste fehlschlagen verursacht. Lernen, wie man diese Probleme schnell zu lösen, oder vollständig zu beseitigen hilft, die Ausfallzeiten und die Einhaltung der SLAs.

## Überwachung
Je nach Art der Anwendung möglicherweise gibt es gesetzliche oder andere gesetzliche Regelungen, die Anforderungen für die Überwachung der Verarbeitungsschritte von Nutzern und Aufnahme aus, die alle Daten angeben. Überwachung kann Kunden mit spezifischen Anforderungen verknüpfen nachweisen; Nichtabstreitbarkeit ist ein wichtiger Faktor in vielen e-Business-Systemen zur Aufrechterhaltung des Vertrauens zwischen dem Kunden und der Organisation verantwortlich für die Anwendung oder ein Dienst sein.

### Anforderungen für die Überwachung
Analyst muss die Abfolge der Geschäftstätigkeit von Benutzern durchgeführt werden, so dass Sie Benutzeraktionen rekonstruieren können zu verfolgen können. Dies kann einfach als eine Frage des Datensatzes oder im Rahmen einer forensischen Untersuchung notwendig sein.

Audit Informationen sind hochsensible, da wird es wahrscheinlich Daten enthalten, die die Benutzer des Systems zusammen mit den Aufgaben identifiziert, die sie durchführen. Aus diesem Grund ist es sehr wahrscheinlich, dass die Audit-Daten werden in Form von Berichten verfügbar nur für vertrauenswürdige Analysten visualisiert werden, anstatt mit einem interaktiven System unterstützt, die Drilldown-grafische Operationen. Ein Analyst sollte erzeugen eine Reihe von Berichten, z. B. Auflisten aller Benutzer-Aktivitäten, die während eines vorgegebenen Zeitrahmens, detailliert die Chronologie der Aktivität für einen einzelnen Benutzer oder auflisten die Folge-Operationen gegen eine oder mehrere Ressourcen durchgeführt.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Die Hauptinformationsquellen für die Überwachung können einschließen:

- Das Sicherheitssystem, das Benutzerauthentifizierung verwaltet.
- Verfolgen Sie Protokolle, die Aufnahme der Benutzeraktivität.
- Sicherheitsprotokolle verfolgen alle erkennbar und nicht identifizierbare Netzwerkanforderungen.

Das Format der Audit-Daten und die Art und Weise, in der es gespeichert ist, möglicherweise gesetzlichen Anforderungen betrieben werden. Z. B. möglicherweise nicht möglich, die Daten in irgendeiner Weise zu reinigen (im Originalformat gespeichert) und Zugriff auf das Repository, in dem es gehalten wird, geschützt werden muss, um Manipulationen zu verhindern.

### Audit-Daten analysieren
Ein Analyst muss die Rohdaten in seiner Gesamtheit in seiner ursprünglichen Form zugreifen können. Abgesehen von der Verpflichtung zur gemeinsamen generieren audit Berichte, Instrumenten zur Analyse dieser Daten werden voraussichtlich spezialisiert und externen System gehalten werden.

## Auslastungs-monitoring
Auslastungs-monitoring verfolgt, wie die Features und Komponenten von einer Anwendung verwendet werden. Die erfassten Daten können genutzt werden, um:

- Bestimmen Sie, welche Features sind stark genutzt und jeder potenzielle Hotspots im System zu bestimmen. Hochverkehr Elemente konnten von funktionalen Partitionierung oder sogar Replikation die Auslastung gleichmäßiger verteilen profitieren. Diese Informationen kann auch verwendet werden, um festzustellen, welche Features werden selten verwendet und sind mögliche Kandidaten für die Stilllegung oder Ersatz in einer zukünftigen Version des Systems.
- Erhalten Sie Informationen über die betriebliche Ereignisse im System unter normalem Gebrauch. Z. B. in e-Commerce-Website könnte aufzeichnen, die statistische Informationen über die Anzahl der Transaktionen und das Volumen der Kunden, die dafür verantwortlich sind. Diese Informationen könnten verwendet werden, für die Kapazitätsplanung, während die Zahl der Kunden wächst.
- Zufriedenheit der Nutzer mit der Leistung oder Funktionalität des Systems (möglicherweise indirekt) zu erkennen. Wenn eine große Anzahl von Kunden in einem e-Commerce-System regelmäßig ihrer Einkaufswagen verlassen könnte dies beispielsweise wegen eines Problems mit der Kasse-Funktionalität sein.
- Rechnungsinformationen zu generieren. Eine kommerzielle Anwendung oder mandantenfähige Service kann Kunden für die Ressourcen erheben, die sie verwenden.
- Quoten zu erzwingen. Wenn ein Benutzer in einem mandantenfähigen System ihre bezahlten Quote der Verarbeitung Zeit oder Ressourcen-Nutzung in einem bestimmten Zeitraum überschreitet, kann ihren Zugang beschränkt oder Verarbeitung gedrosselt.

### Anforderungen für die Überwachung der Verwendung
Um Systemnutzung zu untersuchen, müssten ein Operator in der Regel Informationen einschließlich finden Sie unter:

- Die Anzahl der Anfragen von jedes Subsystem verarbeitet und auf jede Ressource verwiesen wird.
- Die Arbeit von jedem Benutzer durchgeführt werden.
- Das Volumen der Datenspeicherung, die von jedem Benutzer besetzt.
- Die Ressourcen, die von den einzelnen Benutzern zugegriffen wird.

Ein Operator sollte auch Graphen, erzeugen beispielsweise können die meisten Ressourcen-hungrig-Benutzer oder die meistgenutzten Ressourcen anzeigt.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Nutzungskontrolle kann auf relativ hohem Niveau, in Anbetracht der Start- und End-Zeit jeder Anforderung und die Art der Anforderung (lesen, schreiben und so weiter, je nach der betreffenden Ressource) durchgeführt werden. Sie erhalten diese Informationen von:

- Ablaufverfolgung Benutzeraktivität.
- Erfassung von Leistungsindikatoren messen die Auslastung für jede Ressource.
- Überwachung der Auslastung CPU und i/o Operationen durchgeführt von den einzelnen Benutzern.

Dosier Zwecke müssen Sie auch Identität können welche Benutzer verantwortlich sind für die Durchführung, welche Vorgänge und Ressourcen, die diese Vorgänge zu nutzen. Die gesammelten Informationen sollte detailliert genug, um die genaue Abrechnung zu ermöglichen.

<a name="issue-tracking"></a>
## Issue-tracking
Kunden und andere Benutzer können Probleme melden, wenn unerwartete Ereignisse oder Verhalten im System auftritt. Issue-tracking-befasst sich mit diesen Fragen verwalten, verknüpfen sie mit Bemühungen um die zugrunde liegenden Probleme im System zu beheben und informieren Kunden über mögliche Lösungen.

### Anforderungen für die Problemverfolgung
Issue-tracking wird oft durchgeführt, mit einem separaten System, das ermöglicht Operatoren aufzuzeichnen und die Details von Nutzern gemeldeten Probleme zu melden. Diese Angaben können Informationen wie z. B. die Aufgaben enthalten, die der Benutzer versuchte, zu erfüllen, die Symptome des Problems, die Abfolge der Ereignisse, und Fehler oder Warnmeldungen, die ausgegeben wurden.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Die ursprüngliche Datenquelle für Issue-tracking-Daten ist der Benutzer, der in erster Linie das Problem gemeldet. Möglicherweise müssen die Benutzer in der Lage, zusätzliche Daten wie einen Crash-Dump (wenn die Anwendung eine Komponente, die auf dem Desktop des Benutzers läuft enthält), einen Snapshot Bildschirm und Datum und Uhrzeit, an dem der Fehler aufgetreten, zusammen mit allen anderen ökologischen Angaben wie ihre Lage ist.

Diese Informationen können zum Debuggen Anstrengung im feed und helfen, um einen Rückstand auf zukünftige Versionen der Software zu konstruieren.

### Issue-tracking-Daten analysieren
Verschiedene Benutzer meldet möglicherweise das gleiche Problem, und das Thema tracking-System sollten gemeinsame Berichte zusammen ordnen.

Die Fortschritte der Debuggen Anstrengungen gegen jedes Problem Bericht erfasst werden sollen, und wenn das Problem behoben ist, kann der Kunde der Lösung informiert.

Wenn ein Benutzer ein anerkanntes Problem mit eine Lösung bekannt, in der Ausgabe Spurhaltung System meldet, sollte der Betreiber den Benutzer über die Lösung sofort informieren können.

## Operationen Ablaufverfolgung und Debuggen von Software-releases
Wenn ein Benutzer ein Problem meldet, erkennt der Benutzer oft nur die unmittelbaren Auswirkungen hat es auf seine oder ihre Operationen, und der Benutzer kann nur berichten die Ergebnisse ihrer eigenen Erfahrung einen Operator für die Wartung des Systems verantwortlich. Diese Erfahrungen sind in der Regel nur ein sichtbare Symptom für ein oder mehrere grundlegende Probleme. In vielen Fällen müssen ein Analyst durch die Chronologie der zugrunde liegenden Vorgänge, die eigentliche Ursache des Problems herzustellen (Dieser Prozeß wird genannt Graben _Ursachenanalyse_).

> [AZURBLAU. HINWEIS] Root Cause Analysis kann Ineffizienzen im Design einer Anwendung aufdecken. In diesen Situationen ist es möglicherweise möglich, die betroffenen Elemente zu überarbeiten und sie als Teil einer späteren Version bereitstellen. Dieser Prozess erfordert sorgfältigen Kontrolle, und die aktualisierten Komponenten sollten eng überwacht werden.

### Anforderungen für die Ablaufverfolgung und Debuggen
Ablaufverfolgung für unerwartete Ereignisse und anderen Problemen ist es wichtig, dass die Überwachungsdaten genügend informiert nicht nur über die Probleme, die auftreten, auf dem hohen Niveau, sondern umfasst auch ausreichend detailliert aktivieren Analyst zu verfolgen zurück zu den Ursprüngen dieser Probleme und die Abfolge der Ereignisse zu rekonstruieren. Diese Informationen muss ausreichend sein, ermöglichen ein Analyst die Ursache aller Probleme zu diagnostizieren, so dass ein Entwickler die notwendigen Änderungen zur Vorbeugung von wiederkehrenden vornehmen kann.

### Datenanforderungen Quellen, Instrumenten und Daten-Sammlung
Fehlersuche kann Ablaufverfolgung beinhalten alle Methoden (und ihre Parameter), im Rahmen einer Operation zum Aufbau eines Baumes, die Darstellung der logische Ablauf durch das System aufgerufen, wenn ein Kunde eine bestimmte Anforderung stellt. Ausnahmen und Warnungen, die vom System als Folge dieses Flusses müssen erfasst und protokolliert werden.

Um Debuggen zu unterstützen, kann das System bieten Haken, mit die einen Operator Zustandsinformationen an entscheidenden Punkten im System erfassen können oder liefern Detailinformationen schrittweise ausgewählte Vorgänge Fortschreiten. Erfassen von Daten auf dieser Detailebene verhängen kann eine zusätzliche Auslastung des Systems sollte ein temporärer Prozess sein, vor allem eingesetzt, wenn eine höchst ungewöhnliche Reihe Ereignisse auftreten, die schwer zu replizieren, oder wenn eine neue Version eines oder mehrere Elemente in einem System müssen sorgfältig beobachtet, um sicherzustellen, dass sie wie erwartet funktionieren.

## Die Überwachung und Diagnose-pipeline
Überwachung eines großen verteilten Systems stellt eine erhebliche Herausforderung, und jede der im vorherigen Abschnitt beschriebenen Szenarien sollten nicht unbedingt isoliert betrachtet werden. Es wird voraussichtlich eine deutliche Überlappung in die überwachen und Diagnose erforderlichen Daten für jede Situation, obwohl diese Daten verarbeitet und auf unterschiedliche Weise dargestellt werden müssen können. Aus diesen Gründen sollten Sie eine ganzheitliche Sicht der Überwachung und Diagnose treffen.

Sie können die gesamte Überwachung und Diagnose-Prozess als eine Pipeline vorstellen, die die in Abbildung 1 dargestellten Phasen umfasst.

![](media/best-practices-monitoring/Pipeline.png)

_Abbildung 1.
Die Etappen in der Pipeline Überwachung und Diagnose_

Figure 1 highlights how the data for monitoring and diagnostics can come from a variety of data sources. The Instrumentation/Collection stage is concerned with instrumentation; determining which data to capture, how to capture it, and how to format this data so that it can be easily examined. The Analysis/Diagnosis phase takes the raw data and uses it to generate meaningful information that can be used to determine the state of the system. This information can be used to make decisions about possible actions to take, and the results can be fed back into the Instrumentation/Collection phase. The Visualization/Alerting stage phase presents a consumable view of the system state; it could display information in near real-time by using a series of dashboards, and it could generates reports, graphs, and charts to provide a historical view of the data that can help identify long-term trends. If information indicates that a KPI is likely to exceed acceptable bounds, then this stage can also trigger an alert to an operator. In some cases, an alert can also be used to trigger an automated process that attempts to take corrective actions, such as auto-scaling.

Beachten Sie, dass diese Schritte einen kontinuierlichen Fluss-Prozess darstellen, wo die Stadien parallel passieren. Im Idealfall sollten alle Phasen dynamisch konfigurierbar sein; an einigen Stellen vor allem, wenn ein System neu bereitgestellt wurde oder Probleme wird, kann es notwendig erweiterte Daten auf einer regelmäßigeren Basis zu sammeln. Zu anderen Zeiten sollte es möglich sein, Rückkehr zur Erfassung eines Ausgangsniveaus wichtiger Informationen zu überprüfen, ob das System ordnungsgemäß funktioniert.

Darüber hinaus sollte der gesamte monitoring-Prozess eine live, laufende Lösung betrachtet werden, die der Feinabstimmung und Verbesserungen aufgrund der Rückmeldung wird. Zum Beispiel, vielleicht beginnen Sie mit Mess-viele Faktoren um Systemintegrität zu bestimmen, aber Analyse im Laufe der Zeit könnte zu einer Verfeinerung wie Sie Maßnahmen, die nicht relevant sind, verwerfen, damit Sie zu genauer konzentrieren auf die Daten, die Sie benötigen bei gleichzeitiger Minimierung der Hintergrundgeräusche.

## Quellen der Überwachungs-und Diagnose-
The information used by the monitoring process can come from several sources, as illustrated in Figure 1. At the application level, information comes from trace logs incorporated into the code of the system. Developers should follow a standard approach for tracking the flow of control through their code (for example, on entry to a method emit a trace message that specifies the name of the method, the current time, the value of each parameter, and any other pertinent information. Recording the entry and exit times could also prove useful). You should log all exceptions and warnings, and ensure that you retain a full trace of any nested exceptions and warnings. Ideally, you should also capture information that identifies the user running the code together with activity correlation information (to track requests as they pass through the system), and log attempts to access all resources such as message queues, databases, files, and other dependent services; this information can be used for metering and auditing purposes.

Viele Anwendungen verwenden von Bibliotheken und Frameworks zu allgemeinen Aufgaben wie z. B. den Zugriff auf einen Datenspeicher oder über ein Netzwerk kommunizieren. Diese Frameworks möglicherweise konfigurierbare Ausgabe eigene Meldungen zur Ablaufverfolgung und roh Diagnoseinformationen wie Transaktionsraten, Daten-Übertragung-Erfolge und Misserfolge, und so weiter.

> [AZURBLAU. HINWEIS] Viele moderne Frameworks automatisch veröffentlichen, Leistung und Ablaufverfolgungsereignisse und Erfassung dieser Informationen ist lediglich ein Mittel zum Abrufen und speichern Sie es wo es verarbeitet und analysiert werden kann.

Das Betriebssystem, auf dem die Anwendung ausgeführt wird, kann eine Quelle von Low-Level systemweit Informationen, wie z. B. Leistungsindikatoren, die Angabe des i/o-, Speicherauslastung und CPU-Auslastung. Betriebssystemfehler (z. B. der Ausfall zum Öffnen einer Datei korrekt) können auch gemeldet werden.

Außerdem sollten Sie die zugrunde liegende Infrastruktur und Komponenten auf denen System ausgeführt wird. Virtuelle Maschinen, virtueller Netzwerke und Storage-Services können alle Quellen wichtige Leistungsindikatoren auf Infrastrukturebene und andere Diagnosedaten sein.

Wenn Ihre Anwendung andere externe Dienste wie Web-Server oder Datenbank-Managementsystem, verwendet möglicherweise diese Dienste ihre eigenen Ablaufverfolgungsinformationen, Protokolle und Leistungsindikatoren veröffentlichen. Beispiele sind dynamische Verwaltungssichten von SQL Server für die Verfolgung von Operationen, die für eine SQL Server-Datenbank und Internet Information Server-Ablaufverfolgung für die Aufzeichnung von Anfragen an einen Webserver protokolliert.

Da die Komponenten eines Systems geändert und neue Versionen bereitgestellt werden, ist es wichtig zu Attribut Themen, Ereignisse und Metriken zu jeder Version können. Diese Informationen sollten zurück an die Freigabe-Pipeline gebunden, so dass Probleme mit einer bestimmten Version einer Komponente schnell aufgespürt und behoben werden können.

Sicherheitsprobleme können an jedem beliebigen Punkt im System auftreten. Beispielsweise könnte ein Benutzer versucht, sich mit eine ungültige Benutzer-ID oder ein Kennwort anmelden; ein authentifizierter Benutzer könnte versuchen und nicht autorisierten Zugriff auf eine Ressource erhalten; oder ein Benutzer möglicherweise einen ungültigen oder veralteten Schlüssel Zugriff auf verschlüsselte Informationen bereit. Sicherheitsinformationen für erfolgreiche und fehlerhafte Anfragen sollen immer protokolliert werden.

Im Abschnitt [Instrumentieren einer Anwendung](#instrumenting-an-application) enthält weitere Hinweise auf Informationen, die Sie erfassen sollte, aber es eine Vielzahl von Strategien, die Sie verwenden können gibt, um diese Informationen in erster Linie zu sammeln:

- **Anwendung/System-Monitoring**. Diese Strategie verwendet interne Quellen innerhalb der Anwendung, die Applikations-Frameworks, Betriebssystem und Infrastruktur. Der Code der Anwendung selbst kann eine eigene monitoring-Daten an bemerkenswerten Punkten während des Lebenszyklus einer Clientanforderung generieren. Der Antrag kann Ablaufverfolgungsanweisungen enthalten, die selektiv aktiviert oder deaktiviert, da die Umstände erfordern. Diagnose mit einem Diagnose-Framework dynamisch zu injizieren ist ebenfalls möglich. Diese Frameworks bieten in der Regel Plugins, die zu verschiedenen Zeitpunkten der Instrumentierung im Code anfügen und zeichnet Ablaufverfolgungsdaten an diesen Punkten kann.

Darüber hinaus kann Ihr Code und/oder die zugrunde liegende Infrastruktur Ereignisse an kritischen Punkten auslösen. Überwachen der Agents, die konfiguriert sind, um diese Ereignisse zu überwachen, kann die Ereignisinformationen aufzeichnen.

- **Reale Benutzer überwachen**. Dieser Ansatz zeichnet die Interaktionen zwischen Benutzer und Anwendung und beobachtet den Fluss von jeder Anforderung und Antwort. Diese Informationen kann haben einen zweifachen Zweck: Es kann Dosier Nutzung von jedem Benutzer verwendet werden, und es kann verwendet werden, um festzustellen, ob der Benutzer einen geeigneten Qualität des Dienstes (z. B. schnelle Reaktionszeiten, niedrige Latenz und minimal auftretenden Fehler) erhalten. Die erfassten Daten können verwendet werden, um Bereichen Sorge, wo am häufigsten Ausfällen, und Elemente zu identifizieren, wo das System, möglicherweise aufgrund von Hotspots in der Anwendung oder eine andere Form der Flaschenhals verlangsamt. Wenn dieser Ansatz sorgfältig implementiert ist, können Benutzer fließt durch die Anwendung für das Debuggen und Testzwecke rekonstruieren möglich.

	> [AZURBLAU. WICHTIG] Durch die Überwachung von realen Benutzern erfassten Daten sollte hochsensiblen betrachtet werden, da es vertrauliches Material enthalten kann. Wenn die erfassten Daten gespeichert werden, muss es sicher gespeichert werden. Wenn die Daten für Performance monitoring oder Debugzwecke verwendet wird, sollten alle persönlich identifizierbarer Informationen heraus zuerst entfernt werden.

- **Synthetische Anwenderüberwachung**. Bei dieser Vorgehensweise schreiben Sie Ihre eigenen Testclient, simuliert einen Benutzer und konfigurierbare aber typische Folge von Operationen durchführt. Sie können die Leistung des Testclients um zu bestimmen, den Zustand des Systems verfolgen. Sie können auch mehrere Instanzen von der Testclient als Bestandteil eines Lasttests herstellen, wie das System unter Stress reagiert und welche Art der Überwachung Ausgabe generiert wird, unter diesen Bedingungen.

	> [AZURBLAU. HINWEIS] Sie können die echten und synthetischen Anwenderüberwachung von einschließlich Code, Spuren und Mal die Ausführung von Methodenaufrufen und andere wichtige Teile einer Anwendung, implementieren.

- **Profilerstellung**. Dieser Ansatz zielt in erster Linie auf Überwachung und Verbesserung der Anwendungsleistung. Anstatt in Betrieb auf der Funktionsebene von echten und synthetischen Anwenderüberwachung beschäftigt, es fängt Low-Level Informationen wie die Anwendung ausgeführt wird. Profiling kann implementiert werden, durch regelmäßige Probenahme von den Ausführungszustand des Antrags (bestimmen, welches Stück Code, der die Anwendung, zu einem bestimmten Zeitpunkt in der Zeit ausgeführt wird) oder mithilfe von Instrumentierung, das fügt des Codes an wichtigen Stellen (z. B. Beginn und Ende eines Methodenaufrufs) Sonden und notiert, welche Methoden aufgerufen wurden, zu welchem Zeitpunkt , und wie lange dauerte, jeden Anruf. Diese Daten können dann analysiert werden, um zu bestimmen, welche Teile der Anwendung Leistungsprobleme verursachen könnte.

- **Endpunkt-Überwachung**. Diese Technik verwendet einen oder mehrere diagnostische Endpunkte verfügbar gemachten Anwendung speziell für die Überwachung aktivieren. Ein Endpunkt stellt einen Pfad in den Anwendungscode bereit und kann Informationen über den Gesundheitszustand des Systems zurück. Unterschiedliche Endpunkten konnte auf verschiedene Aspekte der Funktionalität konzentrieren. Sie schreiben Ihre eigenen Diagnose-Client, der regelmäßige Abfragen an diese Endpunkte sendet und die Antworten zu assimilieren. Dieser Ansatz wird ausführlicher beschrieben, von der [Gesundheit-Endpunkt-Überwachung-Muster](https://msdn.microsoft.com/library/dn589789.aspx) auf der Microsoft-Website.

Für maximale Abdeckung verwenden Sie diese Techniken in Kombination miteinander.

<a name="instrumenting-an-application"></a>
## Instrumentieren einer Anwendung
Die Instrumentation ist ein wichtiger Bestandteil der monitoring-Prozess; Sie können sinnvolle Entscheidungen über die Leistung und die Gesundheit eines Systems nur vornehmen, wenn Sie die Daten erfassen, die Sie in erster Linie diese Entscheidungen treffen können. Die Informationen, die Sie mithilfe der Instrumentierung sammeln sollte ausreichen, um aktivieren Sie Leistung zu beurteilen, Diagnostizieren von Problemen und Entscheidungen ohne erfordern, dass man in einem Remoteproduktionsserver für die Nachverfolgung einloggen (und Debuggen) manuell.

Die Ausstattungsdaten werden in der Regel auf Protokolle der Ablaufverfolgung und Metriken geschriebenen Informationen umfassen:

- The contents of a trace log can be the result of textual data written by the application, binary data created as the result of a trace event (if the application is using Event Tracing for Windows – ETW), or they can be generated from system logs that record events arising from parts of the infrastructure, such as a web server. Textual log messages are often designed to be human-readable, but they should also be written in a format that enables them to be easily parsed by an automated system. You should also categorize logs; don't write all trace data to a single log but use separate logs to record the trace output from different operational aspects of the system. This enables you to quickly filter log messages by reading from the appropriate log rather than having to process a single lengthy file. Never write information that has different security requirements (such as audit information and debugging data) to the same log.

	> [AZURBLAU. HINWEIS] Ein Protokoll kann als Datei im Dateisystem implementiert werden, oder es könnte in einem anderen Format wie ein Blob in Blob-Speicher gehalten werden. Log-Informationen möglicherweise auch in stärker strukturierten Speicher, z. B. Zeilen in einer Tabelle stattfinden.

- Kennzahlen in der Regel werden einfach eine Maßnahme oder Count einige Aspekt oder einer Ressource im System zu einem bestimmten Zeitpunkt mit einem oder mehrere Tags oder Dimensionen zugeordnet (manchmal auch als ein _Probe_). Eine einzelne Instanz für einen Messwert eignet sich in der Regel nicht isoliert; Stattdessen haben Metriken im Laufe der Zeit erfasst werden. Der entscheidende Punkt zu beachten ist welche Metriken sollten Sie aufnehmen und wie häufig. Generieren von Daten für Metriken zu oft kann eine bedeutende zusätzliche Last auf dem System auferlegen, während aufzeichnende Metriken selten verursachen Sie die Umstände ein bedeutendes Ereignis verpassen. Die Überlegungen variieren von metrisch, metrische. Z. B. CPU-Auslastung auf einem Server kann vom zweiten zweiten erheblich variieren, aber hoher Auslastung wird nur ein Problem, wenn es über mehrere Minuten langlebig ist.

<a name="information-for-correlating-data"></a>
### Informationen zum Daten korrelieren
You can easily monitor individual system-level performance counters, capture metrics for resources, and obtain application trace information from various log files, but some forms of monitoring require the analysis and diagnostics stage in the monitoring pipeline to correlate the data retrieved from several sources. This data may take several forms in the raw data, and the analysis process must be provided with sufficient instrumentation data to be able to map these different forms. For example, at the application framework level, a task might be identified by a thread ID but within an application the same work might be associated with the user ID for the user performing that task. Furthermore, there is unlikely to be a 1:1 mapping between threads and user requests as asynchronous operations may reuse the same threads to perform operations on behalf of more than one user.  To complicate matters further, a single request may be handled by more than one thread as execution flows through the system. If possible, associate each request with a unique activity ID that is propagated through the system as part of the request context (the technique for generating and including activity IDs in trace information will be dependent on the technology being used to capture the trace data).

Alle monitoring-Daten sollten auf die gleiche Weise mit einem Zeitstempel versehen sein. Zeichnen Sie aus Konsistenzgründen alle Datums- und Uhrzeitangaben mit koordinierte Weltzeit auf. Dadurch kann Sie leichter Spur folgen von Ereignissen erstellen können.

> [AZURBLAU. HINWEIS] Computern in unterschiedlichen Zeitzonen und Netzwerken können nicht synchronisiert werden, so dass Sie nicht abhängen, sollten Verwendung von Timestamps allein zum Korrelieren Ausstattungsdaten, die mehrere Maschinen umfasst.

### Welche Informationen soll die Ausstattungsdaten mitschicken?
Beachten Sie die folgenden Punkte bei der Entscheidung, welche Ausstattungsdaten Sie sammeln müssen:

- Von Ablaufverfolgungsereignisse erfassten Informationen sollten Maschine und menschlich lesbar sein.  Sie sollten beschließen wohldefinierte Schemas für diese Informationen, um die automatisierte Verarbeitung von Protokolldaten über Systeme hinweg zu erleichtern, und Konsistenz zu Operationen und engineering-Personal die Protokolle lesen. Enthalten Sie Umweltinformationen, z. B. der Bereitstellungsumgebung, dem Computer, auf dem der Prozess ausgeführt wird, die Informationen über den Prozess und die Aufrufliste.  
- Profiling können einen erheblichen Aufwand auf dem System aufzuzwingen und sollte nur bei Bedarf aktiviert werden. Profilerstellung mithilfe Instrumentation zeichnet ein Ereignis (z. B. einen Methodenaufruf) jedes Mal, wenn er auftritt, während der Probenahme nur ausgewählte Ereignisse aufzeichnet. Die Auswahl könnte sein zeitbasierte – alle N Sekunden, oder Frequenz-basiert-sobald alle N anfordert. Wenn Ereignisse sehr häufig auftreten, kann Profilerstellung durch Instrumentation verursachen zu viel Belastung und selbst Gesamtleistung auswirken. In diesem Fall kann das Stichprobenverfahren vorzuziehen. Jedoch wenn die Häufigkeit der Ereignisse niedrig ist, könnte dann Probenahme vielleicht vermissen sie und in diesem Fall Instrumentierung der bessere Ansatz.
- Bieten Sie ausreichend Kontext ein Entwickler oder Administrator bestimmt jeder Anforderung aktivieren. Dazu kann gehören, dass irgendeine Form von Aktivitäts-ID identifiziert eine bestimmte Instanz einer Anfrage und Informationen zu dieser Aktivität korrelieren mit der computational erledigten Arbeit und die verwendeten Ressourcen verwendet werden kann. Beachten Sie, dass diese Arbeit Prozess- und Maschine Grenzen überschreiten kann. Dosier, Rahmen sollten auch (direkt oder indirekt über andere korrelierte Informationen) einen Verweis auf die Kunden, die die Anforderung zu erfolgen hat. In diesem Kontext liefert wertvolle Informationen über den Anwendungszustand zum Zeitpunkt die monitoring-Daten erfasst wurde.
- Notieren Sie alle Anforderungen, und die Orte oder Regionen, aus denen diese Anforderungen gestellt werden. Diese Informationen kann bei der Bestimmung ob gibt es standortspezifische Hotspots und liefern Daten, die bestimmen, ob Neupartitionieren einer Anwendung oder die Daten, die sie verwendet nützlich sein können.
- Aufnehmen und die Informationen über Ausnahmen sorgfältig zu erfassen. Oft wichtige Debuginformationen ist infolge schlechter Ausnahmebehandlung verloren. Erfassen Sie alle Details der Ausnahmen, die von der Anwendung, einschließlich der inneren Ausnahmen und andere Kontextinformationen, einschließlich der Aufrufliste wenn möglich.
- Be consistent in the data that the different elements of your application capture as this can assist in analyzing events and correlating them with user requests. Consider utilizing a comprehensive and configurable logging package to gather information rather than depending on developers implementing different parts of the system all adopting the same approach. Gather data from key performance counters, such as the volume of I/O being performed, network utilization, number of requests, memory use, and CPU utilization. Some infrastructure services may provide their own specific performance counters, such as the number of connections to a database, the rate at which transactions are being performed, and the number of transactions that succeed or fail. Applications might also define their own specific performance counters.
- Melden Sie alle Aufrufe an externe Dienste wie Datenbanksysteme, Webservices oder andere Systemebene Leistungen als Teil der Infrastruktur (z. B. Zwischenspeichern Azure). Erfassung von Informationen darüber wie viel Zeit für jeden Aufruf durchzuführen und den Erfolg oder Misserfolg des Aufrufs. Wenn möglich, Erfassung von Informationen über alle Wiederholungsversuche und Misserfolge für vorübergehende Fehler, die auftreten.

### Sicherstellung der Kompatibilität mit Telemetriesysteme
In vielen Fällen ist durch Instrumentierung erzeugte Informationen als eine Reihe von Ereignissen generiert und an eine separate Telemetriesystem für Verarbeitung und Analyse. Ein Telemetriesystem ist in der Regel unabhängig von bestimmten Anwendung oder Technologie, aber erwartet Informationen folgen ein bestimmtes Format in der Regel durch ein Schema definiert. Das Schema gibt effektiv einen Vertrag, der Definition der Datenfelder und Typen, die das Telemetriesystem einnehmen kann. Das Schema sollte verallgemeinert werden, um Daten aus einer Reihe von Plattformen und Geräten zu ermöglichen.

Ein gemeinsames Schema sollten Felder enthalten, die gemeinsam alle Instrumentierung-Ereignisse, wie den Namen, die Uhrzeit des Ereignisses, die IP-Adresse des Absenders und die Details für die Korrelation mit anderen Ereignissen (z. B. eine Benutzer-ID, Geräte-ID und ein Anwendungs-ID) erforderlich sind. Denken Sie daran, dass Ereignisse ausgelöst werden können, von einer beliebigen Anzahl von Geräten, so dass das Schema nicht den Gerätetyp abhängig sein soll. Darüber hinaus möglicherweise Ereignisse für die gleiche Anwendung ausgelöst werden, durch eine Reihe von unterschiedlichen Geräten; die Anwendung unterstützt möglicherweise Geräteübergreifende Verbreitung Roaming- oder eine sonstige Form. Idealerweise sollte die Ausgabe der Protokollierung-Bibliothek verwendet, um die Ereignisse zu erfassen die Mehrheit dieser Felder zuordnen.

Das Schema kann auch Domäne Felder enthalten, die über verschiedene Anwendungen für ein bestimmtes Szenario gemeinsame relevant sind. Dies könnte Informationen über Ausnahmen, Anwendungsstart und End-Ereignisse und Web Service API Aufruf Erfolg und/oder Fehler sein. Alle Anwendungen, die Verwendung der gleichen Domäne Felder sollten den gleichen Satz von Veranstaltungen, die Aktivierung einer Reihe von gemeinsamen Berichte und Analysen zu erstellenden ausgeben.

Schließlich könnte ein Schema benutzerdefinierte Felder für die Erfassung der Informationen über anwendungsspezifische Ereignisse enthalten.

### Best Practices für das Instrumentieren von Anwendungen
Die folgende Liste fasst die Empfehlungen für das Instrumentieren einer verteilten Anwendung in der Cloud ausgeführt.

- Machen Sie Protokolle leicht lesbar und leicht zu analysieren. Verwendung strukturiert, Protokollierung, wenn möglich. Prägnante und beschreibende in Log-Meldungen sein.
- Identifizieren Sie in allen Protokollen die Quelle zu und bieten Sie Kontext und Timing-Informationen, wie jeder Protokolldatensatz geschrieben steht.
- Verwenden Sie die Zeitzone und das Format für alle Zeitstempel. Dies wird helfen, um Ereignisse für Operationen zu korrelieren, die Hardware und Diensten in verschiedenen geografischen Regionen erstrecken.
- Protokolle zu kategorisieren und Meldungen in die entsprechende Protokolldatei schreiben.
- Geben Sie vertrauliche Informationen über das System oder persönlichen Daten von Usern nicht. Diese Informationen zu schrubben, bevor es angemeldet ist, aber dafür sorgen, dass die relevanten Details beibehalten werden. Beispielsweise entfernen Sie die ID und das Kennwort aus jeder Datenbank-Verbindungszeichenfolgen zu, aber schreiben Sie die weiteren Informationen in das Protokoll, damit ein Analyst feststellen kann, dass das System die richtige Datenbank zugreift. Protokolliert alle kritische Ausnahmen, aber der Administrator kann sich die Protokollierung für geringeren Ausnahmen und Warnungen aktivieren und deaktivieren. Darüber hinaus erfassen Sie und Protokolldaten Sie alle zur Logik von "Wiederholen". Diese Daten können in den Transienten Zustand des Systems überwachen nützlich sein.
- Verfolgen Sie Out of Process-Aufrufe, wie z. B. Anforderungen an externe Webdienste oder Datenbank.
- Vermischen Sie nicht Log-Nachrichten mit unterschiedlichen Sicherheitsanforderungen in derselben Protokolldatei. Z. B. nicht schreiben, Debuggen und Auditinformationen in das gleiche Protokoll.
- Mit Ausnahme der Überwachungsereignisse, sollte alle Protokollierung Aufrufe Fire-and-forget-Operationen, die nicht den Fortschritt der Geschäftsbetrieb blockieren müssen. Überwachungsereignisse sind außergewöhnlich, denn sie für das Geschäft sind und als ein wesentlicher Bestandteil der Geschäftstätigkeit eingestuft werden können.
- Protokollierung sollte erweiterbar sein und keinen direkten Abhängigkeiten auf konkretes Ziel. Zum Beispiel anstatt Informationen zu schreiben, mit _System.Diagnostics.Trace_, eine abstrakte Schnittstelle (z. B. definieren _ILogger_) die Protokollierungsmethoden und die verfügbar macht mit allen geeigneten Mitteln umgesetzt werden kann.
- Alle Protokollierung muss ausfallsicheren und sollte nie kaskadierenden Fehler auslösen. Protokollierung, dürfen keine Ausnahmen auslösen.
- Behandeln Sie Instrumentierung zu, als einen fortlaufenden iterativen Prozess, und überprüfen Sie die Protokolle regelmäßig, nicht nur, wenn ein Problem vorliegt.

## Erfassen und Speichern von Daten
The collection stage of the monitoring process is concerned with retrieving the information generated by instrumentation, formatting this data to make it easier to consume by the analysis/computation phase, and saving the transformed data in reliable and accessible storage. The instrumentation data that you gather from different parts of a distributed system could be held in a variety of locations and with varying formats. For example, your application code might generate trace log files, and generate application event log data, while performance counters that monitor key aspects of the infrastructure that your application uses could be captured by using other technologies.  Any third-party components and services that your application uses may provide instrumentation information in different formats, using separate trace files, blob storage, or even a custom data store.

Datenerhebung erfolgt oft durch Implementierung einer Sammlung-Service die autonom von der Anwendung ausführen können, die die Instrumentation-Daten generiert. Abbildung 2 zeigt ein Beispiel dieser Architektur, Hervorhebung des Instrumentierung Daten Auflistung Subsystems.

![](media/best-practices-monitoring/TelemetryService.png)

_Abbildung 2.
Instrumentationsdaten sammeln_

Beachten Sie, dass dies eine vereinfachte Ansicht ist. Der Abholservice ist nicht unbedingt ein einzelner Prozess und viele Bestandteile, die auf verschiedenen Computern laufenden umfassen kann, wie in den folgenden Abschnitten beschrieben. Zusätzlich, wenn die Analyse einiger Telemetrie-Daten schnell erfolgen muss (Analyse, heiß, wie im Abschnitt beschrieben [Heiß, Warm und kalt-Analyse zu unterstützen](#supporting-hot-warm-and-cold-analysis) später in diesem Dokument) möglicherweise lokale Komponenten außerhalb des Auflistung-Dienstes sofort die Analyseaufgaben ausführen. Abbildung 2 zeigt diese Situation bei ausgewählten Veranstaltungen; nach der analytischen Verarbeitung können die Ergebnisse direkt an die Visualisierung und Warn-Subsystem gesendet werden. Daten, warme oder kalte Analyse unterzogen ist im Speicher gehalten, während es Verarbeitung wartet.

Für Azure Anwendungen und Dienste bietet Azure Diagnose (WAD) eine mögliche Lösung für die Datenerfassung. WAD sammelt Daten aus folgenden Quellen für jedem Berechnungsknoten fasst es zusammen und dann lädt es zu Azure Storage:

- Himmelblau-Protokolle
- IIS-Protokolle
- IIS Failed Request Protokolle
- Windows-Ereignisprotokolle
- Leistungsindikatoren
- Crash-dumps
- Himmelblau-Diagnose-Infrastruktur-Protokolle  
- Benutzerdefinierte Fehlermeldungen

Weitere Informationen finden Sie im Artikel [Azure: Telemetrie-Grundlagen und Fehlerbehebung](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) auf der Microsoft-Website.

### Strategien für die Datenerhebung Instrumentierung
Angesichts der elastische Art der Wolke und die Notwendigkeit der manuell abrufen von Telemetriedaten von jedem Knoten im System zu vermeiden, Sie sollten für die Daten an einem zentralen Standort übertragen werden ordnen und konsolidiert. In einem System, das mehrere Rechenzentren erstreckt, kann es sinnvoll sein, zuerst sammeln, konsolidieren und Speichern von Daten auf Basis des Region für Region und dann fassen die regionalen Daten in einem zentralen System.

Um die Nutzung der Bandbreite zu optimieren, können Sie wählen, um weniger dringende Daten in Blöcken als Batches zu übertragen. Vor allem, wenn sie zeitkritische Informationen enthält müssen die Daten jedoch nicht auf unbestimmte Zeit verzögert.

#### _Ziehen und schieben Ausstattungsdaten_
Das Instrumentierung Daten Auflistung Subsystem kann entweder aktiv Ausstattungsdaten aus den verschiedenen Protokollen und anderen Quellen für jede Instanz der Anwendung (die abzurufen _Pull-Modell_), oder es kann dienen als passive Empfänger warten auf die Daten aus den Komponenten gesendet werden, die jede Instanz der Anwendung (die bilden _Push-Modell_).

Ein Ansatz zur Umsetzung des Pull-Modells ist Überwachung Agents, die lokal ausgeführt werden, wobei jede Instanz der Anwendung verwenden. Einen Überwachungsagenten ist ein separater Prozess, der in regelmäßigen Abständen abruft (zieht) Telemetrie Daten auf dem lokalen Knoten erfasst und schreibt diese Informationen direkt an das zentrale speichern, die von allen Instanzen der Anwendung freigegeben ist. Dies ist der Mechanismus von WAD implementiert. Jede Instanz einer Azure Web oder Worker Rolle kann konfiguriert werden, diagnostische Erfassung und andere Ablaufverfolgungsinformationen, die lokal gespeichert ist. Die Überwachungsagenten, die neben jeder Kopien die angegebenen Daten zu Azure Storage ausgeführt wird. Die Seite [Konfigurieren Diagnose für Azure Cloud Services und virtuelle Maschinen](https://msdn.microsoft.com/library/azure/dn186185.aspx) Geben Sie auf der Microsoft-Website Weitere Informationen zu diesem Prozess. Einige Elemente, z. B. IIS-Protokolle, crash Dumps, und benutzerdefinierte Fehler, die Protokolle geschrieben werden, um blob-Speicher, während die Daten aus der Windows-Ereignisprotokoll, ETW-Ereignisse und Leistungsindikatoren wird in Tabelle Speicher aufgezeichnet. Dieser Mechanismus ist in Abbildung 3 dargestellt:

![](media/best-practices-monitoring/PullModel.png)

_Abbildung 3.
Verwenden einen Überwachungsagenten ziehen Informationen und Schreibzugriff auf den freigegebenen Speicher_

> [AZURBLAU. HINWEIS] Mit einen Überwachungsagenten eignet sich ideal zur Erfassung von Instrumentationsdaten, die natürlich aus einer Datenquelle, z. B. Informationen aus SQL Server-Management-Ansichten oder die Länge einer Azure Service Bus Warteschlange abgerufen werden.

Informationen zum Konfigurieren und Verwenden von Azure-Diagnose finden Sie auf der [Erfassen Sie Protokollierung von Daten mithilfe von himmelblau Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx) Seite auf der Microsoft-Website.

Telemetrie-Daten für eine kleine Anwendung, die auf eine begrenzte Anzahl von Knoten können an einem einzigen Speicherort mithilfe des soeben beschriebenen Ansatzes durchführbar gespeichert werden. Jedoch kann eine komplexe, hoch skalierbar, globalen Wolke Anwendung leicht große Mengen von Daten von Hunderten Web und Worker Rollen, Datenbank-Scherben und andere Dienste generieren. Diese Flut von Daten konnte die e-Bandbreite zur Verfügung, mit einem einzigen, zentralen Standort leicht überfordern. Daher muss Ihre Telemetrie-Lösung skalierbar, um zu verhindern, dass es als Engpass handeln, da das System erweitert und integrieren idealerweise einen Grad an Redundanz zur Verringerung der Risiken wichtigen monitoring-Informationen (z. B. Audit oder Abrechnung) zu verlieren, wenn Teil des Systems ausfällt.

Um diese Probleme zu beheben, können Sie Warteschlangen implementieren. Abbildung 4 zeigt diese Struktur. In dieser Architektur, die lokalen Überwachungsagenten (wenn es entsprechend konfiguriert werden kann) oder benutzerdefinierte Daten Abholdienst (falls nicht) Beiträge Daten an eine Warteschlange und einem separaten Prozess asynchron ausführen (Speicher schreiben Service in Abbildung 4) übernimmt die Daten in dieser Warteschlange und schreibt es in freigegebenen Speicher. Eine Nachrichtenwarteschlange eignet sich für dieses Szenario, wie es bietet mindestens einmal Semantik sicherstellen, dass sobald gebucht, Daten in der Warteschlange nicht verloren gehen. Der Speicherdienst schreiben kann durch Verwendung einer separaten Arbeitsprozess Rolle implementiert werden.

![](media/best-practices-monitoring/BufferedQueue.png)

_Abbildung 4.
Mithilfe einer Warteschlange, um Puffer Ausstattungsdaten_

Der lokalen Daten-Sammlung-Dienst kann Daten an eine Warteschlange hinzufügen, sofort, sie empfangen wird. Die Warteschlange dient als Puffer und die Lagerung schreiben Dienst kann abrufen und schreiben die Daten in seinem eigenen Tempo. Standardmäßig eine Warteschlange arbeitet auf Basis des First-in-First-Out, aber Sie können Nachrichten an sie durch die Warteschlange zu beschleunigen, wenn diese Daten enthalten, die schneller verarbeitet werden müssen Prioritäten setzen. Weitere Informationen finden Sie unter der [Vorrangwarteschlange](https://msdn.microsoft.com/library/dn589794.aspx) Muster. Alternativ können Sie verschiedene Kanäle (z. B. Service Bus Themen) auf direkte Daten an verschiedene Ziele abhängig von der Form der analytischen Verarbeitung erforderlich.

Für Skalierbarkeit könnten Sie mehrere Instanzen des Speichers schreiben Dienst ausführen. Existiert eine große Anzahl von Veranstaltungen, können einen Ereignis-Hub Sie um zu verschiedenen Compute-Ressourcen für die Verarbeitung und Speicherung die Daten zu versenden.

<a name="consolidating-instrumentation-data"></a>
#### _Konsolidierung der Instrumentierung_
The instrumentation data retrieved by the data collection service from a single instance of an application gives a localized view of the health and performance of that instance. To assess the overall health of the system, it is necessary to consolidate some aspects of the data in the local views together. This can be performed after the data has been stored, but in some cases it could also be achieved as the data is collected. Rather than being written directly to shared storage, the instrumentation data could pass through a separate data consolidation service which combines data and acts as a filter and cleanup process. For example, instrumentation data that includes the same correlation information such as an activity ID can be amalgamated (it is possible that a user starts performing a business operation on one node, and then gets transferred to another node in the event of node failure or depending on how load-balancing is configured). This process can also detect and remove any duplicated data (always a possibility if the telemetry service uses message queues to push instrumentation data out to storage). Figure 5 illustrates an example of this structure.

![](media/best-practices-monitoring/Consolidation.png)

_Abbildung 5.
Mithilfe separaten Dienst zu konsolidieren und Instrumentationsdaten bereinigen_

### Speichern von Instrumentationsdaten
The previous discussions have depicted a rather simplistic view of the way in which instrumentation data is stored. In reality, it can make sense to store the different types of information by using technologies that are most appropriate to the way in which each type is likely to be used. For example, Azure blob and table storage have some similarities in the way in which they are accessed, but have limitations concerning the operations that you can perform using them and the granularity of the data that they hold is quite different. If you need to perform more analytical operations or require full-text search capabilities on the data, it may be more appropriate to use data storage that provides capabilities that are optimized for specific types of queries and data access. For example, performance counter data could be stored in a SQL database to enable ad-hoc analysis; trace logs might be better stored in Azure DocumentDB; security information could be written to HDFS; information requiring full-text search could be stored by using Elastic Search (which can also speed searches by using rich indexing.) You can implement an additional service that periodically retrieves the data from shared storage, partitions and filters the data according to its purpose, and then writes it to an appropriate set of data stores as shown in Figure 6. An alternative approach is to include this functionality in the consolidation and cleanup process and write the data directly to these stores as it is retrieved rather than saving it in an intermediate shared storage area. Each approach has its advantages and disadvantages. Implementing a separate partitioning service lessens the load on the consolidation and cleanup service, and enables at least some of the partitioned data to be regenerated if necessary (depending on how much data is retained in shared storage). However, it consumes additional resources, and there may be a delay between the instrumentation data being received from each application instance and this data being converted into actionable information.

![](media/best-practices-monitoring/DataStorage.png)

_Abbildung 6.
Partitionieren von Daten nach analytischen und Speicheranforderungen_

Für mehr als einen Zweck möglicherweise die gleichen Ausstattungsdaten erforderlich. Z. B. Leistungsindikatoren können zum einen historischen Blick auf die Systemleistung im Laufe der Zeit, aber diese Informationen möglicherweise auch mit anderen Nutzungsdaten Kunde Gebührenzählung Informationen generieren kombiniert werden. In diesen Situationen können die gleichen Daten an mehr als ein Ziel, z. B. einer Dokumentendatenbank gesendet werden, die als einen langfristigen Speicher für die Abhaltung von Zahlungsinformationen und eine Multi-dimensionale Informationsspeicher für den Umgang mit komplexen Leistung Analytik dienen können.

Außerdem sollten Sie, wie dringend die Daten erforderlich sind. Daten, die Informationen zur Alarmierung Bedürfnisse schnell zugegriffen werden, und also sollte stattfinden in, schnellen Datenspeicherung und indiziert oder strukturiert, um die Abfragen zu optimieren, die das Warnungssystem führt. In einigen Fällen kann es notwendig für die Telemetrie-Dienst, der sammelt die Daten auf jedem Knoten zu formatieren und die Daten lokal zu speichern, so dass eine lokale Instanz der das Warnungssystem alle Probleme schnell benachrichtigen kann. Dieselben Daten können versendet werden, in den Speicher schreiben Service in den vorherigen Diagrammen dargestellten und zentral gespeichert werden, wenn es auch für andere Zwecke erforderlich ist.

Informationen, die für mehr verwendet als Analyse, Berichterstattung, Schmierblutungen historische Trends ist für weniger dringende und gespeichert werden kann, in gewissem Sinne, das Datamining und Ad-hoc-Abfragen unterstützt. Weitere Informationen finden Sie im Abschnitt [Heiß, Warm und kalt-Analyse zu unterstützen](#supporting-hot-warm-and-cold-analysis) später in diesem Dokument.

#### _Log-Rotation und Vorratsdatenspeicherung_
Instrumentation can generate considerable volumes of data. This data can be held in several places, starting with the raw log files, trace files, and other information captured at each node to the consolidated, cleaned, and partitioned view of this data held in shared storage. In some cases, once the data has been processed and transferred the original raw source data can be removed from each node. In other cases, it may be necessary or simply useful to save the raw information. For example, data generated for debugging purposes may be best left available in its raw form but can then be discarded quite quickly once any bugs have been rectified. Performance data often has a longer life to enable it to be used to spot performance trends and for capacity planning. The consolidated view of this data is usually kept on-line for a finite period to enable fast access, after which it might be archived or discarded. Data gathered for metering and billing customers may need to be saved indefinitely. Additionally, regulatory requirements might dictate that information collected for auditing and security purposes will also need to be archived and saved. This data is also sensitive and may need to be encrypted or otherwise protected to prevent tampering. You should never record information such as users' passwords or other information that could be used to commit identity fraud; such details should be scrubbed from the data before it is stored.

#### _Nach unten-Probenahme_
Häufig ist es sinnvoll, um langfristigen Wachstumstrends können historische Daten zu speichern. Anstatt die alte Daten in vollem Umfang zu speichern, kann es möglich sein, nach unten-Beispiel die Daten zur Verringerung seiner Entschließung und Lagerkosten sparen. Als Beispiel könnte anstatt speichern Leistungsindikatoren von Minute zu Minute, Daten mehr als einen Monat alt konsolidiert werden, um eine Ansicht von Stunde zu bilden.

### Bewährte Methoden für die Erfassung und Speicherung Protokollieren von Informationen
Die folgende Liste fasst die Empfehlungen für die Erfassung und Speicherung Protokollieren von Informationen.

- Die Überwachungsagenten oder Auflistung Datendienst sollte sollte als Out-of-Process-Dienst ausgeführt und einfach bereitstellen.
- Alle Ausgaben von der Überwachungsagenten oder Auflistung Datendienst sollte ein plattformunabhängiges Format, die unabhängig von der Maschine, Betriebssystem oder Netzwerk-Protokoll. Beispielsweise geben Sie Informationen in einem selbstbeschreibenden Format wie JSON, MessagePack, oder Protobuf als ETL/ETW. Mit einem standard-Format ermöglicht dem System, Verarbeitungspipelines zu konstruieren; Komponenten, die lesen, umwandeln und Ausgeben von Daten im vereinbarten Format können einfach integriert werden.
- Die Überwachung und Einziehung von Forderungen muß ausfallsicheren und muss nicht kaskadierende Fehlerbedingungen auslösen.
- Bei einem vorübergehenden Ausfall beim Senden von Informationen an eine Datensenke der Agent oder Daten Auflistung Überwachungsdienst sollten bereit sein, Telemetrie-Daten neu anordnen, so dass die neueste Informationen zuerst gesendet werden (der Überwachungsdienst Agent/Daten-Auflistung kann beschließen, die älteren Daten löschen oder lokal speichern und später, um aufzuholen, nach eigenem Ermessen zu übertragen.)

## Analysieren von Daten und Diagnose von Problemen
Ein wichtiger Bestandteil der Überwachung und Diagnose-Prozess ist erhalten Sie einen Überblick über das allgemeine Wohlbefinden des Systems gesammelten Daten analysieren. Sie sollten eigene KPIs und Kennzahlen definiert haben, und es ist wichtig zu verstehen, wie Sie die Daten strukturieren können, die für Ihre Analyse Anforderungen gesammelt wurden. Es ist auch wichtig zu verstehen, wie die Daten aufgezeichnet, in verschiedene Metriken und Protokolldateien ist korreliert, wie diese Informationen sein Schlüssel zur Verfolgung einer Abfolge von Ereignissen und Diagnose von Problemen, die auftreten kann.

Wie im Abschnitt beschrieben [Konsolidierung der Instrumentation](#consolidating-instrumentation-data), the data for each part of the system is typically captured locally, but generally needs to be combined with data generated at other sites that participate in the system. This information requires careful correlation to ensure that data is combined accurately. For example, the usage data for an operation may span a node hosting a web site to which a user connects, a node running a separate service accessed as part of this operation, and data storage held on a further node. This information needs to be tied together to provide an overall view of the resource and processing usage for the operation. Some pre-processing and filtering of data might occur on the node on which the data is captured, while aggregation and formatting is more likely to occur on a central node.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Heiß, warm und kalt-Analyse zu unterstützen
Analysieren und Formatieren von Daten für die Visualisierung, können Berichterstattung und Alarmierung Zwecke ein komplexer Prozess sein, der einen eigenen Satz von Ressourcen verbraucht. Einige Formen der Überwachung sind zeitkritisch und erfordern sofortige Analyse von Daten, um wirksam zu sein. Dies bezeichnet man als _heiß-Analyse_. Beispiele hierfür sind die Analysen für Alarmierung und einige Aspekte der Sicherheitsüberwachung (z. B. das Erkennen von einem Angriff auf das System) erforderlich. Für diese Zwecke erforderliche Daten müssen schnell verfügbar und für die effiziente Bearbeitung strukturiert sein; es einigen Fällen möglicherweise erforderlich sein, verschieben die Analyse, die Verarbeitung an den einzelnen Knoten, auf dem die Daten gespeichert ist.

Andere Formen der Analyse sind weniger zeitkritische und erfordern einige Berechnungen und Aggregation, sobald die Rohdaten eingegangen ist. Dies bezeichnet man als _warm-Analyse_. Performance-Analyse, fällt oft in diese Kategorie. In diesem Fall ist ein isoliert, einzelner Leistung-Ereignis kaum statistisch signifikant (es könnte durch eine plötzliche Spitze oder Fehler verursacht werden), werden die Daten aus einer Reihe von Veranstaltungen sollten ein zuverlässigeres Bild der Systemleistung vorsehen. Warm-Analyse kann auch verwendet werden, zur Diagnose von Gesundheitsfragen. Ein Systemereignis wird in der Regel durch heiße Analyse verarbeitet und kann sofort eine Warnung auslösen. Ein Operator sollte in der Lage, die Gründe für das Systemereignis zu bohren, durch Untersuchen der Daten aus dem warmen Pfad; Diese Daten sollten enthalten Informationen zu den Ereignissen im Vorfeld des Problems, das das Gesundheit-Ereignis verursacht.

Einige Formen der Überwachung mehr langfristige Daten generiert, und die Analyse kann erfolgen zu einem späteren Zeitpunkt möglicherweise nach einem vordefinierten Zeitplan. In einigen Fällen müssen die Analyse komplexer Filterung großer Mengen von Daten über einen längeren Zeitraum erfasst. Dies bezeichnet man als _kalt-Analyse_. Die Grundvoraussetzung ist, dass die Daten sicher gespeichert werden, nachdem es aufgezeichnet wurde. Zum Beispiel Auslastungs-monitoring und Überwachung erfordern einen genauen Überblick über den Zustand des Systems zu regelmäßigen Zeitpunkten in der Zeit, aber diese Zustandsinformationen muss nicht verfügbar für die Verarbeitung sofort erfasst wurden.  Kalte Analyse kann auch genutzt werden, um die Daten für die prädiktive Gesundheits-Analyse bereitzustellen. Historische Informationen über einen bestimmten Zeitraum kann in Verbindung mit der aktuellen Gesundheitsdaten (abgerufen aus dem heißen Pfad) zum Aufspüren von Trends verwendet werden, die bald Gesundheitsprobleme verursachen kann. In diesen Fällen kann es notwendig, eine Warnung damit Korrekturmaßnahmen ergriffen werden können zu erhöhen.

### Korrelation von Daten
The data captured by instrumentation can provide a snapshot of the system state, but the purpose of analysis is to make this data actionable. For example, what has caused an intense I/O loading at the system level at a specific time? Is it the result of a large number of database operations? Is this reflected in the database response times, the number of transactions per second, and application response times at the same juncture? If so, then one remedial action that may reduce the load could be to shard the data over more servers. In addition, exceptions can arise as a result of a fault in any level of the system, and an exception in one level often triggers another fault in the level above. For these reasons you need to be able to correlate the different types of monitoring data at each level to produce an overall view of the state of the system and the applications that are executing on it. You can then use this information to make decisions about whether the system is functioning acceptably or not, and determine what can be done to improve the quality of the system.

Wie im Abschnitt beschrieben [Informationen zum Daten korrelieren](#information-for-correlating-data), Sie müssen sicherstellen, dass die rohen Ausstattungsdaten ausreichend Kontext und Aktivität-ID-Informationen zur erforderlichen Aggregationen Unterstützung für Korrelation von Ereignissen enthält. Darüber hinaus können diese Daten in verschiedenen Formaten gehalten werden und es kann erforderlich sein, diese Informationen in ein standardisiertes Format für Analysezwecke umwandeln zu analysieren.

### Fehlersuche und Diagnose von Problemen
Die Diagnose erfordert in der Lage, die Ursache für Fehler oder unerwartetes Verhalten, einschließlich der Durchführung der Ursachenanalyse ermitteln. In der Regel erforderlichen Informationen umfassen:

- Detaillierte Informationen von Ereignisprotokollen und Spuren, die das gesamte System oder für eine angegebene Subsystem während eines festgelegten Zeitfensters.
- Komplette Stack-Traces infolge Ausnahmen und Fehlern alle angegebenen Ebene, die entweder innerhalb des Systems oder eine angegebene Subsystem während eines bestimmten Zeitraums auftreten.
- Deponien für jede fehlgeschlagene Prozesse entweder an einer beliebigen Stelle im System oder für eine angegebene Subsystem während eines festgelegten Zeitfensters zu Abstürzen.
- Aktivitäten protokolliert Aufzeichnung der Vorgänge entweder alle Benutzer oder für ausgewählte Benutzer während eines bestimmten Zeitraums durchgeführt wird.

Analysieren von Daten zum Zweck der Problembehandlung häufig erfordert ein tiefgehende technisches Verständnis für die Systemarchitektur und die verschiedenen Komponenten die Lösung. Daher erfordert es häufig ein hohes Maß an manueller Eingriff und Interpretation der Daten, die Ursache der Probleme schaffen und empfehlen eine geeignete Strategie zu korrigieren. Es kann einfach eine Kopie dieser Informationen in ihrem Originalformat speichern und für kalte Analyse durch einen Experten zur Verfügung stellen angebracht sein.

## Visualisierung der Daten und Warnungen auslösen
Ein wichtiger Aspekt des monitoring-Systems ist die Fähigkeit, präsentieren die Daten so, dass ein Operator schnell vor, alle Trends oder Probleme Ort kann, und um schnell einen Operator, wenn ein bedeutendes Ereignis informieren aufgetreten, die Aufmerksamkeit erfordern.

Datenpräsentation kann verschiedene Formen annehmen, einschließlich Visualisierung mithilfe von Dashboards, Warnungen und Berichte.

### Visualisierung mit dashboards
The most common way to visualize data is to use dashboards that can display information as a series of charts, graphs, or some other pictorial manner. These items could be parameterized, and an analyst should be able to select the important parameters (such as the time period) for any specific situation. Dashboards can be organized hierarchically, with top-level dashboards giving an overall view of each aspect of the system but with the facility to enable an operator to drill down to the details. For example, a dashboard that depicts the overall disk I/O for the system should allow an analyst to dig into the data and view the I/O rates for each individual disk to ascertain whether one or more specific devices account for a disproportionate volume of traffic. Ideally the dashboard should also display related information, such as the source of each request (the user or activity) that is generating this I/O. This information could then be used to determine whether (and how) to spread the load more evenly across devices, and whether the system would perform better if more devices were added. A dashboard might also be able to use color-coding or some other visual cues to indicate values that appear anomalous or that are outside an expected range. Using the previous example, a disk with an I/O rate approaching its maximum capacity over an extended period (a hot disk) could be highlighted in red, a disk with an I/O rate that periodically runs at its maximum limit over short periods (a warm disk) could be highlighted in yellow, and a disk exhibiting normal usage could be displayed in green.

Beachten Sie, dass für ein Dashboard-System effektiv zu arbeiten, muss es die Rohdaten mit dem Sie arbeiten. Wenn Sie Ihr eigenes Dashboard-System erstellen oder von einer anderen Organisation unter Verwendung eines Dashboards entwickelt, müssen Sie verstehen, welche Ausstattungsdaten Sie sammeln auf welche Marktstufe Granularität und wie es für den Verzehr durch das Dashboard formatiert werden soll, müssen.

Ein gutes Dashboard zeigt nicht nur Informationen, es bietet die Möglichkeit, ein Analyst, Ad-hoc-Fragen zu diesen Informationen darstellen lassen. Einige Systeme bieten ein Operator verwenden kann, um diese Aufgaben und erkunden Sie die zugrunde liegenden Daten-Management-Tools. Alternativ kann je nach dem Repository verwendet, um diese Informationen aufzunehmen es möglich sein, diese Daten direkt Abfragen, oder importieren Sie sie in Tools wie Microsoft Excel für weitere Analysen und Auswertungen.

> [AZURBLAU. HINWEIS] Sie sollten Zugriff auf Dashboards für autorisiertes Personal beschränken; Diese Informationen können wirtschaftlich sensibler sein. Sie sollten auch die zugrunde liegenden Daten, präsentiert von dem Armaturenbrett zu verhindern, dass Benutzer ändern es schützen.

### Auslösen von Warnungen
Alarmierung ist der Prozess der Analyse der Überwachung und Instrumentierung-Daten und generieren eine Benachrichtigung, wenn ein bedeutendes Ereignis erkannt wird.

Alarmierung wird verwendet, um sicherzustellen, dass das System gesund, reaktionsschnell und sicher bleibt. Es ist ein wichtiger Bestandteil eines jeden Systems, die Leistung, Verfügbarkeit und Privatsphäre garantiert der Nutzer stellt, und die Daten müssen auf sofort gehandelt werden. Der Luftfahrtunternehmer müssen über das Ereignis informiert werden, der die Warnung ausgelöst. Warnungen kann auch verwendet werden, zum Aufrufen von Funktionen wie automatische Skalierung.

Warnungen in der Regel hängt von den folgenden Ausstattungsdaten:

- Sicherheitsrelevante Ereignisse. Wenn die Ereignisprotokolle anzugeben, die wiederholt Authentifizierung und/oder Autorisierungsfehler auftreten, ist möglicherweise das System angegriffen und ein Operator sollte informiert werden.
- Performance-Kennzahlen. Das System muss schnell reagieren, wenn eine bestimmte Leistungsmetrik einen bestimmten Schwellenwert überschreitet.
- Informationen zur Verfügbarkeit. Wenn ein Fehler, dass es möglicherweise notwendig festgestellt wird, eine oder mehrere Subsysteme oder Failover auf eine Sicherung Ressource schnell neu zu starten. Wiederholte Fehler in einem Subsystem können mehr ernsthafte Bedenken hinweisen.

Operatoren können Warnungsinformationen über viele Lieferung Kanäle wie e-Mail, Pager-Gerät oder einer SMS-Textnachricht empfangen. Eine Warnung kann auch einen Hinweis auf, wie kritisch die Situation ist enthalten, der entstanden ist. Viele Alarm Systeme unterstützen Abonnenten Gruppen und alle Operatoren, die Mitglieder derselben Gruppe sind dieselben Warnungen gesendet werden können.

An alerting system should be customizable and the appropriate values from the underlying instrumentation data could be provided as parameters. This approach enables an operator to filter data and focus on those thresholds or combinations of values which are of interest. Note that in some cases, the raw instrumentation data could be provided to the alerting system, while in other situations it might be more appropriate to supply aggregated data (for example, an alert could be triggered if the CPU utilization for a node exceeded 90% over the last 10 minutes). The details provided to the alerting system should also include any appropriate summary and context information; this data can help to reduce the possibility of false-positive events tripping an alert.

### Berichterstattung
Berichterstattung wird verwendet, um einen Überblick über das System zu generieren, und historische Daten sowie aktuelle Informationen übernehmen kann. Berichterstattung, Anforderungen selbst fallen in zwei Kategorien unterteilen: Berichterstattung für operative und Sicherheits-reporting.

Berichterstattung für operative umfasst in der Regel die folgenden Aspekte:

- Aggregieren von Statistiken Ressourcenauslastung des Gesamtsystems oder angegebenen Subsysteme während eines festgelegten Zeitfensters verstehen können.
- Trends der Ressourcenverwendung für das Gesamtsystem oder angegebenen Subsysteme während eines bestimmten Zeitraums erkennen.
- Überwachung der Ausnahmen, die im gesamten System aufgetreten sind oder in Subsysteme während eines bestimmten Zeitraums angegeben haben.
- Bestimmung der Effizienz der Anwendung in Bezug auf die bereitgestellten Ressourcen und verstehen, ob der Umfang der Mittel (und die damit verbundenen Kosten) ohne Beeinträchtigung der Leistung unnötig reduziert werden können.

Sicherheitsberichte befasst sich mit der Verfolgung der Nutzung des Systems durch den Kunden und kann einschließen:

- Benutzervorgänge Überwachung; Dies erfordert das Aufnehmen der einzelnen Anforderungen durchgeführt, die von jedem Nutzer zusammen mit Datums- und Zeitangaben. Die Daten sollten so strukturiert sein, damit einen Administrator schnell die Folge von Operationen, die von einem bestimmten Benutzer über einen bestimmten Zeitraum zu rekonstruieren kann.
- Verfolgung der Ressourcennutzung durch Benutzer; Dies erfordert die Aufnahme wie Anfragen jeweils für einen Benutzer greift auf die verschiedenen Ressourcen, die das System bilden und für wie lange. Ein Administrator muss diese Daten verwenden, um eine Auslastung-Bericht vom Benutzer über einen bestimmten Zeitraum möglicherweise für Abrechnungszwecken generieren können.

Berichte können in vielen Fällen durch Batch-Prozesse nach einem festen Zeitplan generiert werden (Wartezeit ist nicht normalerweise ein Problem), aber sie sollten auch für Generation auf Ad-hoc-Basis bei Bedarf verfügbar sein.  Als Beispiel wenn Sie Daten in einer relationalen Datenbank wie Azure SQL-Datenbank speichern können Sie ein Tool wie SQL Server Reporting Services zu extrahieren und Formatieren von Daten und präsentieren es als eine Reihe von Berichten.

## Ähnliche Muster und Anleitungen
- Die Grundgröße-Anleitung beschreibt Verwaltung Aufwand verringern, indem Sie die Notwendigkeit eines Operators kontinuierlich überwachen die Leistung eines Systems und Entscheidungen über das Hinzufügen oder Entfernen von Ressourcen verringert.
- Die [Gesundheit-Endpunkt-Überwachung-Muster](https://msdn.microsoft.com/library/dn589789.aspx) Beschreibt die Funktionsprüfungen innerhalb einer Anwendung zu implementieren, die externe Tools in regelmäßigen Abständen über exponierte Endpunkte zugreifen können.
- Die [Vorrangwarteschlange](https://msdn.microsoft.com/library/dn589794.aspx) Muster veranschaulicht die Nachrichten in der Warteschlange zu priorisieren, so dass dringende Anfragen übermittelt werden und vor weniger dringende Nachrichten verarbeitet werden können.

## Weitere Informationen
- Der Artikel [Überwachung, Diagnose und Problembehandlung von Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md) auf der Microsoft-Website.
- Der Artikel [Azure: Telemetrie-Grundlagen und Fehlerbehebung](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) auf der Microsoft-Website.
- Die Seite [Erfassen Sie Protokollierung von Daten mithilfe von himmelblau Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx) auf der Microsoft-Website.
- Die Seite [Konfigurieren Diagnose für Azure Cloud Services und virtuelle Maschinen](https://msdn.microsoft.com/library/azure/dn186185.aspx) auf der Microsoft-Website.
- Die [Azurblaue Redis-Cache](http://azure.microsoft.com/services/cache/), [Himmelblau DocumentDB](http://azure.microsoft.com/services/documentdb/), und [HDInsight](http://azure.microsoft.com/services/hdinsight/) Seiten auf der Microsoft-Website.
- Die Seite [Service Bus Warteschlangen veranschaulicht](http://azure.microsoft.com/) auf der Microsoft-Website.
- Der Artikel [SQL Server Business Intelligence in himmelblau virtuellen Maschinen](https://msdn.microsoft.com/library/azure/jj992719.aspx) auf der Microsoft-Website.
- Die Seite [Grundlegendes zu Warnungen und Benachrichtigungen in Azure Überwachung](https://msdn.microsoft.com/library/azure/dn306639.aspx) auf der Microsoft-Website.
- Die [Anwendung-Einblicke](app-insights-get-started/) Seite auf der Microsoft-Website.
