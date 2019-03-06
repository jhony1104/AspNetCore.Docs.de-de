---
title: Protokollierung und-Diagnose in ASP.NET Core SignalR
author: anurse
description: Informationen Sie zum Sammeln von Diagnosedaten aus Ihrer ASP.NET Core SignalR-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400941"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Protokollierung und-Diagnose in ASP.NET Core SignalR

Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Anleitungen für das Sammeln von Diagnosen aus Ihrer ASP.NET Core SignalR-app zum Behandeln von Problemen.

## <a name="server-side-logging"></a>Serverseitige Protokollierung

> [!WARNING]
> Serverseitiger Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app. **Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.

Da SignalR Teil von ASP.NET Core ist, wird die ASP.NET Core-Protokollierung System verwendet. Klicken Sie in der Standardkonfiguration SignalR nur sehr wenig Informationen protokolliert, jedoch kann dies konfiguriert. Finden Sie in der Dokumentation auf [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) Weitere Informationen zum Konfigurieren von ASP.NET Core-Protokollierung.

SignalR verwendet zwei Protokollierungskategorien:

* `Microsoft.AspNetCore.SignalR` -Aktivieren Sie für die Protokolle im Zusammenhang mit der Hub-Protokolle, Hubs, das Aufrufen von Methoden und anderen Aktivitäten im Zusammenhang mit dem Hub.
* `Microsoft.AspNetCore.Http.Connections` – für Protokolle im Zusammenhang mit Transporten, z. B. WebSockets, Long Polling und Server-Sent-Ereignisse und Low-Level-SignalR-Infrastruktur.

Detaillierte Protokolle von SignalR konfigurieren, um sowohl die vorherige Präfixe in der `Debug` Ebene in Ihrer `appsettings.json` -Datei, indem Sie die folgenden Elemente zum Hinzufügen der `LogLevel` im Unterabschnitt `Logging`:

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

Sie können dies auch konfigurieren, im Code in Ihre `CreateWebHostBuilder` Methode:

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

