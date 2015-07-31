<properties
   pageTitle="Content Delivery Network (CDN) guidance | Microsoft Azure"
   description="Guidance on Content Delivery Network (CDN) to deliver high bandwidth content hosted in Azure."
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

# Content Delivery Network (CDN)-Anleitung

![](media/best-practices-cdn/pnp-logo.png)

## Übersicht

Microsoft Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung für die Bereitstellung von hoher Bandbreite Inhalt, in Azure gehostet wird. Die CDN zwischenspeichert öffentlich zugängliche Objekte geladen von Azure Blob Storage oder ein Anwendungsordner auf strategisch platzierten Speicherorte, um die maximalen Bandbreite für die Lieferung bieten Inhalte an Benutzer. Es dient in der Regel für die Bereitstellung von statischen Inhalts wie Bilder, Stylesheets, Dokumente, Dateien, Client-seitige Scripts und HTML-Seiten.

Die wichtigsten Vorteile der Verwendung der CDN sind geringere Latenz und schnellere Bereitstellung von Content für Benutzer unabhängig von ihrer geographischen Lage in Bezug auf das Rechenzentrum, in dem die Anwendung gehostet wird und eine Verringerung der Belastung für die Anwendung selbst, weil es der Verarbeitung entlastet wird benötigt, um zugreifen und den Inhalt zu liefern. Diese Verringerung der Belastung kann helfen, um die Leistung und Skalierbarkeit der Anwendung sowie die hosting-Kosten zu minimieren, durch die Verringerung der Verarbeitungsressourcen benötigt, um ein bestimmtes Maß an Performance und Verfügbarkeit zu erhöhen.

Sie können möglicherweise andere Content-Delivery-Netzwerk-Systeme verwenden, die nicht implementiert werden, indem Azure in Ihren Anwendungen Wenn die Azure CDN nicht Ihren Anforderungen entspricht. Alternativ können Sie die Azure CDN für Anwendungen mit anderen Anbietern gehostet werden, indem man die statische Inhalte in Azure Speicher oder in Azure Compute Instanzen verwenden können.  
![](media/best-practices-cdn/CDN.png)

## Wie und warum die CDN verwendet wird

Typische Einsatzgebiete für die CDN sind:  

- Bereitstellung von statischen Ressourcen für die Client-Anwendungen oft von einer Website. Diese können sein, Bilder, Stylesheets, Dokumente, Dateien, Client-seitige Scripts, HTML-Seiten, HTML-Fragmente oder sonstigen Inhalten, die der Server nicht für jede Anforderung ändern muss. Die Anwendung kann Elemente zur Laufzeit zu erstellen und zur Verfügung stellen der CDN (z. B. durch Erstellen eine Liste der aktuellen Schlagzeilen), aber es tut dies nicht für jede Anforderung.

- Liefern öffentliche statische und dynamische Inhalte auf Geräte wie Mobiltelefone und Tablet-PCs, wo die Anwendung selbst ist ein Web-Service, der Kunden eine API anbietet. Neben anderen Inhalten, die der Server nicht für jede Anforderung ändern muss, kann die CDN statische Datasets für den Client zu verwenden, vielleicht um die Client-Benutzeroberfläche zu generieren - liefern. Beispielsweise könnte verwendet werden, um JSON oder XML-Dokumente zu liefern.

- Portion komplette Webseiten, die nur öffentliche statische Inhalte an Clients, bestehen, ohne irgendwelche speziellen berechnen Ressourcen.

- Streaming video-Dateien an den Client auf Nachfrage. Video profitiert von der Latenz und zuverlässige Konnektivität von der Global befindet sich Rechenzentren, die CDN-Verbindungen anbieten.

- Im Allgemeinen verbessert die Benutzerfreundlichkeit für Benutzer, vor allem diejenigen liegt weit Datacenter-Speicherort der Anwendung, die andernfalls höhere Latenz leiden würde. Ein Großteil der Gesamtgröße des Inhalts in einer Webanwendung ist oft statisch und mit dem CDN zur Erhaltung der Leistungsfähigkeit und allgemeine User experience und eliminierte die Verpflichtung zur Bereitstellung der Anwendung mehrere Rechenzentren hilft.

