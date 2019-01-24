---
uid: signalr/overview/performance/signalr-connection-density-testing-with-crank
title: Verbindungs-Dichte SignalR mit Crank testen | Microsoft-Dokumentation
author: bradygaster
description: Verbindungs-Dichte SignalR mit Crank testen
ms.author: bradyg
ms.date: 02/22/2015
ms.assetid: 148d9ca7-1af1-44b6-a9fb-91e261b9b463
msc.legacyurl: /signalr/overview/performance/signalr-connection-density-testing-with-crank
msc.type: authoredcontent
ms.openlocfilehash: 40c9764f0c47b83df8300553b4b290429937345c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836544"
---
<a name="signalr-connection-density-testing-with-crank"></a>Verbindungs-Dichte SignalR mit Crank testen
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieser Artikel beschreibt, wie das Crank-Tool zum Testen einer Anwendung mit mehreren simulierten Clients.


Sobald Ihre Anwendung in der hostumgebung (entweder eine Azure web-Rolle, IIS, oder für selbst gehostete Einsatz von Owin) ausgeführt wird, können Sie die Antwort der Anwendung auf ein hohes Maß an Verbindung Dichte, die mit dem Tool Crank testen. Die Hostingumgebung kann ein Server (Internet Information Services, IIS), einem Owin-Host oder einer Azure-Webrolle sein. (Beachten Sie: Leistungsindikatoren sind nicht verfügbar auf Azure App Service-Web-Apps, sodass Sie nicht um ein Dichte Verbindungstest Leistungsdaten erhalten.)

Verbindung Dichte bezieht sich auf die Anzahl der gleichzeitigen TCP-Verbindungen, die auf einem Server hergestellt werden kann. Jede TCP-Verbindung eine eigene Rechenaufwand, und Öffnen einer großen Anzahl von Verbindungen im Leerlauf werden schließlich einen Speicherengpass erstellen.

