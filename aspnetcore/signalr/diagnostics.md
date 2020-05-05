---
title: Protokollierung und Diagnose in ASP.net CoreSignalR
author: anurse
description: Erfahren Sie, wie Sie Diagnoseinformationen aus SignalR Ihrer ASP.net Core-App erfassen.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 5fda458c2418c3570d55d551ce5144730afd7f85
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767225"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Protokollierung und Diagnose in ASP.net Core signalr

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus Ihrer ASP.net Core signalr-APP, um Probleme zu beheben.

## <a name="server-side-logging"></a>Server seitige Protokollierung

> [!WARNING]
> Server seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Da signalr Teil ASP.net Core ist, verwendet es das ASP.net Core Protokollierungs System. In der Standardkonfiguration protokolliert signalr nur wenige Informationen, aber dies kann konfiguriert werden. Ausführliche Informationen zum Konfigurieren ASP.net Core Protokollierung finden Sie in der Dokumentation zur [ASP.net Core Protokollierung](xref:fundamentals/logging/index#configuration) .

Signalr verwendet zwei Kategorien von Kategorien:

* `Microsoft.AspNetCore.SignalR`&ndash; Protokolle im Zusammenhang mit hubprotokollen, das Aktivieren von Hubs, das Aufrufen von Methoden und andere hubbezogene Aktivitäten.
* `Microsoft.AspNetCore.Http.Connections`&ndash; Protokolle, die sich auf Transporte beziehen, wie z. b. websockets, lange Abruf Ereignisse und Server gesendete Ereignisse und eine signalr-Infrastruktur auf niedriger Ebene.

Um ausführliche Protokolle von signalr zu aktivieren, konfigurieren Sie beide vorangehenden Präfixe auf `Debug` der Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente `LogLevel` zum unter Abschnitt in `Logging`hinzufügen:

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Sie können dies auch in Code in der `CreateWebHostBuilder` -Methode konfigurieren:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem fest:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Überprüfen Sie die Dokumentation Ihres Konfigurations Systems, um zu bestimmen, wie Sie die Werte für die in der Struktur eingested Wenn z. b. Umgebungsvariablen verwendet werden `_` , werden anstelle von zwei Zeichen `:` verwendet (z. `Logging__LogLevel__Microsoft.AspNetCore.SignalR`b.).

Es wird empfohlen, `Debug` die Ebene zu verwenden, wenn Sie eine detailliertere Diagnose für Ihre APP sammeln. Die `Trace` Ebene erzeugt eine sehr geringe Diagnose und wird nur selten zum Diagnostizieren von Problemen in Ihrer APP benötigt.

## <a name="access-server-side-logs"></a>Zugreifen auf serverseitige Protokolle

Wie Sie auf serverseitige Protokolle zugreifen, hängt von der Umgebung ab, in der Sie ausgeführt werden.

### <a name="as-a-console-app-outside-iis"></a>Als Konsolen-App außerhalb von IIS

Wenn Sie in einer Konsolen-app ausgeführt werden, sollte die [Konsolen](xref:fundamentals/logging/index#console-provider) Protokollierung standardmäßig aktiviert werden. Signalr-Protokolle werden in der-Konsole angezeigt.

### <a name="within-iis-express-from-visual-studio"></a>In IIS Express aus Visual Studio

Visual Studio zeigt die Protokoll Ausgabe im **Ausgabe** Fenster an. Wählen Sie die Dropdown Option **ASP.net Core Webserver** aus.

### <a name="azure-app-service"></a>Azure App Service

Aktivieren Sie die Option **Anwendungs Protokollierung (File System)** im Abschnitt **Diagnoseprotokolle** des Azure App Service Portals, und **Level** konfigurieren Sie `Verbose`die Ebene auf. Protokolle sollten über den **protokollstreamingdienst** und in Protokollen im Dateisystem des App Service verfügbar sein. Weitere Informationen finden Sie unter [Azure-Protokoll Streaming](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Andere Umgebungen

Wenn die app in einer anderen Umgebung (z. b. Docker, Kubernetes oder Windows-Dienst) bereitgestellt <xref:fundamentals/logging/index> wird, finden Sie weitere Informationen zum Konfigurieren von Protokollierungs Anbietern, die für die Umgebung geeignet sind.

## <a name="javascript-client-logging"></a>JavaScript-Client Protokollierung

> [!WARNING]
> Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Wenn Sie den JavaScript-Client verwenden, können Sie die Protokollierungs `configureLogging` Optionen mithilfe `HubConnectionBuilder`der Methode für konfigurieren:

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an

In der folgenden Tabelle werden die für den JavaScript-Client verfügbaren Protokoll Ebenen angezeigt. Wenn Sie die Protokollebene auf einen dieser Werte festlegen, wird die Protokollierung auf dieser Ebene und allen darüber liegenden Ebenen in der Tabelle ermöglicht.

| Ebene | BESCHREIBUNG |
| ----- | ----------- |
| `None` | Es werden keine Nachrichten protokolliert. |
| `Critical` | Meldungen, die auf einen Fehler in der gesamten App hindeuten. |
| `Error` | Meldungen, die auf einen Fehler im aktuellen Vorgang hindeuten. |
| `Warning` | Meldungen, die auf ein nicht schwer wiedliches Problem hinweisen. |
| `Information` | Informationsmeldungen. |
| `Debug` | Diagnosemeldungen sind für das Debuggen nützlich. |
| `Trace` | Sehr ausführliche Diagnosemeldungen, die für die Diagnose bestimmter Probleme entwickelt wurden. |

Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in die Browser Konsole (oder die Standard Ausgabe in einer nodejs-APP) geschrieben.

Wenn Sie Protokolle an ein benutzerdefiniertes Protokollierungs System senden möchten, können Sie ein JavaScript-Objekt `ILogger` bereitstellen, das die-Schnittstelle implementiert. Die einzige Methode, die implementiert werden muss, `log`ist, die die Ebene des Ereignisses und die dem Ereignis zugeordnete Meldung annimmt. Beispiel:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET-Client Protokollierung

> [!WARNING]
> Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Um Protokolle vom .NET-Client zu erhalten, können Sie die `ConfigureLogging` -Methode `HubConnectionBuilder`für verwenden. Dies funktioniert genauso wie die `ConfigureLogging` -Methode in `WebHostBuilder` und. `HostBuilder` Sie können die gleichen Protokollierungs Anbieter konfigurieren, die Sie in ASP.net Core verwenden. Allerdings müssen Sie die nuget-Pakete für die einzelnen Protokollierungs Anbieter manuell installieren und aktivieren.

### <a name="console-logging"></a>Konsolenprotokollierung

Fügen Sie das Paket [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) hinzu, um die Konsolen Protokollierung zu aktivieren. Verwenden Sie dann die `AddConsole` -Methode zum Konfigurieren der Konsolen Protokollierung:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Debuggen des Ausgabe Fensters

Sie können Protokolle auch so konfigurieren, dass Sie zum Fenster **Ausgabe** in Visual Studio wechseln. Installieren Sie das Paket " [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) " `AddDebug` , und verwenden Sie die-Methode:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Andere Protokollierungs Anbieter

SignalRunterstützt andere Protokollierungs Anbieter wie serilog, seq, nlog oder ein beliebiges anderes Protokollierungs System `Microsoft.Extensions.Logging`, das in integriert ist. Wenn Ihr Protokollierungs System ein `ILoggerProvider`bereitstellt, können Sie es `AddProvider`mit folgenden Aktionen registrieren:

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Ausführlichkeit von Steuerelementen

Wenn Sie sich an anderen Stellen in ihrer App anmelden, ist das Ändern der Standard `Debug` Ebene in möglicherweise zu ausführlich. Sie können einen Filter verwenden, um den Protokolliergrad für SignalR Protokolle zu konfigurieren. Dies kann im Code auf die gleiche Weise wie auf dem Server erfolgen:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Netzwerkablaufverfolgung

> [!WARNING]
> Eine Netzwerk Ablauf Verfolgung enthält den gesamten Inhalt jeder Nachricht, die von Ihrer APP gesendet wird. Veröffentlichen Sie **niemals** unformatierte Netzwerk Ablauf Verfolgungen aus Produktions-apps in öffentlichen Foren wie GitHub.

Wenn ein Problem auftritt, kann es vorkommen, dass eine Netzwerk Ablauf Verfolgung viele hilfreiche Informationen liefert. Dies ist besonders nützlich, wenn Sie ein Problem in unserer Problemverfolgung melden.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Erfassen einer Netzwerk Ablauf Verfolgung mit "fddler" (bevorzugte Option)

Diese Methode funktioniert für alle apps.

"Fddler" ist ein sehr leistungsfähiges Tool zum Erfassen von http-Ablauf Verfolgungen. Installieren Sie Sie über [Telerik.com/Fiddler](https://www.telerik.com/fiddler), starten Sie Sie, und führen Sie dann die APP aus. "Fddler" ist für Windows verfügbar, und es gibt Beta Versionen für macOS und Linux.

Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzliche Schritte erforderlich, um sicherzustellen, dass der HTTPS-Datenverkehr von fddler entschlüsselt werden kann Weitere Informationen finden Sie in der [Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)zu "Dokumentation".

Nachdem Sie die Ablauf Verfolgung gesammelt haben, können Sie die Ablauf Verfolgung exportieren, indem Sie auf der Menüleiste auf **Datei** > **Save** > **alle Sitzungen** speichern klicken.

![Exportieren aller Sitzungen aus "fddler"](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Erfassen einer Netzwerk Ablauf Verfolgung mit tcpdump (nur macOS und Linux)

Diese Methode funktioniert für alle apps.

Sie können unformatierte TCP-Ablauf Verfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen. Wenn Sie einen Berechtigungs `root` Fehler erhalten, müssen Sie `sudo` möglicherweise den Befehl mit einem Präfix versehen.

```console
tcpdump -i [interface] -w trace.pcap
```

Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, die Sie erfassen möchten. In der Regel handelt es sich `/dev/eth0` hierbei um (für die standardethernet `/dev/lo0` -Schnittstelle) oder (für localhost-Datenverkehr). Weitere Informationen finden Sie auf der `tcpdump` Seite "man" auf Ihrem Host System.

## <a name="collect-a-network-trace-in-the-browser"></a>Erfassen einer Netzwerk Ablauf Verfolgung im Browser

Diese Methode funktioniert nur bei browserbasierten apps.

Die meisten Browser Entwicklertools über eine Registerkarte "Netzwerk" verfügen, mit der Sie die Netzwerkaktivität zwischen dem Browser und dem Server erfassen können. Diese Ablauf Verfolgungen enthalten jedoch keine WebSocket-und vom Server gesendeten Ereignis Nachrichten. Wenn Sie diese Transporte verwenden, ist die Verwendung eines Tools wie z. b. "fddler" oder "tcpdump" (unten beschrieben) ein besserer Ansatz.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge und Internet Explorer

(Die Anweisungen sind für Edge und Internet Explorer identisch.)

1. Drücken Sie F12, um die Entwickler Tools zu öffnen.
2. Klicken Sie auf die Registerkarte Netzwerk
3. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
4. Klicken Sie in der Symbolleiste auf das Symbol speichern, um die Ablauf Verfolgung als "har"-Datei zu exportieren:

![Symbol "Speichern" auf der Registerkarte "Netzwerk" von Microsoft Edge dev tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Drücken Sie F12, um die Entwickler Tools zu öffnen.
2. Klicken Sie auf die Registerkarte Netzwerk
3. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
4. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Anforderungsliste, und wählen Sie "Speichern unter har mit Inhalt"

![Option "Speichern unter har mit Inhalt" auf der Registerkarte "Google Chrome dev Tools Network"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Drücken Sie F12, um die Entwickler Tools zu öffnen.
2. Klicken Sie auf die Registerkarte Netzwerk
3. Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.
4. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie "alles als har

![Option "alle als har speichern" auf der Registerkarte "Mozilla Firefox dev Tools Network"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Anfügen von Diagnose Dateien an GitHub-Probleme

Sie können Diagnose Dateien an GitHub-Probleme anfügen, indem Sie Sie so `.txt` umbenennen, dass Sie über eine Erweiterung verfügen, und Sie dann auf das Problem ziehen und ablegen.

> [!NOTE]
> Fügen Sie den Inhalt von Protokolldateien oder Netzwerk Ablauf Verfolgungen nicht in ein GitHub-Problem ein. Diese Protokolle und Ablauf Verfolgungen können sehr umfangreich sein, und GitHub verkürzt Sie in der Regel.

![Ziehen von Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