- Umgang mit der wachsenden Belastung Anwendungen dieser Service Mobilfunk- und Festnetz-Geräte, die Teil des Internet der Dinge (IoT) sind. Die große Anzahl solcher Geräte und Geräte könnte die Anwendung leicht überfordern, wenn es erforderlich war, um broadcast-Nachrichten verarbeiten und Verwalten von Firmware-Update-Verteilung direkt.

- Umgang mit Spitzen und Stoßspannungen Nachfrage ohne die Anwendung skaliert, die daraus resultierende Erhöhung, die laufenden Kosten zu vermeiden. Beispielsweise wenn ein Update für ein Betriebssystem, ein Hardware-Gerät z. B. ein bestimmtes Modell des Routers oder für ein Endgerät wie eine intelligente TV freigegeben wird werden ein großer Berg gefragt wie es in einem kurzen Zeitraum von Millionen von Benutzern und Geräten gedownloadet wird.  

- Die folgende Tabelle zeigt Beispiele für die mediane Zeit bis zum ersten Byte aus verschiedenen geografischen Standorten. Die Zielrolle Web wird Azure West uns bereitgestellt. Es gibt eine starke Korrelation zwischen größeren Schub durch die CDN und Nähe zu einem CDN-Knoten. Eine Liste der Azure CDN Knotenpositionen ist abrufbar unter [Azure Content Delivery Network (CDN) Knotenpositionen](http://msdn.microsoft.com/library/azure/gg680302.aspx).  

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Time to First Byte (Origin)</p></th><th><p>Time to First Byte (CDN)</p></th><th><p>% faster for CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* London, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Has an Azure CDN node in the same city.  
* Has an Azure CDN node in a neighboring city.  


## Herausforderungen  

Es gibt verschiedene Herausforderungen Rechnung zu tragen, wenn Sie beabsichtigen, die CDN verwenden:  

- **Bereitstellung** Sie müssen entscheiden, wo Inhalte für die CDN aus (der Ursprung, aus denen die CDN den Inhalt holt) geladen, und ob Sie den Inhalt in mehr als einem Storage-System bereitstellen müssen (wie z.B. den CDN und an einem anderen Speicherort).

- Ihre Anwendung Bereitstellungsmechanismus muss Konto Bereitstellen von statischen Inhalten und Ressourcen sowie die Bereitstellung von Dateien der Anwendung wie ASPX-Seiten berücksichtigen. Beispielsweise kann sie verlangen, einen separaten Schritt, Inhalte in Azure Blob-Speicher zu laden.

- **Versionsverwaltung und Cache-control** Überlegen Sie, wie Sie statischen Inhalte zu aktualisieren und neue Versionen bereitstellen. Die CDN bietet derzeit keinen Mechanismus zum Inhalt zu spülen, so dass neue Versionen verfügbar sind. Dies ist eine ähnliche Herausforderung für Client-seitige caching, z. B. in einem Webbrowser zu verwalten.

- **Testen** Es kann durch lokale Tests Ihrer CDN-Einstellungen beim entwickeln und Testen einer Anwendung lokal oder beim Staging schwierig sein.

- **SEO** Inhalte wie Bilder und Dokumente werden aus einer anderen Domäne gedient, wenn Sie verwenden sie CDN, die Auswirkungen auf SEO für diesen Inhalt haben wird.

- **Sicherheit** Viele CDN-Services wie Azure CDN bieten derzeit keine beliebige Zugriffssteuerung für den Inhalt.

- **Ausfallsicherheit** CDN ist eine mögliche Fehlerquelle für eine Anwendung. Es hat eine geringere Verfügbarkeit SLA als blob-Speicher (die zum Übermitteln von Inhalten direkt verwendet werden kann) also du musst möglicherweise erwägen ein Sicherungsmechanismus für kritische Inhalte zu implementieren.

- Kunden können aus einer Umgebung verbinden werden, die keinen Zugriff auf Ressourcen auf dem CDN zulässt. Dies könnte eine begrenzte Sicherheit Umwelt, die Beschränkung des Zugriffs auf nur eine Reihe von bekannten Quellen, oder das Laden von Ressourcen aus etwas anderes als der Ursprung der Seite verhindert. Daher wird eine alternative Implementierung erforderlich sein.

- Implementieren Sie einen Mechanismus, um Ihre Content-Verfügbarkeit durch die CDN überwachen.

Szenarien, wo CDN einschließen weniger nützlich sein kann:  

- Wenn der Inhalt einer geringen Trefferquote und so hat möglicherweise Zugriff paar Mal oder nur einmal während der Time-to-live, Gültigkeitsdauer. Zum ersten Mal, das ein Element heruntergeladen wird, entstehen Sie zwei Transaktionsgebühren (vom Ursprung auf die CDN und dann von der CDN an den Kunden).

- Wenn die Daten privat, wie für Großunternehmen oder Versorgung Kette Ökosysteme sind.


## Allgemeine Richtlinien und bewährte

Mit dem CDN ist eine gute Möglichkeit zur Minimierung der Belastung für Ihre Anwendung und Maximierung der Verfügbarkeit und Performance. Berücksichtigen Sie dies für alle entsprechenden Inhalte und Ressourcen, die Sie Anwendung verwendet. Beachten Sie die folgenden Punkte beim Entwerfen Ihrer Strategie die CDN verwenden:  

- **Herkunft ** Bereitstellung von Inhalten durch die CDN benötigt lediglich einen HTTP (Port 80) Endpunkt angeben, mit denen der CDN-Dienst zugreifen und den Inhalt zwischenspeichern. + Der Endpunkt kann einen Azure Blob Storage-Container angeben, der die statische Inhalte enthält, die Sie durch die CDN liefern möchten. Der Container muss als öffentlich markiert werden. Nur Blobs in einem öffentlichen Container, die öffentlichen Lesezugriff haben werden durch die CDN.

- Der Endpunkt kann einen Ordner mit dem Namen angeben. **CDN** Berechnen Sie Schichten (z. B. eine Webserverrolle oder eine virtuelle Maschine), im Stammverzeichnis einer Anwendung. Die Ergebnisse von Anforderungen für Ressourcen, einschließlich der dynamische Ressourcen wie ASPX-Seiten werden auf das CDN zwischengespeichert. Die minimale Cachefähigkeit beträgt 300 Sekunden. Kürzere Frist verhindert den Inhalt an die CDN bereitgestellt wird (siehe Abschnitt"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cache-Steuerung</a>"für weitere Informationen).

- Bei Verwendung von Azure Web-Sites, wird der Endpunkt in den Stammordner der Site durch die Standortwahl beim Erstellen der CDN-Instanz festgelegt. Der gesamte Inhalt der Website wird durch die CDN verfügbar sein.

- In den meisten Fällen wird das zeigen Ihrer CDN-Endpunkts an einen Ordner innerhalb der Compute-Schichten der Anwendung mehr Flexibilität und Kontrolle bieten. Beispielsweise erleichtert es aktuelle und zukünftige routing Anforderungen verwalten und statischen Inhalte wie Vorschaubildern dynamisch zu generieren.

- Abfrage-Strings können Sie um Objekte im Cache zu unterscheiden, wenn Inhalte aus dynamische Quellen wie ASPX-Seiten geliefert wird. Jedoch kann dieses Verhalten durch eine Einstellung im Verwaltungsportal deaktiviert, wenn Sie den CDN-Endpunkt angeben. Wenn Inhalte von Blob-Speicher zu liefern, werden als Zeichenfolgenliterale Abfragezeichenfolgen behandelt, also zwei Elemente, die den gleichen Namen aber unterschiedliche Abfragezeichenfolgen haben als gesonderte Elemente auf das CDN gespeichert werden.

- Sie können die URL-rewriting für Ressourcen wie z. B. Skripts und andere Inhalte zu vermeiden, Ihre Dateien in den CDN-Ursprungs-Ordner verschieben nutzen.

- Wenn Azure Speicher Blobs zum Inhalt für die CDN zu halten, ist die URL der Ressourcen in Blobs Groß-/Kleinschreibung für den Container und Blob.

- Bei der Verwendung von Azure Web-Sites, geben Sie den Pfad der CDN-Instanz in der Links zu Ressourcen. Folgendes gibt z. B. eine Bilddatei in das **Bilder und Videos** Ordner der Website, die durch die CDN übermittelt werden können:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Bereitstellung** Statischer Inhalte müssen bereitgestellt werden, und unabhängig von der Anwendung bereitgestellt werden, wenn Sie es nicht in der Bereitstellung-Paket der Anwendung oder des Prozesses einschließen. Prüfen, welche Auswirkungen dies die Versionsverwaltung Ansatz, die Sie verwenden, um die Anwendungskomponenten und die statische Ressource-Inhalte verwalten.

- Betrachten Sie, wie Bündelung (kombinieren mehrere Dateien in einer einzigen Datei) und Verkleinerung (entfernt überflüssige Zeichen wie Leerzeichen, Zeilenumbruchzeichen, Kommentare und andere Zeichen) für Script und CSS-Dateien behandelt werden. Diese häufig verwendete Techniken reduzieren können Ladezeiten für Kunden und sind kompatibel mit der Bereitstellung von Inhalten durch die CDN. Weitere Informationen finden Sie unter [Bündelung und Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Wenn Sie den Inhalt auf einen zusätzlichen Speicherort bereitstellen müssen, wird das ein zusätzlicher Schritt in der Bereitstellung-Prozess. Wenn die Anwendung den Inhalt für die CDN aktualisiert, muss vielleicht in regelmäßigen Abständen oder als Reaktion auf ein Ereignis, es aktualisierte Inhalte in jedem weiteren Orten sowie den Endpunkt für die CDN speichern.

- Ein CDN-Endpunkt für eine Anwendung bereitgestellt in Azure staging oder in den lokalen Azure-Emulator in Visual Studio kann nicht eingerichtet werden. Dies wirkt sich Unit-Tests, Funktionstests und endgültig vor der Bereitstellung testen. Sie müssen dies zulassen, durch einen alternativen Mechanismus implementieren. Beispielsweise könnten Sie vorab den Inhalt an die CDN mit einer temporären benutzerdefinierten Anwendung oder Dienstprogramm bereitstellen und führen Tests durch, während der Periode wurde es zwischengespeichert wird. Alternativ verwenden Sie kompilieren Richtlinien oder globale Konstanten steuern, wo die Anwendung lädt die Ressourcen aus. Ausführung im Debugmodus könnte es beispielsweise Ressourcen wie clientseitiges Skript Bündel und andere Inhalte aus dem lokalen Ordner zu laden und die CDN verwenden, wenn im Release-Modus ausgeführt.

- Die CDN unterstützt keine native Kompression-Funktionen. Jedoch liefern sie Inhalte, die bereits komprimiert ist, z. B. zip- und Gzip-Dateien. Wenn Sie einen Anwendungsordner als CDN Endpunkt verwenden, kann der Server einige Inhalte standardmäßig auf die gleiche Weise wie komprimieren wenn es direkt an einen Web-Browser oder eine andere Art von Client zu liefern. Dies beruht auf der **Accept-Encoding** Wert vom Client gesendet. In Azure wird standardmäßig automatisch Inhalte zu komprimieren, wenn CPU-Auslastung unter 50 % liegt. Ändern der Einstellungen und eventuell die Verwendung von einem Startaufgabe Kompression der dynamische Ausgabe in IIS einzuschalten, wenn Sie Cloud-Services verwenden, um die Anwendung zu hosten. Finden Sie unter [Gzip-Komprimierung mit Azure CDN über eine Web-Rolle aktivieren](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) für weitere Informationen.

- **Routing und Versionsverwaltung** Sie müssen möglicherweise verschiedene CDN-Instanzen verwenden; beispielsweise wenn Sie eine neue Version der Anwendung bereitstellen, sollten Sie ein anderes CDN verwenden. Wenn Sie als Inhalt Ursprung Azure Blob-Speicher verwenden, können einen separaten Speicher-Konto oder einem separaten Behälter zu erstellen und zeigen sie den CDN-Endpunkt. Bei Verwendung der **CDN** Stammordner der Anwendung als CDN Endpunkt, den Sie URL-rewriting Techniken verwenden können, um direkte Anforderungen an einen anderen Ordner.

- Verwenden Sie den Query-String nicht um verschiedene Versionen der Anwendung Links zu Ressourcen auf das CDN zu bezeichnen, da, wenn Sie Inhalte von Azure Blob Storage zu zeichnen, die Abfragezeichenfolge den Ressourcennamen (Blob-Name) gehört. Es kann auch beeinflussen, wie der Client die Ressourcen zwischenspeichert.

- Neue Versionen von statischen Inhalten bereitstellen, wenn Sie eine Anwendung aktualisieren kann eine Herausforderung sein, wenn die vorherigen Ressourcen auf das CDN zwischengespeichert werden. Weitere Informationen finden Sie im Abschnitt"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cache-Steuerung</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Cache-Steuerung**+ Prüfen, wie Sie das Zwischenspeichern, verwalten möchten und an welche Schichten der Anwendung. Beispielsweise können wenn Sie einen Ordner als Ursprung CDN verwenden Sie die Cachefähigkeit Seiten angeben, die Inhalt und Inhalt Ablauf für alle Ressourcen in einem bestimmten Ordner generieren. Sie können auch Cache-Eigenschaften für die CDN und für den Client mithilfe von standard-HTTP-Header angeben. Obwohl Sie sollten bereits verwalten Zwischenspeichern auf dem Server und Client mithilfe der CDN hilft Sie mehr bewusst machen, wie Ihr Inhalt zwischengespeichert wird und wo.

- Um zu verhindern, dass Objekte wird auf das CDN können Sie vom Ursprung (Blob Container oder Anwendung löschen **CDN** Root-Ordner), entfernen oder löschen Sie den Endpunkt CDN oder — im Falle von Blob-Speicher — machen den Container oder blob-private. Jedoch werden Elemente aus dem CDN entfernt, nur, wenn ihre Time-to-live (TTL) abläuft.

- Wenn kein Cache-Ablauf-Zeitraum (z. B., wenn Inhalte von Blob-Speicher geladen wird) angegeben wird, wird es bis zu 72 Stunden auf das CDN zwischengespeichert werden.

- In einer Webanwendung können festlegen die Zwischenspeicherung und Ablauf für alle Inhalte über die **clientCache** Element in der **system.webServer/staticContent** Abschnitt der Datei web.config. Sie können eine web.config-Datei in einem beliebigen Ordner platzieren, so dass sie die Dateien in diesem Ordner und die Dateien in allen Unterordnern betrifft.

- Wenn Sie eine dynamische Technik wie ASP.NET zum Erstellen des Inhalts für die CDN verwenden, stellen Sie sicher, dass Sie angeben der **Cache.SetExpires** Eigenschaft auf jeder Seite. Die CDN cachiert nicht die Ausgabe von Seiten, die die Standardeinstellung für die Cachefähigkeit der Öffentlichkeit verwenden.  Legen Sie die Cache-Ablauf-Periode auf einen geeigneten Wert um sicherzustellen, dass der Inhalt nicht verworfen und neu von der Anwendung in sehr kurzen Intervallen geladen.  

- **Sicherheit** Die CDN liefern Inhalte über HTTPS (SSL) verwenden die CDN bereitgestellten Zertifikats, sondern es werden auch über HTTP als auch. Sie können nicht HTTP-Zugriff auf Elemente in der CDN blockieren. Sie müssen möglicherweise HTTPS verwenden, um statische Inhalte anfordern, der auf Seiten, die über HTTPS (z. B. ein Warenkorb) zur Vermeidung von Browser-Warnungen über gemischte Inhalte geladen angezeigt wird.

- Viele CDN-Dienste, z. B. die Azure CDN, bieten derzeit keine Einrichtungen für die Steuerung des Zugriffs auf Zugriff auf die Inhalte zu sichern. Shared Access Signaturen (SAS) können nicht mit dem CDN.

- Werben Sie Client-seitige Scripts mit dem CDN, können Sie Probleme auftreten, wenn diese Skripts verwenden, ein **XMLHttpRequest** Aufruf von HTTP-Anforderungen für andere Ressourcen wie Daten, Bilder oder Schriften in einer anderen Domäne zu machen. Viele Webbrowser verhindern Kreuz-Herkunft Ressourcenfreigabe (COR), wenn der Webserver so konfiguriert ist, um die entsprechende Antwort-Header festzulegen. Weitere Informationen zu Flutes finden Sie im Abschnitt "Gefahr Mitigation" im guide <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">API-Sicherheitsaspekte</span>. Sie können konfigurieren, dass die CDN zur Unterstützung von Cor: + Wenn die Herkunft aus dem liefern Sie Inhalte ein Azure Blob-Speicher ist, können Sie hinzufügen ein **CorsRule** um die Diensteigenschaften. Die Regel kann die zulässige Ursprünge für Flutes Anfragen, die zulässigen Methoden wie GET und das maximale Alter in Sekunden für die Regel (der Zeitraum, innerhalb, den der Client verknüpften Ressourcen anfordern muss, nach dem Laden des Originalinhalts) angeben. Weitere Informationen finden Sie unter [Cross-Origin Ressourcenfreigabe (COR) Unterstützung für die Azure Storage Services](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Wenn die Herkunft aus dem Sie von Inhalten Bereitstellung sind wie z. B. einen Ordner innerhalb der Anwendung ist die **CDN** Root-Ordner, Sie können ausgehende Regeln konfigurieren, in der Konfigurationsdatei der Anwendung festlegen einer **Zugangs-Kontrolle-erlauben-Origin** Header für alle Antworten. Weitere Informationen zur Verwendung von Rewrite-Regeln finden Sie unter [URL-Rewrite-Modul](http://www.iis.net/learn/extensions/url-rewrite-module). Beachten Sie, dass diese Technik nicht möglich ist, bei der Verwendung von Azure Web-Sites.

- **Benutzerdefinierten Domänen**+ Die meisten CDNs, einschließlich der Azure CDN, ermöglichen es Ihnen, um benutzerdefinierte Domänennamen angeben und es verwenden, um Zugriff auf Ressourcen durch die CDN. Sie können auch eine benutzerdefinierte Sub-Domain Name using Einrichten einer **CNAME** Notieren Sie in Ihrem DNS. Mit diesem Ansatz kann eine zusätzliche Sicherheitsebene der Abstraktion und Kontrolle bieten.

- Wenn Sie verwenden ein **CNAME**, können Sie nicht (zu diesem Zeitpunkt wurde dieses Handbuch geschrieben) auch SSL verwenden, da die CDN seinen eigenen einzelne SSL-Zertifikat verwendet, und dies nicht Ihrer benutzerdefinierten Domäne/Sub-Domain-Namen entspricht.

- **CDN-fallback** Sie sollten überlegen, wie Ihre Anwendung mit einem Fehler oder einer vorübergehenden Ausfall der CDN fertig werden. Clientanwendungen möglicherweise Kopien der Ressourcen verwendet, die lokal (auf dem Client) während der früheren Anforderungen zwischengespeichert wurden, oder sie können Code, erkennt Fehler und fordert stattdessen Ressourcen vom Ursprung (Anwendungsordner oder Azure Blob-Container mit den Ressourcen) Wenn die CDN nicht verfügbar ist.

- **SEO** Wenn SEO ein wichtiger Aspekt in der Anwendung ist, stellen Sie sicher Sie: + Include a **Rel** kanonische Kopfzeile auf jeder Seite oder Ressource.

- Verwendung einer **CNAME** Sub-Domain aufnehmen und Zugriff auf die Ressourcen, die mit diesem Namen.

- Beachten Sie die Auswirkungen der Tatsache, dass die IP-Adresse die CDN sein kann, ein Land oder eine Region, die von der Anwendung selbst unterscheidet.

- Bei der Verwendung von Azure Blob-Speicher als Ursprung behalten Sie die gleiche Dateistruktur für Ressourcen auf dem CDN wie in den Anwendungsordner.


- **Überwachung & Protokollierung** Gehören die CDN als Teil Ihrer Anwendung, die Überwachung der Strategie zu erkennen und Fehler zu messen oder Verlängerung der Wartezeit vorkommen.

- Aktivieren Sie der Protokollierung für die CDN und fügen Sie sie als Teil Ihrer täglichen Arbeit.

- **Kosten Implikation** Sie zahlen für beide ausgehenden Datenübertragungen von der CDN und Speicher-Transaktionen, wenn die CDN Daten aus Ihrer Anwendung lädt. Sie sollten realistische Cache Ablauf Perioden für Inhalt, um die frische zu gewährleisten, aber nicht ganz so kurze, wiederholte Nachladen von Inhalten aus der Anwendung verursachen oder blob-Speicher für die CDN. Jedoch erschweren Ablauf sehr lange Zeiträume Elemente aus dem CDN entfernt, weil Sie, für sie warten müssen ablaufen.

- Die selten runtergeladen werden entstehen zwei Transaktionsgebühren, ohne Angabe erheblichen Rückgang der Serverlast.  



## Beispiel-code
Dieser Abschnitt enthält einige Beispiele für Code und Techniken zum Arbeiten mit dem CDN.  


## URL-rewriting
Folgende außer aus der Datei Web.config im Stammverzeichnis einer Anwendung gehostete Cloud-Dienste veranschaulicht führen Sie URL-rewriting bei Verwendung der CDN. Anforderungen aus dem CDN für Inhalte, die es zwischengespeichert werden werden auf bestimmte Ordner im Stammverzeichnis Anwendung basierend auf dem Typ der Ressource (z. B. Skripts und Bilder) umgeleitet.  

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Der Zusatz von Rewrite-Regeln führt die folgenden Umleitungen:  

- Die erste Regel können Sie eine Version in den Dateinamen einer Ressource einbetten, die dann ignoriert wird. Zum Beispiel **Dateiname_V123.jpg **ist als umgeschrieben. **Filename.jpg**.

- Die nächsten vier Regeln zeigen, wie Anforderungen umleiten, wenn Sie nicht, dass die Ressourcen in einem Ordner mit dem Namen speichern möchten **CDN** in der Wurzel die Rolle "Webserver". Ordnen Sie die Regeln der **CDN/Images**, **CDN/Content**, **CDN/Scripts**, und **CDN/Bündel** URLs zu ihren jeweiligen Stammordner in der Rolle "Webserver".
URL-rewriting müssen Sie einige Änderungen vornehmen, um die Bündelung von Ressourcen.  


## Bündelung und Verkleinerung ##

Bündelung und Verkleinerung können von ASP.NET behandelt werden. In einem MVC-Projekt definieren Sie Ihr Bündel in **BundleConfig.cs**. Ein Verweis auf das minimierte Skript-Bundle entsteht durch Aufrufen der **Script.Render** Methode, in der Regel in Code in der View-Klasse. Diese Referenz enthält eine Abfragezeichenfolge, die einen Hash enthält, die auf den Inhalt des Bündels basiert. Wenn die Bündel-Inhalt ändern, ändert sich der generierte Hash auch.  
Azure CDN Instanzen verfügen standardmäßig über die **Abfrage-String-Status** -Einstellung deaktiviert. Für die aktualisierte Skript Pakete von der CDN richtig behandelt werden, Sie müssen aktivieren die **Abfrage-String-Status** Einstellung für die CDN-Instanz. Beachten Sie, dass es vielleicht eine Stunde oder länger vor dem Erstellen der CDN und Ändern der Einstellungen wirksam.  


## Weitere Informationen
+ [Azurblaue CDN](http://azure.microsoft.com/services/cdn/)
+ [Überblick über die Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Inhalt von himmelblau CDN in der Webanwendung zu bedienen](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Integration von Cloud-Service mit Azure CDN](cdn-cloud-service-with-cdn.md)
+ [Best Practices für die azurblaue Content Delivery Networks](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)
