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
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242770"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="4af4b-103">Debuggen der ASP.NET Core Blazor-Assembly</span><span class="sxs-lookup"><span data-stu-id="4af4b-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="4af4b-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="4af4b-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="4af4b-105">Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4af4b-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="4af4b-106">Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="4af4b-107">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="4af4b-107">Available scenarios include:</span></span>

* <span data-ttu-id="4af4b-108">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="4af4b-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="4af4b-109">Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.</span><span class="sxs-lookup"><span data-stu-id="4af4b-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="4af4b-110">Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.</span><span class="sxs-lookup"><span data-stu-id="4af4b-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="4af4b-111">Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.</span><span class="sxs-lookup"><span data-stu-id="4af4b-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="4af4b-112">Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.</span><span class="sxs-lookup"><span data-stu-id="4af4b-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="4af4b-113">Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="4af4b-114">Folgendes ist derzeit *nicht möglich*:</span><span class="sxs-lookup"><span data-stu-id="4af4b-114">For now, you *can't*:</span></span>

* <span data-ttu-id="4af4b-115">Halt bei Ausnahmefehlern</span><span class="sxs-lookup"><span data-stu-id="4af4b-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="4af4b-116">Erreichen von Breakpoints beim App-Start</span><span class="sxs-lookup"><span data-stu-id="4af4b-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="4af4b-117">Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.</span><span class="sxs-lookup"><span data-stu-id="4af4b-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4af4b-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4af4b-118">Prerequisites</span></span>

<span data-ttu-id="4af4b-119">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="4af4b-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="4af4b-120">Microsoft Edge (Version 80 oder höher)</span><span class="sxs-lookup"><span data-stu-id="4af4b-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="4af4b-121">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="4af4b-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="4af4b-122">Aktivieren des Debuggens für Visual Studio und Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4af4b-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="4af4b-123">Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei `launchSettings.json` im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:</span><span class="sxs-lookup"><span data-stu-id="4af4b-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="4af4b-124">Nach dem Aktualisieren sollte die Datei `launchSettings.json` dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="4af4b-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="4af4b-125">Die `inspectUri`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="4af4b-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="4af4b-126">ermöglicht es der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="4af4b-127">weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.</span><span class="sxs-lookup"><span data-stu-id="4af4b-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="4af4b-128">Die Platzhalterwerte für das WebSocket-Protokoll (`wsProtocol`), den Host (`url.hostname`), den Port (`url.port`) und den Inspektor-URI im gestarteten Browser (`browserInspectUri`) werden vom Framework bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="4af4b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4af4b-129">Visual Studio</span></span>

<span data-ttu-id="4af4b-130">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4af4b-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="4af4b-131">Erstellen Sie eine neue in ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="4af4b-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="4af4b-132">Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="4af4b-133">Legen Sie in `Pages/Counter.razor` einen Breakpoint in der `IncrementCount`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="4af4b-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="4af4b-134">Rufen Sie die Registerkarte **`Counter`** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:</span><span class="sxs-lookup"><span data-stu-id="4af4b-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="4af4b-136">Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:</span><span class="sxs-lookup"><span data-stu-id="4af4b-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="4af4b-138">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="4af4b-139">Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="4af4b-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="4af4b-140">Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.</span><span class="sxs-lookup"><span data-stu-id="4af4b-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="4af4b-141">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="4af4b-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="4af4b-142">Rufen Sie die Registerkarte **`Fetch Data`** auf, um den ersten Breakpoint in der Komponente `FetchData` zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:</span><span class="sxs-lookup"><span data-stu-id="4af4b-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="4af4b-144">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="4af4b-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="4af4b-146">Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="4af4b-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="4af4b-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4af4b-147">Visual Studio Code</span></span>

