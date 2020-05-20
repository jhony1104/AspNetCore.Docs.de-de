---
title: Protokollierung und Diagnose in ASP.net CoreSignalR
author: anurse
description: Erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen.
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
ms.openlocfilehash: 0dda4fb55b1e2275d9cdb2af0b55824b12121dee
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444216"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Protokollierung und Diagnose in ASP.net Core signalr

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus Ihrer ASP.net Core signalr-APP, um Probleme zu beheben.

## <a name="server-side-logging"></a>Server seitige Protokollierung

> [!WARNING]
> Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App. Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.

Da signalr Teil ASP.net Core ist, verwendet es das ASP.net Core Protokollierungs System. In der Standardkonfiguration protokolliert signalr nur wenige Informationen, aber dies kann konfiguriert werden. In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.

Signalr verwendet zwei Kategorien von Kategorien:

* `Microsoft.AspNetCore.SignalR`&ndash;Protokolle im Zusammenhang mit hubprotokollen, das Aktivieren von Hubs, das Aufrufen von Methoden und andere hubbezogene Aktivitäten.
* `Microsoft.AspNetCore.Http.Connections`&ndash;Protokolle, die sich auf Transporte beziehen, wie z. b. websockets, lange Abruf Ereignisse und Server gesendete Ereignisse und eine signalr-Infrastruktur auf niedriger Ebene.

Um ausführliche Protokolle von signalr zu aktivieren, konfigurieren Sie beide vorangehenden Präfixe auf der `Debug` Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente zum `LogLevel` unter Abschnitt in hinzufügen `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Sie können dies auch in Code in der- `CreateWebHostBuilder` Methode konfigurieren:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem fest:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden. Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen. Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.

## <a name="access-server-side-logs"></a>Zugreifen auf serverseitige Protokolle

Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.

### <a name="as-a-console-app-outside-iis"></a>Als Konsolen-App außerhalb von IIS

Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein. Signalr-Protokolle werden in der-Konsole angezeigt.

### <a name="within-iis-express-from-visual-studio"></a>In IIS Express aus Visual Studio

Visual Studio zeigt die Protokoll Ausgabe im **Ausgabe** Fenster an. Wählen Sie die Dropdown Option **ASP.net Core Webserver** aus.

### <a name="azure-app-service"></a>Azure App Service

Aktivieren Sie die Option **Anwendungs Protokollierung (File System)** im Abschnitt **Diagnoseprotokolle** des Azure App Service Portals, und konfigurieren Sie die **Ebene** auf `Verbose` . Protokolle sollten über den **protokollstreamingdienst** und in Protokollen im Dateisystem des App Service verfügbar sein. Weitere Informationen finden Sie unter [Azure-Protokoll Streaming](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Andere Umgebungen

Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.

## <a name="javascript-client-logging"></a>JavaScript-Client Protokollierung

> [!WARNING]
> Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App. Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.

Wenn Sie den JavaScript-Client verwenden, können Sie die Protokollierungs Optionen mithilfe der `configureLogging` Methode für konfigurieren `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an

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

Wenn Sie Protokolle an ein benutzerdefiniertes Protokollierungs System senden möchten, können Sie ein JavaScript-Objekt bereitstellen, das die- `ILogger` Schnittstelle implementiert. Die einzige Methode, die implementiert werden muss `log` , ist, die die Ebene des Ereignisses und die dem Ereignis zugeordnete Meldung annimmt. Beispiel:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET-Client Protokollierung

> [!WARNING]
> Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App. Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.

Um Protokolle vom .NET-Client zu erhalten, können Sie die- `ConfigureLogging` Methode für verwenden `HubConnectionBuilder` . Dies funktioniert genauso wie die `ConfigureLogging` -Methode in `WebHostBuilder` und `HostBuilder` . Sie können die gleichen Protokollierungs Anbieter konfigurieren, die Sie in ASP.net Core verwenden. Allerdings müssen Sie die nuget-Pakete für die einzelnen Protokollierungs Anbieter manuell installieren und aktivieren.

### <a name="console-logging"></a>Konsolenprotokollierung

