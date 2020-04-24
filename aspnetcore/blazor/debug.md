---
title: Debuggen der ASP.NET Core Blazor-Assembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661605"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="b28a6-103">Debuggen der ASP.NET Core Blazor-Assembly</span><span class="sxs-lookup"><span data-stu-id="b28a6-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b28a6-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b28a6-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b28a6-105">Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b28a6-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="b28a6-106">Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="b28a6-107">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="b28a6-107">Available scenarios include:</span></span>

* <span data-ttu-id="b28a6-108">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="b28a6-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b28a6-109">Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.</span><span class="sxs-lookup"><span data-stu-id="b28a6-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="b28a6-110">Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.</span><span class="sxs-lookup"><span data-stu-id="b28a6-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="b28a6-111">Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.</span><span class="sxs-lookup"><span data-stu-id="b28a6-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="b28a6-112">Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.</span><span class="sxs-lookup"><span data-stu-id="b28a6-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="b28a6-113">Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b28a6-114">Folgendes ist derzeit *nicht möglich*:</span><span class="sxs-lookup"><span data-stu-id="b28a6-114">For now, you *can't*:</span></span>

* <span data-ttu-id="b28a6-115">Untersuchen von Arrays</span><span class="sxs-lookup"><span data-stu-id="b28a6-115">Inspect arrays.</span></span>
* <span data-ttu-id="b28a6-116">Zeigen auf Member zum Untersuchen</span><span class="sxs-lookup"><span data-stu-id="b28a6-116">Hover to inspect members.</span></span>
* <span data-ttu-id="b28a6-117">Schrittweises Debuggen in oder aus verwaltetem Code</span><span class="sxs-lookup"><span data-stu-id="b28a6-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="b28a6-118">Vollständige Unterstützung für die Untersuchung von Werttypen</span><span class="sxs-lookup"><span data-stu-id="b28a6-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="b28a6-119">Halt bei Ausnahmefehlern</span><span class="sxs-lookup"><span data-stu-id="b28a6-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b28a6-120">Erreichen von Breakpoints beim App-Start</span><span class="sxs-lookup"><span data-stu-id="b28a6-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="b28a6-121">Debuggen einer App mit Service Worker</span><span class="sxs-lookup"><span data-stu-id="b28a6-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="b28a6-122">Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.</span><span class="sxs-lookup"><span data-stu-id="b28a6-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b28a6-123">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b28a6-123">Prerequisites</span></span>

