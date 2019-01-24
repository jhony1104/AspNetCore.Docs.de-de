---
uid: signalr/overview/older-versions/signalr-performance
title: SignalR-Leistung (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: SignalR-Leistung
ms.author: bradyg
ms.date: 07/03/2013
ms.assetid: 9594d644-66b6-4223-acdd-23e29a6e4c46
msc.legacyurl: /signalr/overview/older-versions/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: 4158cb055088f3da752020e577007ffe80856b60
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837727"
---
<a name="signalr-performance-signalr-1x"></a>SignalR-Leistung (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema wird beschrieben, wie für entwerfen, zu messen und Verbessern der Leistung in einer SignalR-Anwendung.


Aktuelle Darstellung der SignalR-Leistung und Skalierung, finden Sie unter [Skalierung im Web in Echtzeit mit SignalR für ASP.NET](https://channel9.msdn.com/Events/Build/2013/3-502).

Dieses Thema enthält folgende Abschnitte:

- [Überlegungen zum Entwurf](#design)
- [Optimieren der Leistung des Servers SignalR](#tuning)
- [Behandeln von Leistungsproblemen](#troubleshooting)
- [Verwenden von SignalR-Leistungsindikatoren](#perfcounters)
- [Verwenden die übrigen Leistungsindikatoren](#othercounters)
- [Weitere Ressourcen](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Dieser Abschnitt beschreibt die Muster, die implementiert werden können, während des Entwurfs einer SignalR-Anwendung, um sicherzustellen, dass die Leistung nicht beeinträchtigt wird wird, indem Sie unnötigen Netzwerkdatenverkehr generieren.

### <a name="throttling-message-frequency"></a>Einschränkung der Häufigkeit der Nachricht

Die meisten Anwendungen müssen nicht selbst in Anwendungen, die Nachrichten mit hoher Frequenz (z. B. eine Echtzeit-Gaming-Anwendung) sendet, mehr als ein paar Nachrichten pro Sekunde zu senden. Um die Menge des Datenverkehrs zu verringern, die von jedem Client generiert, eine Meldungsschleife kann implementiert werden, dass Warteschlangen und sendet Nachrichten häufig nicht mehr als einer festen Rate (d. h. bis zu einer bestimmten Anzahl von Nachrichten gesendet pro Sekunde, wenn sich Nachrichten in dieser Zeit in euerstellungsintervall gesendet werden). Eine beispielanwendung, die Nachrichten an eine bestimmte Rate an (von Client und Server) drosselt, finden Sie unter [Echtzeitnachrichten mit SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).

### <a name="reducing-message-size"></a>Verringern der Größe

Sie können die Größe einer SignalR-Nachricht reduzieren, durch die Reduzierung der serialisierten Objekte. Im Server-Code, wenn Sie ein Objekt senden, das Eigenschaften, die nicht enthält übertragen werden müssen, verhindern, dass diese Eigenschaften serialisiert werden, indem Sie mit der `JsonIgnore` Attribut. Die Namen von Eigenschaften werden auch in der Nachricht gespeichert. die Namen der Eigenschaften können verkürzt werden, mit der `JsonProperty` Attribut. Im folgenden Codebeispiel wird veranschaulicht, wie Sie ausschließen möchten, dass eine Eigenschaft, die an den Client gesendet werden, und Eigenschaftsnamen zu verkürzen:

**.NET Server-Code, die zeigt, das JsonIgnore-Attribut, um Daten auszuschließen, die an den Client gesendet werden, und das Attribut "jsonproperty" Reduzieren der Größe**

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

Um die Lesbarkeit beibehalten / Verwaltbarkeit im Clientcode, den abgekürzten Namen können neu zugeordnete, Benutzerfreundlicher Namen nach dem die Nachricht empfangen wird. Das folgende Codebeispiel veranschaulicht eine Möglichkeit der neuzuordnung verkürzter Namen länger werden, durch die Definition eines Nachrichtenvertrags (Zuordnung), und Verwenden der `reMap` Funktion, die den Vertrag für die optimierte Nachrichtenklasse gelten:

**Die clientseitige JavaScript-Code, der zugeordnet verkürzt Eigenschaftsnamen für Menschen lesbaren Namen**

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

Namen können in den Nachrichten vom Client an den Server auch verkürzt werden mit derselben Methode.

Reduziert den Speicherbedarf (d. h. die Menge des Arbeitsspeichers, die für die Nachricht verwendet) der Nachricht Objekt kann auch die Leistung verbessern. Z. B. wenn das gesamte Spektrum ein `int` ist nicht erforderlich, eine `short` oder `byte` kann stattdessen verwendet werden.

Da Nachrichten im Nachrichtenbus im Arbeitsspeicher des Servers, verringern der Größe der Nachrichten gespeichert werden, können Sie auch eine Adresse Server Arbeitsspeicherproblemen.

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a>Optimieren der Leistung des Servers SignalR

Die folgenden Konfigurationseinstellungen können verwendet werden, um den Server für eine bessere Leistung in einer SignalR-Anwendung zu optimieren. Allgemeine Informationen zum Verbessern der Leistung in einer ASP.NET-Anwendung finden Sie unter [Verbessern der Leistung von ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).

**SignalR-Konfigurationseinstellungen**

- **DefaultMessageBufferSize**: Standardmäßig behält SignalR 1000-Nachrichten im Arbeitsspeicher pro Hub pro Verbindung. Wenn große Nachrichten verwendet werden, kann dies Arbeitsspeicherproblemen erstellen, die durch eine Reduzierung dieses Werts verringert werden kann. Diese Einstellung kann festgelegt werden, der `Application_Start` -Ereignishandler in einer ASP.NET-Anwendung oder in der `Configuration` Methode eine OWIN-Startup-Klasse in einer selbst gehosteten Anwendung. Im folgende Beispiel wird veranschaulicht, wie kann ich diesen Wert verringern, um verringern den Speicherbedarf Ihrer Anwendung, um den Server verwendete Speichermenge zu reduzieren:

    **.NET Server-Code in "Global.asax" zum Verringern der Größe des Nachrichtenpuffers Standard**

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

**IIS-Konfigurationseinstellungen**

- **Maximale Anzahl gleichzeitiger Anforderungen pro Anwendung**: Erhöhen der Anzahl von gleichzeitigen IIS erhöht Anforderungen Serverressourcen verfügbar sind, Anforderungen zu verarbeiten. Der Standardwert ist 5000. Um diese Einstellung erhöhen, führen Sie die folgenden Befehle an einer Eingabeaufforderung mit erhöhten Rechten aus:

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

**ASP.NET-Konfigurationseinstellungen**

Dieser Abschnitt enthält Konfigurationseinstellungen, die festgelegt werden können, in der `aspnet.config` Datei. Diese Datei befindet sich in einem von zwei Speicherorten, je nach Plattform:

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

Die folgenden: ASP.NET-Einstellungen, die SignalR-Leistung verbessern können

- **Maximale Anzahl gleichzeitiger Anforderungen pro CPU**: Erhöhen diese Einstellung möglicherweise Leistungsengpässe verringern. Um diese Einstellung erhöhen, fügen Sie die folgende Konfigurationseinstellung der `aspnet.config` Datei:

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- **Begrenzung für Anforderungswarteschlange**: Wenn die Gesamtzahl der Verbindungen überschreitet die `maxConcurrentRequestsPerCPU` festlegen, ASP.NET startet drosselungsanforderungen über eine Warteschlange. Um die Größe der Warteschlange zu erhöhen, können Sie erhöhen die `requestQueueLimit` festlegen. Zu diesem Zweck fügen Sie die folgende Konfigurationseinstellung der `processModel` Knoten `config/machine.config` (statt `aspnet.config`):

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a>Behandeln von Leistungsproblemen

Dieser Abschnitt beschreibt die Möglichkeiten, um Leistungsengpässe in Ihrer Anwendung zu finden.

### <a name="verifying-that-websocket-is-being-used"></a>Überprüfen, dass WebSocket verwendet wird

Obwohl SignalR eine Vielzahl von Transporten für die Kommunikation zwischen Client und Server verwenden kann, werden WebSocket bietet einen deutlichen Leistungsvorteil, und sollte verwendet werden, wenn der Client und Server unterstützen. Um zu bestimmen, wenn sich Client und Server die Anforderungen für den WebSocket erfüllt, finden Sie unter [Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports). Um zu bestimmen, welcher Transport in Ihrer Anwendung verwendet wird, können Sie das browserentwicklertools verwenden, und überprüfen die Protokolle, um festzustellen, welcher Transport für die Verbindung verwendet wird. Weitere Informationen finden Sie unter den Browser-Entwicklungstools in Internet Explorer und Chrome, finden Sie unter [Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a>Verwenden von SignalR-Leistungsindikatoren

In diesem Abschnitt wird beschrieben, wie aktivieren und Verwenden von SignalR-Leistungsindikatoren finden Sie in der `Microsoft.AspNet.SignalR.Utils` Paket.

### <a name="installing-signalrexe"></a>Installieren von signalr.exe

Performance-Leistungsindikatoren können mit dem Server mithilfe eines Dienstprogramms namens SignalR.exe hinzugefügt werden. Um dieses Hilfsprogramm zu installieren, gehen Sie folgendermaßen vor:

1. Wählen Sie in Visual Studio **Tools** > **NuGet Paket-Manager** > **NuGet-Pakete verwalten Lösung**
2. Suchen Sie nach **signalr.utils**, und wählen Sie installieren.

    ![](signalr-performance/_static/image1.png)
3. Akzeptieren des Lizenzvertrags zum Installieren des Pakets an.
4. SignalR.exe installiert werden soll `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.

### <a name="installing-performance-counters-with-signalrexe"></a>Installieren von Leistungsindikatoren mit SignalR.exe

Führen Sie zum Installieren von SignalR-Leistungsindikatoren, SignalR.exe in einer Eingabeaufforderung mit erhöhten Rechten, mit dem folgenden Parameter:

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

Führen Sie zum Entfernen von SignalR-Leistungsindikatoren SignalR.exe in einer Eingabeaufforderung mit erhöhten Rechten, mit dem folgenden Parameter:

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a>SignalR-Leistungsindikatoren

Das Dienstprogramme-Paket installiert die folgenden Leistungsindikatoren. Die "Gesamt" Indikatoren messen die Anzahl der Ereignisse, seit der letzten Anwendungspool oder die Server neu starten.

**Verbindungsmetriken**

Die folgenden Metriken messen Sie die Verbindung Objektlebensdauer-Ereignisse, die auftreten. Weitere Informationen finden Sie unter [verstehen und Verbindung Objektlebensdauer-Ereignisse behandeln](../guide-to-the-api/handling-connection-lifetime-events.md).

- **Verbindungen mit verbunden**
- **Verbindungen, die die Verbindung wiederhergestellt**
- **Verbindungen Disonnected**
- **Aktuelle für Verbindungen**

**Nachrichtenmetrik**

Die folgenden Metriken messen, den Nachrichtenverkehr, die von SignalR generiert wird.

- **Gesamtanzahl der empfangenen Nachrichten der Verbindung**
- **Verbindung gesendeten Daten insgesamt**
- **Verbindung empfangene Nachrichten/Sek.**
- **Verbindung gesendete Nachrichten/Sek.**

**Message Bus-Metriken**

Die folgenden Metriken Messen von Datenverkehr über die interne SignalR-Nachrichtenbus der Warteschlange, in dem alle eingehende und ausgehende SignalR-Nachrichten platziert werden. Eine Nachricht ist **veröffentlicht** Wenn gesendet oder übertragen. Ein **Abonnenten** in diesem Kontext wird ein Abonnement für den Nachrichtenbus; Dies sollte die Anzahl der Clients und den Server selbst entsprechen. Ein **Worker zugewiesen** ist eine Komponente, die Daten an die aktiven Verbindungen, sendet eine **ausgelasteten Worker** ist eine, die aktiv eine Nachricht sendet.

- **Nachrichtenbus Nachrichten empfangen gesamt**
- **Nachrichtenbus Nachrichten empfangen/Sekunde**
- **Message Bus-Nachrichten veröffentlicht gesamt**
- **Nachrichtenbus Nachrichten veröffentlicht/Sekunde**
- **Message Bus Abonnenten aktuelle**
- **Message Bus Abonnenten gesamt**
- **Message Bus Abonnenten/Sekunde**
- **Nachrichtenbus Worker zugewiesen**
- **Message Bus ausgelasteter Worker**
- **Message Bus-Themen aktuelle**

**Fehlermetriken**

Die folgenden Metriken Messen von SignalR-Nachrichtenverkehr generierten Fehler. **Hub-Lösung** Fehler auftreten, wenn Sie einen Hub oder hubmethode nicht aufgelöst werden kann. **Hubaufruf** Fehler sind Ausnahmen, die beim Aufruf einer hubmethode. **Transport** treten Verbindungsfehler während einer HTTP-Anforderung oder Antwort ausgelöst wurde.

- **Fehler: Alle gesamt**
- **Fehler: Alle/Sekunde**
- **Fehler: Gesamtanzahl der Hub-Lösung**
- **Fehler: Hub-Lösung/Sekunde**
- **Fehler: Hub-aufrufen gesamt**
- **Fehler: Hub-aufrufen/Sek.**
- **Fehler: Transport gesamt**
- **Fehler: Transport/Sek.**

**Horizontale Skalierung Metriken**

Die folgenden Metriken messen Datenverkehr und Fehler, die durch den Datenanbieter mit horizontaler Skalierung generiert. Ein **Stream** in diesem Kontext ist eine Mengen-Einheit der Anbieter mit horizontaler Skalierung verwendet; dies ist eine Tabelle aus, wenn SQL Server verwendet wird, ein Thema, wenn Service Bus verwendet wird und ein Abonnement, wenn Redis verwendet wird. In der Standardeinstellung nur einen Stream wird verwendet, aber dies kann über die Konfiguration auf SQL Server und Service Bus erhöht werden. Ein **Pufferung** Stream ist ein, der einem fehlerhaften Zustand befindet, wenn der Datenstrom in den Fehlerzustand ist, alle Nachrichten, die an die Backplane gesendet funktioniert erst, sofort der Stream nicht mehr fehlschlägt bzw. fehlerhaft ist. Die **Länge der Sendewarteschlange** ist die Anzahl der Nachrichten, die gesendet, aber noch nicht gesendet wurden.

- **Nachrichtenbus mit horizontaler Skalierung Nachrichten empfangen/Sekunde**
- **Horizontale Skalierung in Streams gesamt**
- **Horizontale Skalierung in Streams öffnen**
- **Horizontale Skalierung in Streams Pufferung**
- **Horizontale Skalierung gesamt**
- **Horizontale Skalierung/Sek.**
- **Länge der Sendewarteschlange für horizontale Skalierung**

Weitere Informationen dazu, was diese Indikatoren messen, finden Sie unter [SignalR Scaleout mit Azure Service Bus](scaleout-with-windows-azure-service-bus.md).

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a>Verwenden die übrigen Leistungsindikatoren

Die folgenden Leistungsindikatoren können auch bei der Überwachung der Leistung Ihrer Anwendung nützlich sein.

**Arbeitsspeicher**

- .NET CLR-Speicher Anzahl von Bytes in allen Heaps (für w3wp)

**ASP.NET**

- Aktuelle für "ASP.net\aktuelle Anforderungen"
- ASP.NET\Queued
- ASP.NET\Rejected

**CPU**

- Information\Processor Prozessorzeit

**TCP/IP**

- TCPv6/hergestellten Verbindungen.
- TCPv4/hergestellten Verbindungen.

**Webdienst**

- Web-Dienst\Aktuelle Verbindungen
- Service\Maximum Webverbindungen

**Threading**

- .NET CLR-Sperren\# aktuelle logische Threads
- .NET CLR LocksAnd Threads\# aktuelle physische Threads

<a id="otherresources"></a>

## <a name="other-resources"></a>Weitere Ressourcen

Weitere Informationen zu ASP.NET zur Leistungsüberwachung und-Optimierung finden Sie unter den folgenden Themen:

- [ASP.NET Performance Overview (Die Leistung von ASP.NET im Überblick)](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)
- [ASP.NET-Thread Usage on IIS 7.5, IIS 7.0 und IIS 6.0](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [&lt;ApplicationPool&gt; -Element (Webeinstellungen)](https://msdn.microsoft.com/library/dd560842.aspx)
