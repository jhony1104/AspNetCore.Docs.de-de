---
title: 'Debuggen der Blazor-WebAssembly' author: description: 'Erfahren Sie, wie Sie Blazor-Apps debuggen.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Debuggen der ASP.NET Core Blazor-Assembly

[Daniel Roth](https://github.com/danroth27)

Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden.  Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.

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
 
1. Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.

   ![Erweiterungen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS-Vorschaudebugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Öffnen Sie eine vorhandene Blazor WebAssembly-App mit aktiviertem Debuggen.

   * Wenn Sie die folgende Benachrichtigung erhalten, dass zusätzliches Setup zum Aktivieren des Debuggens erforderlich ist, stellen Sie sicher, dass Sie die richtigen Erweiterungen installiert und den JavaScript-Vorschaudebugger aktiviert haben. Laden Sie das Fenster dann neu:

     ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Eine Benachrichtigung bietet an, die erforderlichen Ressourcen zum Erstellen und Debuggen zur Apps hinzuzufügen. Wählen Sie **Ja** aus:

     ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Das Starten der App im Debugger ist ein zweistufiger Prozess:

   1\. Starten Sie die App **zuerst** mit der Startkonfiguration **.NET Core Launch (Blazor Standalone)** .

   2\. **Nachdem die App gestartet wurde**, starten Sie den Browser mit der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (erfordert Google Chrome). Ändern Sie den `type`-Wert der Startkonfiguration in *.vscode/launch.json* von `pwa-chrome` in `pwa-msedge`, um Microsoft Edge anstelle von Google Chrome zu verwenden.

1. Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Debuggen im Browser

1. Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.

1. Drücken Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden. Wenn das Remotedebuggen deaktiviert ist, wird die Fehlerseite mit der Aussage generiert, dass **keine debugfähige Browserregisterkarte gefunden werden konnte**. Die Fehlerseite enthält Anweisungen zum Ausführen des Browsers bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann. *Schließen Sie alle Browserinstanzen*, und starten Sie den Browser gemäß den Anweisungen neu.

Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnen die Tastenkombination zum Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an. Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen. Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen. Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).

Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert. Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).

## <a name="browser-source-maps"></a>Browserquellzuordnungen

Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet. Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu. Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Sie auf Fehler stoßen, könnte der folgende Tipp helfen:

Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.
