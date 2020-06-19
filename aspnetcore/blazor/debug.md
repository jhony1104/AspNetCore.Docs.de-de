---
title: Debuggen der ASP.NET Core Blazor-Assembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84239205"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Debuggen der ASP.NET Core Blazor-Assembly

[Daniel Roth](https://github.com/danroth27)

Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden. Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.

Verfügbare Szenarien:

* Festlegen und Entfernen von Breakpoints.
* Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.
* Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.
* Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.
* Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.
* Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.

Folgendes ist derzeit *nicht möglich*:

* Halt bei Ausnahmefehlern
* Erreichen von Breakpoints beim App-Start

Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.

## <a name="prerequisites"></a>Voraussetzungen

Zum Debuggen ist einer der folgenden Browser erforderlich:

* Microsoft Edge (Version 80 oder höher)
* Google Chrome (Version 70 oder höher)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Aktivieren des Debuggens für Visual Studio und Visual Studio Code

Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei *launchSettings.json* im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Anschließend sollte die Datei *launchSettings.json* dem folgenden Beispiel ähneln:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Die `inspectUri`-Eigenschaft:

* ermöglicht es der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.
* weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.

Die Platzhalterwerte für das WebSocket-Protokoll (`wsProtocol`), den Host (`url.hostname`), den Port (`url.port`) und den Inspektor-URI im gestarteten Browser (`browserInspectUri`) werden vom Framework bereitgestellt.

## <a name="visual-studio"></a>Visual Studio

So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:

1. Erstellen Sie eine neue in ASP.NET Core gehostete Blazor WebAssembly-App.
1. Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.
1. Legen Sie einen Breakpoint in der `IncrementCount`-Methode von *Counter.razor* fest.
1. Rufen Sie die Registerkarte **Counter** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.

Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:

1. Legen Sie einen Breakpoint auf der Seite *FetchData.razor* in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.
1. Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.
1. Rufen Sie die Registerkarte **FetchData** auf, um den ersten Breakpoint in der `FetchData`-Komponente zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code:
 
Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.

![Erweiterungen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS-Vorschaudebugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Debuggen der eigenständigen Blazor WebAssembly

1. Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.

   Wenn Sie die folgende Benachrichtigung erhalten, bedeutet das, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:
   
   * Vergewissern Sie sich, dass die richtige Erweiterung installiert ist.
   * Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist.
   * Laden Sie das Fenster neu.

   ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.

1. Wenn Sie dazu aufgefordert werden, wählen Sie die **Blazor WebAssembly debuggen**, um das Debugging zu starten.

   ![Liste der verfügbaren Debugoptionen](index/_static/blazor-vscode-debugtypes.png)

1. Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.

1. Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Debuggen einer gehosteten Blazor WebAssembly

1. Öffnen Sie die gehostete Blazor WebAssembly-App in VS Code.

1. Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt. Wählen Sie **Ja**.

   ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Wählen Sie im Auswahlfenster das Projekt *Server* in der gehosteten Lösung.

Zum Starten des Debuggers wird eine *launch.json*-Datei mit der Startkonfiguration generiert.

### <a name="attach-to-an-existing-debugging-session"></a>Anfügen an eine vorhandene Debugsitzung

Um an eine laufende Blazor-App anzufügen, erstellen Sie eine *launch.json*-Datei mit der folgenden Konfiguration:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Das Anfügen an eine Debugsitzung wird nur für eigenständige Apps unterstützt. Um das Full-Stack-Debuggen zu verwenden, muss die App über VS Code gestartet werden.

### <a name="launch-configuration-options"></a>Optionen für die Startkonfiguration

Für den Debugtyp `blazorwasm` werden die folgenden Optionen für die Startkonfiguration unterstützt.

| Option    | Beschreibung |
| --------- | ----------- |
| `request` | Verwenden Sie `launch`, um eine Debugsitzung zu starten und an eine Blazor WebAssembly-App anzufügen oder `attach`, um eine Debugsitzung an eine bereits laufende App anzufügen. |
| `url`     | Die URL, die beim Debuggen im Browser geöffnet werden soll. Wird standardmäßig auf `https://localhost:5001` festgelegt. |
| `browser` | Der Browser, der für die Debugsitzung gestartet werden muss. Wird auf `edge` oder `chrome` festgelegt. Wird standardmäßig auf `chrome` festgelegt. |
| `trace`   | Wird zum Generieren von Protokollen vom JS-Debugger verwendet. Zum Generieren von Protokollen auf `true` festlegen. |
| `hosted`  | Muss auf `true` gesetzt werden, wenn eine gehostete Blazor WebAssembly-App gestartet und debuggt werden soll. |
| `webRoot` | Gibt den absoluten Pfad des Webservers an. Muss gesetzt werden, wenn eine App von einer Unterroute bereitgestellt wird. |
| `timeout` | Die Anzahl der Millisekunden, die auf das Anfügen der Debugsitzung gewartet werden soll. Wird standardmäßig auf 30.000 Millisekunden (30 Sekunden) festgelegt. |
| `program` | Ein Verweis auf die ausführbare Datei zur Ausführung des Servers der gehosteten App. Muss festgelegt werden, wenn `hosted` den Wert `true` hat. |
| `cwd`     | Das Arbeitsverzeichnis, unter dem die App gestartet werden soll. Muss festgelegt werden, wenn `hosted` den Wert `true` hat. |
| `env`     | Die Umgebungsvariable, die für den gestarteten Prozess bereitgestellt werden muss. Trifft nur zu, wenn `hosted` auf `true` festgelegt ist. |

### <a name="example-launch-configurations"></a>Beispiel für Startkonfigurationen

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Starten und Debuggen einer eigenständigen Blazor WebAssembly-App

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anfügen an eine laufende App unter einer angegebenen URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Starten und Debuggen einer gehosteten Blazor WebAssembly-App

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Debuggen im Browser

1. Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.

1. Drücken Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden. Wenn das Remotedebuggen deaktiviert ist, wird die Fehlerseite mit der Aussage generiert, dass **keine debugfähige Browserregisterkarte gefunden werden konnte**. Die Fehlerseite enthält Anweisungen zum Ausführen des Browsers bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann. *Schließen Sie alle Browserinstanzen*, und starten Sie den Browser gemäß den Anweisungen neu.

Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnen die Tastenkombination zum Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an. Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen. Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen. Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).

Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert. Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).

## <a name="browser-source-maps"></a>Browserquellzuordnungen

Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet. Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu. Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:

* Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.
* Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen. Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