[SignalR-Codebasis](https://github.com/signalr/signalr) enthält ein Auslastungstest-Tool namens **Anforderungen**. Die neueste Version des Crank befinden sich [der entwicklungsverzweigung](https://github.com/SignalR/signalr/tree/dev) auf GitHub. Sie können eine ZIP-Archiv der entwicklungsverzweigung von SignalR-Codebasis Datei [hier](https://github.com/SignalR/SignalR/archive/dev.zip).

Crank kann zu vollständig sättigenden der Arbeitsspeicher des Servers verwendet werden, um die Gesamtzahl von Verbindungen im Leerlauf auf die Serverhardware möglich zu berechnen. Alternativ können Sie auch Kurbel verwenden für den Auslastungstest den Server unter eine bestimmte Menge an Arbeitsspeicher durch langsame Verbindungen bis eine bestimmte Anzahl oder einen bestimmten speicherschwellenwert erreicht ist.

Beim Testen ist es wichtig, die remote-Clients zu verwenden, um einen beliebigen Wettbewerb, für die Ressourcen (z. B. TCP-Verbindungen und Arbeitsspeicher) zu vermeiden. Überwachen Sie den Clients, um sicherzustellen, dass sie kein Engpässe erreichen, die den Server verhindert möglicherweise, dessen voller Kapazität (Arbeitsspeicher oder CPU) erreicht. Sie müssen möglicherweise die Anzahl der Clients zu erhöhen, um vollständig vom Server geladen werden.

### <a name="running-a-connection-density-test"></a>Ausführen eines Tests der Verbindungs-Dichte

In diesem Abschnitt wird beschrieben, die erforderlichen Schritte zum ein Verbindungstest-Dichte auf einer SignalR-Anwendung ausgeführt wird.

1. Herunterladen und erstellen die [entwicklungsverzweigung von SignalR-Codebasis](https://github.com/SignalR/SignalR/archive/dev.zip). Wechseln Sie in einer Eingabeaufforderung den Befehl zu &lt;Projektverzeichnis&gt;\src\Microsoft.AspNet.SignalR.Crank\bin\debug.
2. Stellen Sie Ihre Anwendung in der vorgesehenen hostumgebung bereit. Notieren Sie sich den Endpunkt die Anwendung verwendeten; z. B. in der Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md), der Endpunkt ist `http://<yourhost>:8080/signalr`.
3. Installieren Sie [SignalR-Leistungsindikatoren](signalr-performance.md#perfcounters) auf dem Server. Wenn Ihre Anwendung in Azure ausgeführt wird, finden Sie unter [mithilfe von SignalR-Leistungsindikatoren in einer Azure-Webrolle](using-signalr-performance-counters-in-an-azure-web-role.md).

Sobald heruntergeladen und erstellt die Codebasis und Leistungsindikatoren auf dem Host installiert haben, das Befehlszeilentool "Crank" finden Sie in der `src\Microsoft.AspNet.SignalR.Crank\bin\Debug` Ordner.

Optionen für das Tool Crank sind verfügbar:

- **/?**: Zeigt die Hilfe an. Die verfügbaren Optionen werden auch angezeigt, wenn die **Url** Parameter ausgelassen wird.
- **/Url**: Die URL für SignalR-Verbindungen. Dieser Parameter ist erforderlich. Für eine SignalR-Anwendung, die Verwendung der standardzuordnung, endet der Pfad in "/ Signalr".
- **/Transport**: Der Name des Transports verwendet werden soll. Der Standardwert ist `auto`, wählt die verfügbare Protokoll. Unter anderem `WebSockets`, `ServerSentEvents`, und `LongPolling` (`ForeverFrame` ist keine Option für Crank, seit der .NET Client statt Internet Explorer verwendet wird). Weitere Informationen dazu, wie SignalR Transporte auswählt, finden Sie unter [Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).
- **/BatchSize**: Die Anzahl der Clients, die in jedem Batch hinzugefügt werden soll. Der Standardwert ist 50.
- **/ConnectInterval**: Das Intervall in Millisekunden zwischen dem Hinzufügen von Verbindungen. Der Standard ist 500.
- **/ Verbindungen**: Die Anzahl der Verbindungen verwendet, um Auslastungstest-Anwendung. Der Standardwert ist 100.000.
- **/ConnectTimeout**: Das Timeout in Sekunden vor Abbruch des Testlaufs. Der Standardwert ist 300.
- **MinServerMBytes**: Die minimale Megabyte (MB) erreicht. Der Standard ist 500.
- **SendBytes**: Die Größe der Nutzlast in Bytes an den Server gesendet werden soll. Der Standard ist 0.
- **SendInterval**: Die Verzögerung in Millisekunden zwischen Nachrichten an den Server. Der Standard ist 500.
- **SendTimeout**: Das Timeout in Millisekunden für Nachrichten an den Server. Der Standardwert ist 300.
- **ControllerUrl**: Die Url, in denen ein Client einen Controller-Hub hostet. Der Standardwert ist null (kein Controller-Hub). Der Controller-Hub wurde gestartet, beim Start der Sitzung Crank. keine weiteren erfolgt, wenden Sie sich an, zwischen dem Controller-Hub und einer Kurbel.
- **NumClients**: Die Anzahl der simulierten Clients an die Anwendung eine Verbindung herzustellen. Der Standardwert ist 1.
- **Logfile**: Der Dateiname für die Protokolldatei für den Testlauf. Die Standardeinstellung ist `crank.csv`.
- **SampleInterval**: Die Zeit in Millisekunden zwischen Proben von Leistungsindikatoren. Der Standard ist 1000.
- **SignalRInstance**: Der Instanzname für die Leistungsindikatoren auf dem Server. Der Standardwert ist der Zustand der Client-Verbindung verwenden.

### <a name="example"></a>Beispiel

Der folgende Befehl testet eine Site mit dem Namen `pfsignalr` mithilfe von 100 Verbindungen in Azure, die eine Anwendung an Port 8080 mit einem Hub, mit dem Namen "ControllerHub" hostet.

`crank /Connections:100 /Url:http://pfsignalr.cloudapp.net:8080/signalr`