<span data-ttu-id="4af4b-148">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="4af4b-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="4af4b-149">Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="4af4b-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Erweiterungen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS-Vorschaudebugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="4af4b-152">Debuggen der eigenständigen Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4af4b-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="4af4b-153">Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="4af4b-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="4af4b-154">Wenn Sie die folgende Benachrichtigung erhalten, bedeutet das, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="4af4b-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="4af4b-155">Vergewissern Sie sich, dass die richtige Erweiterung installiert ist.</span><span class="sxs-lookup"><span data-stu-id="4af4b-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="4af4b-156">Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="4af4b-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="4af4b-157">Laden Sie das Fenster neu.</span><span class="sxs-lookup"><span data-stu-id="4af4b-157">Reload the window.</span></span>

   ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="4af4b-159">Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.</span><span class="sxs-lookup"><span data-stu-id="4af4b-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="4af4b-160">Wenn Sie dazu aufgefordert werden, wählen Sie die **Blazor WebAssembly debuggen**, um das Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="4af4b-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Liste der verfügbaren Debugoptionen](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="4af4b-162">Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4af4b-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="4af4b-163">Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="4af4b-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="4af4b-165">Debuggen einer gehosteten Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4af4b-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="4af4b-166">Öffnen Sie die gehostete Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="4af4b-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="4af4b-167">Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="4af4b-168">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="4af4b-168">Select **Yes**.</span></span>

   ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="4af4b-170">Wählen Sie im Auswahlfenster das Projekt *Server* in der gehosteten Lösung.</span><span class="sxs-lookup"><span data-stu-id="4af4b-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="4af4b-171">Zum Starten des Debuggers wird eine `launch.json`-Datei mit der Startkonfiguration generiert.</span><span class="sxs-lookup"><span data-stu-id="4af4b-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="4af4b-172">Anfügen an eine vorhandene Debugsitzung</span><span class="sxs-lookup"><span data-stu-id="4af4b-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="4af4b-173">Um an eine laufende Blazor-App anzufügen, erstellen Sie eine `launch.json`-Datei mit der folgenden Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="4af4b-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="4af4b-174">Das Anfügen an eine Debugsitzung wird nur für eigenständige Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="4af4b-175">Um das Full-Stack-Debuggen zu verwenden, muss die App über VS Code gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="4af4b-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="4af4b-176">Optionen für die Startkonfiguration</span><span class="sxs-lookup"><span data-stu-id="4af4b-176">Launch configuration options</span></span>

<span data-ttu-id="4af4b-177">Für den Debugtyp `blazorwasm` werden die folgenden Optionen für die Startkonfiguration unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="4af4b-178">Option</span><span class="sxs-lookup"><span data-stu-id="4af4b-178">Option</span></span>    | <span data-ttu-id="4af4b-179">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4af4b-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="4af4b-180">Verwenden Sie `launch`, um eine Debugsitzung zu starten und an eine Blazor WebAssembly-App anzufügen oder `attach`, um eine Debugsitzung an eine bereits laufende App anzufügen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="4af4b-181">Die URL, die beim Debuggen im Browser geöffnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="4af4b-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="4af4b-182">Wird standardmäßig auf `https://localhost:5001` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="4af4b-183">Der Browser, der für die Debugsitzung gestartet werden muss.</span><span class="sxs-lookup"><span data-stu-id="4af4b-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="4af4b-184">Wird auf `edge` oder `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="4af4b-185">Wird standardmäßig auf `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="4af4b-186">Wird zum Generieren von Protokollen vom JS-Debugger verwendet.</span><span class="sxs-lookup"><span data-stu-id="4af4b-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="4af4b-187">Zum Generieren von Protokollen auf `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="4af4b-188">Muss auf `true` gesetzt werden, wenn eine gehostete Blazor WebAssembly-App gestartet und debuggt werden soll.</span><span class="sxs-lookup"><span data-stu-id="4af4b-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="4af4b-189">Gibt den absoluten Pfad des Webservers an.</span><span class="sxs-lookup"><span data-stu-id="4af4b-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="4af4b-190">Muss gesetzt werden, wenn eine App von einer Unterroute bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4af4b-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="4af4b-191">Die Anzahl der Millisekunden, die auf das Anfügen der Debugsitzung gewartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="4af4b-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="4af4b-192">Wird standardmäßig auf 30.000 Millisekunden (30 Sekunden) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4af4b-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="4af4b-193">Ein Verweis auf die ausführbare Datei zur Ausführung des Servers der gehosteten App.</span><span class="sxs-lookup"><span data-stu-id="4af4b-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="4af4b-194">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="4af4b-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="4af4b-195">Das Arbeitsverzeichnis, unter dem die App gestartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="4af4b-195">The working directory to launch the app under.</span></span> <span data-ttu-id="4af4b-196">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="4af4b-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="4af4b-197">Die Umgebungsvariable, die für den gestarteten Prozess bereitgestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="4af4b-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="4af4b-198">Trifft nur zu, wenn `hosted` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4af4b-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="4af4b-199">Beispiel für Startkonfigurationen</span><span class="sxs-lookup"><span data-stu-id="4af4b-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="4af4b-200">Starten und Debuggen einer eigenständigen Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="4af4b-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="4af4b-201">Anfügen an eine laufende App unter einer angegebenen URL</span><span class="sxs-lookup"><span data-stu-id="4af4b-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="4af4b-202">Starten und Debuggen einer gehosteten Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="4af4b-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="4af4b-203">Debuggen im Browser</span><span class="sxs-lookup"><span data-stu-id="4af4b-203">Debug in the browser</span></span>

1. <span data-ttu-id="4af4b-204">Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.</span><span class="sxs-lookup"><span data-stu-id="4af4b-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="4af4b-205">Drücken Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="4af4b-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="4af4b-206">Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4af4b-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="4af4b-207">Wenn das Remotedebuggen deaktiviert ist, wird die Fehlerseite mit der Aussage generiert, dass **keine debugfähige Browserregisterkarte gefunden werden konnte**.</span><span class="sxs-lookup"><span data-stu-id="4af4b-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="4af4b-208">Die Fehlerseite enthält Anweisungen zum Ausführen des Browsers bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="4af4b-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="4af4b-209">*Schließen Sie alle Browserinstanzen*, und starten Sie den Browser gemäß den Anweisungen neu.</span><span class="sxs-lookup"><span data-stu-id="4af4b-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="4af4b-210">Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnen die Tastenkombination zum Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an.</span><span class="sxs-lookup"><span data-stu-id="4af4b-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="4af4b-211">Erweitern Sie jede Assembly, und suchen Sie die `.cs`/`.razor`-Quelldateien, die zum Debuggen zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="4af4b-212">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="4af4b-213">Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="4af4b-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="4af4b-214"> bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="4af4b-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="4af4b-215">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="4af4b-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="4af4b-216">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="4af4b-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="4af4b-217">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="4af4b-217">Browser source maps</span></span>

<span data-ttu-id="4af4b-218">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4af4b-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="4af4b-219">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="4af4b-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="4af4b-220">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="4af4b-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4af4b-221">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="4af4b-221">Troubleshoot</span></span>

<span data-ttu-id="4af4b-222">Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:</span><span class="sxs-lookup"><span data-stu-id="4af4b-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="4af4b-223">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="4af4b-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="4af4b-224">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="4af4b-225">Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen.</span><span class="sxs-lookup"><span data-stu-id="4af4b-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="4af4b-226">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="4af4b-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
