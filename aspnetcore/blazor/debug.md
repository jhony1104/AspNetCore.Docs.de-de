---
<span data-ttu-id="58085-101">title: 'Debuggen der Blazor-WebAssembly' author: description: 'Erfahren Sie, wie Sie Blazor-Apps debuggen.'</span><span class="sxs-lookup"><span data-stu-id="58085-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="58085-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58085-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58085-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58085-103">'Blazor'</span></span>
- <span data-ttu-id="58085-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58085-104">'Identity'</span></span>
- <span data-ttu-id="58085-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58085-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="58085-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58085-106">'Razor'</span></span>
- <span data-ttu-id="58085-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="58085-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="58085-108">Debuggen der ASP.NET Core Blazor-Assembly</span><span class="sxs-lookup"><span data-stu-id="58085-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="58085-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="58085-109">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="58085-110">Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="58085-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="58085-111">Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.</span><span class="sxs-lookup"><span data-stu-id="58085-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="58085-112">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="58085-112">Available scenarios include:</span></span>

* <span data-ttu-id="58085-113">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="58085-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="58085-114">Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.</span><span class="sxs-lookup"><span data-stu-id="58085-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="58085-115">Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.</span><span class="sxs-lookup"><span data-stu-id="58085-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="58085-116">Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.</span><span class="sxs-lookup"><span data-stu-id="58085-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="58085-117">Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.</span><span class="sxs-lookup"><span data-stu-id="58085-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="58085-118">Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.</span><span class="sxs-lookup"><span data-stu-id="58085-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="58085-119">Folgendes ist derzeit *nicht möglich*:</span><span class="sxs-lookup"><span data-stu-id="58085-119">For now, you *can't*:</span></span>

* <span data-ttu-id="58085-120">Untersuchen von Arrays</span><span class="sxs-lookup"><span data-stu-id="58085-120">Inspect arrays.</span></span>
* <span data-ttu-id="58085-121">Zeigen auf Member zum Untersuchen</span><span class="sxs-lookup"><span data-stu-id="58085-121">Hover to inspect members.</span></span>
* <span data-ttu-id="58085-122">Schrittweises Debuggen in oder aus verwaltetem Code</span><span class="sxs-lookup"><span data-stu-id="58085-122">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="58085-123">Vollständige Unterstützung für die Untersuchung von Werttypen</span><span class="sxs-lookup"><span data-stu-id="58085-123">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="58085-124">Halt bei Ausnahmefehlern</span><span class="sxs-lookup"><span data-stu-id="58085-124">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="58085-125">Erreichen von Breakpoints beim App-Start</span><span class="sxs-lookup"><span data-stu-id="58085-125">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="58085-126">Debuggen einer App mit Service Worker</span><span class="sxs-lookup"><span data-stu-id="58085-126">Debug an app with a service worker.</span></span>

<span data-ttu-id="58085-127">Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.</span><span class="sxs-lookup"><span data-stu-id="58085-127">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="58085-128">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="58085-128">Prerequisites</span></span>

<span data-ttu-id="58085-129">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="58085-129">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="58085-130">Microsoft Edge (Version 80 oder höher)</span><span class="sxs-lookup"><span data-stu-id="58085-130">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="58085-131">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="58085-131">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="58085-132">Aktivieren des Debuggens für Visual Studio und Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58085-132">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="58085-133">Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei *launchSettings.json* im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:</span><span class="sxs-lookup"><span data-stu-id="58085-133">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="58085-134">Anschließend sollte die Datei *launchSettings.json* dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="58085-134">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="58085-135">Die `inspectUri`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="58085-135">The `inspectUri` property:</span></span>

* <span data-ttu-id="58085-136">ermöglicht es der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.</span><span class="sxs-lookup"><span data-stu-id="58085-136">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="58085-137">weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.</span><span class="sxs-lookup"><span data-stu-id="58085-137">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="58085-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58085-138">Visual Studio</span></span>

<span data-ttu-id="58085-139">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="58085-139">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="58085-140">Erstellen Sie eine neue in ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="58085-140">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="58085-141">Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="58085-141">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="58085-142">Legen Sie einen Breakpoint in der `IncrementCount`-Methode von *Counter.razor* fest.</span><span class="sxs-lookup"><span data-stu-id="58085-142">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="58085-143">Rufen Sie die Registerkarte **Counter** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:</span><span class="sxs-lookup"><span data-stu-id="58085-143">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="58085-145">Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:</span><span class="sxs-lookup"><span data-stu-id="58085-145">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="58085-147">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="58085-147">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="58085-148">Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="58085-148">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="58085-149">Legen Sie einen Breakpoint auf der Seite *FetchData.razor* in `OnInitializedAsync` fest.</span><span class="sxs-lookup"><span data-stu-id="58085-149">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="58085-150">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="58085-150">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="58085-151">Rufen Sie die Registerkarte **FetchData** auf, um den ersten Breakpoint in der `FetchData`-Komponente zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:</span><span class="sxs-lookup"><span data-stu-id="58085-151">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="58085-153">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="58085-153">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="58085-155">Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="58085-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="58085-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58085-156">Visual Studio Code</span></span>