Wenn Sie JSON-basierte Konfiguration nicht verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Überprüfen Sie die Dokumentation für Ihr Konfigurationssystem, um zu bestimmen, wie Sie geschachtelte Konfigurationswerte angeben. Beispielsweise bei Verwendung von Umgebungsvariablen, zwei `_` Zeichen werden verwendet, statt die `:` (z. B.: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

Es wird empfohlen, die `Debug` Ebene beim Erfassen, ausführlichere Diagnose für Ihre app. Die `Trace` erzeugt sehr Low-Level-Diagnose und nur selten erforderlich, um die diagnose von Problemen in Ihrer app.

## <a name="access-server-side-logs"></a>Serverseitige Zugriffsprotokolle

Wie Sie serverseitige Protokolle zugreifen, hängt von der Umgebung, in der Sie ausgeführt werden.

### <a name="as-a-console-app-outside-iis"></a>Als Konsolen-app außerhalb von IIS

Wenn Sie in einer Konsolen-app ausführen, die [konsolenprotokollierung](xref:fundamentals/logging/index#console-provider) sollte standardmäßig aktiviert. SignalR-Protokolle werden in der Konsole angezeigt.

### <a name="within-iis-express-from-visual-studio"></a>Innerhalb von IIS Express aus Visual Studio

Visual Studio zeigt die Ausgabe des Protokolls in der **Ausgabe** Fenster. Wählen Sie die **ASP.NET Core-Web-Server** Dropdown-Option.

### <a name="azure-app-service"></a>Azure App Service

Aktivieren Sie die Option "Anwendungsprotokollierung (Dateisystem)" im Abschnitt "Diagnoseprotokolle" im Azure App Service-Portal und konfigurieren Sie die anzuzeigende Stufe `Verbose`. Protokolle sollten von der "Log"-streamingdienst, auch wie Protokolle im Dateisystem Ihres App Service verfügbar sein. Weitere Informationen finden Sie in der Dokumentation auf [Azure-protokollstreaming](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Andere Umgebungen

Wenn Sie in einer anderen ausführen Umgebung (Docker, Kubernetes, Windows-Dienst usw.), finden Sie die vollständige Dokumentation für [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index) für Weitere Informationen zum Konfigurieren der Anbieter für die Protokollierung für Ihre Umgebung geeignet ist.

## <a name="javascript-client-logging"></a>JavaScript-Clients, die Protokollierung

> [!WARNING]
> Die clientseitige Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app. **Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.

Wenn Sie den JavaScript-Client verwenden, können Sie Konfigurieren der Protokollierungsoptionen mithilfe der `configureLogging` Methode `HubConnectionBuilder`:

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

Geben Sie zum Deaktivieren der Protokollierung vollständig, `signalR.LogLevel.None` in die `configureLogging` Methode.

Die folgende Tabelle zeigt die verfügbaren Protokollebenen an den JavaScript-Client. Die Protokollebene auf einen der folgenden Werte festlegen kann die Protokollierung auf dieser Ebene und alle Ebenen darüber, in der Tabelle.

| Ebene | Beschreibung |
| ----- | ----------- |
| `None` | Es werden keine Meldungen protokolliert. |
| `Critical` | Nachrichten, die einen Fehler in der gesamten app angeben. |
| `Error` | Nachrichten, die einen Fehler in den aktuellen Vorgang angeben. |
| `Warning` | Nachrichten, die ein nicht schwerwiegender Fehler aufgetreten. |
| `Information` | Informationsmeldungen. |
| `Debug` | Diagnosemeldungen für das Debuggen hilfreich. |
| `Trace` | Sehr detaillierte diagnosemeldungen zum Diagnostizieren von Problemen mit bestimmten entwickelt. |

Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in der Browserkonsole (oder die Standardausgabe in eine NodeJS-app) geschrieben werden.

Bei der Sie Protokolle an ein System für die benutzerdefinierte Protokollierung senden möchten, können Sie angeben, ein JavaScript-Objekt implementieren die `ILogger` Schnittstelle. Ist die einzige Methode, die implementiert werden muss `log`, der die Ebene des Ereignisses akzeptiert und die Nachricht, die dem Ereignis zugeordnet. Zum Beispiel:

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET Client-Protokollierung

> [!WARNING]
> Die clientseitige Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app. **Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.

Um Protokolle aus dem .NET-Client zu erhalten, können Sie die `ConfigureLogging` Methode `HubConnectionBuilder`. Dies funktioniert genauso wie die `ConfigureLogging` Methode `WebHostBuilder` und `HostBuilder`. Sie können dieselben Protokollierungsanbieter, die Sie verwenden, konfigurieren, in ASP.NET Core. Allerdings müssen Sie manuell installieren und aktivieren die NuGet-Pakete für die einzelnen Protokollierungsanbieter.

### <a name="console-logging"></a>Konsolenprotokollierung

Um die konsolenprotokollierung zu aktivieren, fügen Sie der [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Paket. Verwenden Sie dann die `AddConsole` Methode zum Konfigurieren der konsolenprotokollierung:

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Debugprotokollierung bei der Ausgabe-Fenster

Sie können auch Protokolle wechseln Sie zum Konfigurieren der **Ausgabe** Fenster in Visual Studio. Installieren Sie die [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) packen sowie die Verwendung der `AddDebug` Methode:

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Anderer Protokollierungsanbieter

SignalR unterstützt anderer Protokollierungsanbieter wie z. B. Serilog, Seq, NLog oder ein anderes Protokollierungssystem, die integriert `Microsoft.Extensions.Logging`. Wenn Ihr Protokollierungssystem bietet eine `ILoggerProvider`, registrieren Sie ihn mit `AddProvider`:

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Steuerelement-Ausführlichkeitsgrads

Wenn Sie an anderen Stellen in Ihrer app anmelden, ändern Sie die Standardebene, `Debug` möglicherweise zu ausführlich. Sie können einen Filter verwenden, so konfigurieren Sie den Protokolliergrad für SignalR-Protokolle. Dies kann im Code im Wesentlichen die gleiche Weise wie auf dem Server durchgeführt werden:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Netzwerkablaufverfolgung

> [!WARNING]
> Eine netzwerkablaufverfolgung enthält den vollständigen Inhalt jeder Nachricht, die von Ihrer app gesendet. **Nie** Netzwerk-ablaufverfolgungen von Produktions-apps in öffentlichen Foren wie GitHub Posten.

Wenn Sie ein Problem auftritt, bieten eine netzwerkablaufverfolgung manchmal sehr hilfreiche Informationen. Dies ist besonders nützlich, wenn Sie zum Melden eines Problems in unserer problemverfolgung also.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Erfassen einer netzwerkablaufverfolgung mit Fiddler (bevorzugte Option)

Diese Methode funktioniert für alle apps.

Fiddler ist ein äußerst leistungsfähiges Tool zum Sammeln von HTTP-ablaufverfolgungen. Installieren Sie sie über [telerik.com/fiddler](https://www.telerik.com/fiddler), starten Sie ihn, und klicken Sie dann die Anwendung ausführen und reproduzieren Sie das Problem. Fiddler ist verfügbar für Windows, und es gibt Betaversionen für MacOS und Linux.

Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzlichen Schritte ausführen, um sicherzustellen, dass den HTTPS-Datenverkehr von Fiddler entschlüsselt werden kann. Weitere Informationen finden Sie unter den [Fiddler-Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Nachdem Sie die Ablaufverfolgung gesammelt haben, können Sie die Ablaufverfolgung exportieren, indem auswählen **Datei** > **speichern** > **alle Sitzungen...**  in der Menüleiste.

![Exportieren alle Sitzungen von Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Erfassen einer netzwerkablaufverfolgung mit Tcpdump (Mac OS und nur Linux)

Diese Methode funktioniert für alle apps.

Sie können die unformatierte TCP-ablaufverfolgungen, die mit Tcpdump mithilfe des folgenden Befehls über eine Befehlsshell sammeln. Möglicherweise werden `root` oder den Befehl mit Präfix `sudo` Wenn Sie einen Berechtigungsfehler erhalten:

```console
tcpdump -i [interface] -w trace.pcap
```

Ersetzen Sie dies `[interface]` der Netzwerkschnittstelle, die Sie auf erfassen möchten. In der Regel ist dies so etwas wie `/dev/eth0` (für Ihre standard-Ethernet-Schnittstelle) oder `/dev/lo0` (für Datenverkehr von "localhost"). Weitere Informationen finden Sie unter den `tcpdump` Manpage auf Ihrem Hostsystem.

## <a name="collect-a-network-trace-in-the-browser"></a>Erfassen einer netzwerkablaufverfolgung im browser

Diese Methode funktioniert nur für browserbasierte apps.

Die meisten Browserentwicklertools haben eine "Netzwerk"-Registerkarte, die Ihnen ermöglicht, die Netzwerkaktivität zwischen dem Browser und dem Server zu erfassen. Diese ablaufverfolgungen enthalten jedoch keine WebSocket und Server-Sent ereignismeldungen. Wenn Sie diese Transporte verwenden, ist die mit einem Tool wie Fiddler oder mit TcpDump (siehe unten) ein besserer Ansatz.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge und InternetExplorer

(Die Anweisungen sind identisch für sowohl Microsoft Edge und Internet Explorer)

1. Drücken Sie F12, um den Entwicklertools zu öffnen.
2. Klicken Sie auf der Registerkarte "Netzwerk"
3. Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem
4. Klicken Sie auf das Symbol "Speichern" in der Symbolleiste, um die Ablaufverfolgung als "HAR"-Datei zu exportieren:

![Der Speichervorgang Symbol auf der Microsoft Edge-Entwickler-Tools Registerkarte "Netzwerk"](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Drücken Sie F12, um den Entwicklertools zu öffnen.
2. Klicken Sie auf der Registerkarte "Netzwerk"
3. Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem
4. Rechtsklick an beliebiger Stelle in der Liste der Anforderungen, und wählen Sie "Als HAR mit Inhalte speichern":

![Option "Als HAR mit Inhalte speichern", in Google Chrome Dev Tools Registerkarte "Netzwerk"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Drücken Sie F12, um den Entwicklertools zu öffnen.
2. Klicken Sie auf der Registerkarte "Netzwerk"
3. Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem
4. Rechtsklick an beliebiger Stelle in der Liste der Anforderungen, und wählen Sie "Speichern Sie alle als HAR"

!["Speichern Sie alle als HAR"-Option in Mozilla Firefox Dev Tools Registerkarte "Netzwerk"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Fügen Sie die Diagnosedateien zu GitHub-Problemen

Sie können die Diagnose-Dateien zu GitHub-Problemen anfügen, indem Sie Sie umbenennen, müssen sie sich eine `.txt` -Erweiterung, und klicken Sie dann ziehen und Ablegen an das Problem.

> [!NOTE]
> Fügen Sie keine den Inhalt der Protokolldateien oder netzwerkablaufverfolgungen im GitHub-Problem. Diese Protokolle und ablaufverfolgungen können sehr groß sein und GitHub wird in der Regel werden abgeschnitten.

![Ziehen die Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