Fügen Sie das Paket [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) hinzu, um die Konsolen Protokollierung zu aktivieren. Verwenden Sie dann die- `AddConsole` Methode zum Konfigurieren der Konsolen Protokollierung:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Debuggen des Ausgabe Fensters

Sie können Protokolle auch so konfigurieren, dass Sie zum Fenster **Ausgabe** in Visual Studio wechseln. Installieren Sie das Paket " [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) ", und verwenden Sie die- `AddDebug` Methode:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Andere Protokollierungs Anbieter

SignalRunterstützt andere Protokollierungs Anbieter wie serilog, seq, nlog oder ein beliebiges anderes Protokollierungs System, das in integriert ist `Microsoft.Extensions.Logging` . Wenn Ihr Protokollierungs System ein bereitstellt `ILoggerProvider` , können Sie es mit folgenden Aktionen registrieren `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Ausführlichkeit von Steuerelementen

Wenn Sie sich an anderen Stellen in ihrer App anmelden, ist das Ändern der Standard Ebene in `Debug` möglicherweise zu ausführlich. Sie können einen Filter verwenden, um den Protokolliergrad für Protokolle zu konfigurieren SignalR . Dies kann im Code auf die gleiche Weise wie auf dem Server erfolgen:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Netzwerkablaufverfolgung

> [!WARNING]
> Eine Netzwerk Ablauf Verfolgung enthält den gesamten Inhalt jeder Nachricht, die von Ihrer APP gesendet wird. Veröffentlichen Sie **niemals** unformatierte Netzwerk Ablauf Verfolgungen aus Produktions-apps in öffentlichen Foren wie GitHub.

Wenn ein Problem auftritt, kann es vorkommen, dass eine Netzwerk Ablauf Verfolgung viele hilfreiche Informationen liefert. Dies ist besonders nützlich, wenn Sie ein Problem in unserer Problemverfolgung melden.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Erfassen einer Netzwerk Ablauf Verfolgung mit "fddler" (bevorzugte Option)

Diese Methode funktioniert für alle apps.

"Fddler" ist ein sehr leistungsfähiges Tool zum Erfassen von http-Ablauf Verfolgungen. Installieren Sie Sie über [Telerik.com/Fiddler](https://www.telerik.com/fiddler), starten Sie Sie, und führen Sie dann die APP aus. "Fddler" ist für Windows verfügbar, und es gibt Beta Versionen für macOS und Linux.

Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzliche Schritte erforderlich, um sicherzustellen, dass der HTTPS-Datenverkehr von fddler entschlüsselt werden kann Weitere Informationen finden Sie in der [Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)zu "Dokumentation".

Nachdem Sie die Ablauf Verfolgung gesammelt haben, können Sie die Ablauf Verfolgung exportieren **File**, indem Sie auf  >  **Save**  >  der Menüleiste auf Datei**alle Sitzungen** speichern klicken.

![Exportieren aller Sitzungen aus "fddler"](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Erfassen einer Netzwerk Ablauf Verfolgung mit tcpdump (nur macOS und Linux)

Diese Methode funktioniert für alle apps.

Sie können unformatierte TCP-Ablauf Verfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen. `root` `sudo` Wenn Sie einen Berechtigungs Fehler erhalten, müssen Sie möglicherweise den Befehl mit einem Präfix versehen.

```console
tcpdump -i [interface] -w trace.pcap
```

Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, die Sie erfassen möchten. In der Regel handelt es sich hierbei um `/dev/eth0` (für die standardethernet-Schnittstelle) oder `/dev/lo0` (für localhost-Datenverkehr). Weitere Informationen finden `tcpdump` Sie auf der Seite "man" auf Ihrem Host System.

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

Sie können Diagnose Dateien an GitHub-Probleme anfügen, indem Sie Sie so umbenennen, dass Sie über eine Erweiterung verfügen, und Sie `.txt` dann auf das Problem ziehen und ablegen.

> [!NOTE]
> Fügen Sie den Inhalt von Protokolldateien oder Netzwerk Ablauf Verfolgungen nicht in ein GitHub-Problem ein. Diese Protokolle und Ablauf Verfolgungen können sehr umfangreich sein, und GitHub verkürzt Sie in der Regel.

![Ziehen von Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