<span data-ttu-id="b28a6-124">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="b28a6-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b28a6-125">Microsoft Edge (Version 80 oder höher)</span><span class="sxs-lookup"><span data-stu-id="b28a6-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="b28a6-126">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="b28a6-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="b28a6-127">Aktivieren des Debuggens für Visual Studio und Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b28a6-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="b28a6-128">Das Debuggen ist für neue Projekte automatisch aktiviert, die mit der Blazor WebAssembly-Projektvorlage von ASP.NET Core 3.2 Preview 3 oder höher erstellt werden ([aktuelles Release ist 3.2 Preview 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="b28a6-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="b28a6-129">Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei *launchSettings.json* im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:</span><span class="sxs-lookup"><span data-stu-id="b28a6-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b28a6-130">Anschließend sollte die Datei *launchSettings.json* dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="b28a6-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b28a6-131">Die `inspectUri`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="b28a6-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="b28a6-132">ermöglicht es der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.</span><span class="sxs-lookup"><span data-stu-id="b28a6-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b28a6-133">weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.</span><span class="sxs-lookup"><span data-stu-id="b28a6-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="b28a6-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b28a6-134">Visual Studio</span></span>

<span data-ttu-id="b28a6-135">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b28a6-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b28a6-136">Stellen Sie sicher, dass Sie [das neueste Preview-Release von Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 oder höher) installiert haben.</span><span class="sxs-lookup"><span data-stu-id="b28a6-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="b28a6-137">Erstellen Sie eine neue in ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="b28a6-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b28a6-138">Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="b28a6-139">Legen Sie einen Breakpoint in der `IncrementCount`-Methode von *Counter.razor* fest.</span><span class="sxs-lookup"><span data-stu-id="b28a6-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="b28a6-140">Rufen Sie die Registerkarte **Counter** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:</span><span class="sxs-lookup"><span data-stu-id="b28a6-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="b28a6-142">Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:</span><span class="sxs-lookup"><span data-stu-id="b28a6-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="b28a6-144">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b28a6-145">Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="b28a6-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="b28a6-146">Legen Sie einen Breakpoint auf der Seite *FetchData.razor* in `OnInitializedAsync` fest.</span><span class="sxs-lookup"><span data-stu-id="b28a6-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="b28a6-147">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="b28a6-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b28a6-148">Rufen Sie die Registerkarte **FetchData** auf, um den ersten Breakpoint in der `FetchData`-Komponente zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:</span><span class="sxs-lookup"><span data-stu-id="b28a6-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="b28a6-150">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="b28a6-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="b28a6-152">Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="b28a6-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="b28a6-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b28a6-153">Visual Studio Code</span></span>

<span data-ttu-id="b28a6-154">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b28a6-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="b28a6-155">Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="b28a6-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Erweiterungen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS-Vorschaudebugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="b28a6-158">Öffnen Sie eine vorhandene Blazor WebAssembly-App mit aktiviertem Debuggen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="b28a6-159">Wenn Sie die folgende Benachrichtigung erhalten, dass zusätzliches Setup zum Aktivieren des Debuggens erforderlich ist, stellen Sie sicher, dass Sie die richtigen Erweiterungen installiert und den JavaScript-Vorschaudebugger aktiviert haben. Laden Sie das Fenster dann neu:</span><span class="sxs-lookup"><span data-stu-id="b28a6-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="b28a6-161">Eine Benachrichtigung bietet an, die erforderlichen Ressourcen zum Erstellen und Debuggen zur Apps hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="b28a6-162">Wählen Sie **Ja** aus:</span><span class="sxs-lookup"><span data-stu-id="b28a6-162">Select **Yes**:</span></span>

     ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="b28a6-164">Das Starten der App im Debugger ist ein zweistufiger Prozess:</span><span class="sxs-lookup"><span data-stu-id="b28a6-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="b28a6-165">1\.</span><span class="sxs-lookup"><span data-stu-id="b28a6-165">1\.</span></span> <span data-ttu-id="b28a6-166">Starten Sie die App **zuerst** mit der Startkonfiguration **.NET Core Launch (Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="b28a6-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="b28a6-167">2\.</span><span class="sxs-lookup"><span data-stu-id="b28a6-167">2\.</span></span> <span data-ttu-id="b28a6-168">**Nachdem die App gestartet wurde**, starten Sie den Browser mit der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (erfordert Google Chrome).</span><span class="sxs-lookup"><span data-stu-id="b28a6-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b28a6-169">Ändern Sie den `type`-Wert der Startkonfiguration in *.vscode/launch.json* von `pwa-chrome` in `pwa-msedge`, um Microsoft Edge anstelle von Google Chrome zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b28a6-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="b28a6-170">Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="b28a6-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="b28a6-172">Debuggen im Browser</span><span class="sxs-lookup"><span data-stu-id="b28a6-172">Debug in the browser</span></span>

1. <span data-ttu-id="b28a6-173">Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.</span><span class="sxs-lookup"><span data-stu-id="b28a6-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b28a6-174">Drücken Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b28a6-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="b28a6-175">Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b28a6-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="b28a6-176">Wenn das Remotedebuggen deaktiviert ist, wird die Fehlerseite mit der Aussage generiert, dass **keine debugfähige Browserregisterkarte gefunden werden konnte**.</span><span class="sxs-lookup"><span data-stu-id="b28a6-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="b28a6-177">Die Fehlerseite enthält Anweisungen zum Ausführen des Browsers bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="b28a6-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="b28a6-178">*Schließen Sie alle Browserinstanzen*, und starten Sie den Browser gemäß den Anweisungen neu.</span><span class="sxs-lookup"><span data-stu-id="b28a6-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="b28a6-179">Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnen die Tastenkombination zum Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an.</span><span class="sxs-lookup"><span data-stu-id="b28a6-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="b28a6-180">Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="b28a6-181">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="b28a6-182">Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b28a6-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="b28a6-183"> bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="b28a6-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b28a6-184">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="b28a6-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b28a6-185">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="b28a6-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b28a6-186">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="b28a6-186">Browser source maps</span></span>

<span data-ttu-id="b28a6-187">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="b28a6-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b28a6-188">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="b28a6-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b28a6-189">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="b28a6-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b28a6-190">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b28a6-190">Troubleshoot</span></span>

<span data-ttu-id="b28a6-191">Wenn Sie auf Fehler stoßen, könnte der folgende Tipp helfen:</span><span class="sxs-lookup"><span data-stu-id="b28a6-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="b28a6-192">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="b28a6-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b28a6-193">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="b28a6-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