<span data-ttu-id="58085-157">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="58085-157">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="58085-158">Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="58085-158">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Erweiterungen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS-Vorschaudebugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="58085-161">Öffnen Sie eine vorhandene Blazor WebAssembly-App mit aktiviertem Debuggen.</span><span class="sxs-lookup"><span data-stu-id="58085-161">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="58085-162">Wenn Sie die folgende Benachrichtigung erhalten, dass zusätzliches Setup zum Aktivieren des Debuggens erforderlich ist, stellen Sie sicher, dass Sie die richtigen Erweiterungen installiert und den JavaScript-Vorschaudebugger aktiviert haben. Laden Sie das Fenster dann neu:</span><span class="sxs-lookup"><span data-stu-id="58085-162">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="58085-164">Eine Benachrichtigung bietet an, die erforderlichen Ressourcen zum Erstellen und Debuggen zur Apps hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="58085-164">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="58085-165">Wählen Sie **Ja** aus:</span><span class="sxs-lookup"><span data-stu-id="58085-165">Select **Yes**:</span></span>

     ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="58085-167">Das Starten der App im Debugger ist ein zweistufiger Prozess:</span><span class="sxs-lookup"><span data-stu-id="58085-167">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="58085-168">1\.</span><span class="sxs-lookup"><span data-stu-id="58085-168">1\.</span></span> <span data-ttu-id="58085-169">Starten Sie die App **zuerst** mit der Startkonfiguration **.NET Core Launch (Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="58085-169">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="58085-170">2\.</span><span class="sxs-lookup"><span data-stu-id="58085-170">2\.</span></span> <span data-ttu-id="58085-171">**Nachdem die App gestartet wurde**, starten Sie den Browser mit der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (erfordert Google Chrome).</span><span class="sxs-lookup"><span data-stu-id="58085-171">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="58085-172">Ändern Sie den `type`-Wert der Startkonfiguration in *.vscode/launch.json* von `pwa-chrome` in `pwa-msedge`, um Microsoft Edge anstelle von Google Chrome zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="58085-172">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="58085-173">Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="58085-173">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="58085-175">Debuggen im Browser</span><span class="sxs-lookup"><span data-stu-id="58085-175">Debug in the browser</span></span>

1. <span data-ttu-id="58085-176">Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.</span><span class="sxs-lookup"><span data-stu-id="58085-176">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="58085-177">Drücken Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="58085-177">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="58085-178">Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="58085-178">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="58085-179">Wenn das Remotedebuggen deaktiviert ist, wird die Fehlerseite mit der Aussage generiert, dass **keine debugfähige Browserregisterkarte gefunden werden konnte**.</span><span class="sxs-lookup"><span data-stu-id="58085-179">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="58085-180">Die Fehlerseite enthält Anweisungen zum Ausführen des Browsers bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="58085-180">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="58085-181">*Schließen Sie alle Browserinstanzen*, und starten Sie den Browser gemäß den Anweisungen neu.</span><span class="sxs-lookup"><span data-stu-id="58085-181">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="58085-182">Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnen die Tastenkombination zum Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an.</span><span class="sxs-lookup"><span data-stu-id="58085-182">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="58085-183">Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="58085-183">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="58085-184">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen.</span><span class="sxs-lookup"><span data-stu-id="58085-184">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="58085-185">Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="58085-185">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="58085-186"> bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="58085-186"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="58085-187">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="58085-187">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="58085-188">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="58085-188">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="58085-189">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="58085-189">Browser source maps</span></span>

<span data-ttu-id="58085-190">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="58085-190">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="58085-191">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="58085-191">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="58085-192">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="58085-192">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="58085-193">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="58085-193">Troubleshoot</span></span>

<span data-ttu-id="58085-194">Wenn Sie auf Fehler stoßen, könnte der folgende Tipp helfen:</span><span class="sxs-lookup"><span data-stu-id="58085-194">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="58085-195">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="58085-195">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="58085-196">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="58085-196">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
