---
title: ASP.net Core Debuggen Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-apps Debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159988"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="19ada-103">ASP.net Core Debuggen [!OP.NO-LOC(Blazor)]</span><span class="sxs-lookup"><span data-stu-id="19ada-103">Debug ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

[<span data-ttu-id="19ada-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="19ada-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="19ada-105">*Frühe* Unterstützung für das Debuggen [!OP.NO-LOC(Blazor)] Webassembly mithilfe der Browser Entwicklertools in Chrom basierten Browsern (Chrome/Microsoft Edge).</span><span class="sxs-lookup"><span data-stu-id="19ada-105">*Early* support exists for debugging [!OP.NO-LOC(Blazor)] WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="19ada-106">Die Arbeit wird in folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="19ada-106">Work is in progress to:</span></span>

* <span data-ttu-id="19ada-107">Aktivieren Sie das Debuggen in Visual Studio vollständig.</span><span class="sxs-lookup"><span data-stu-id="19ada-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="19ada-108">Aktivieren Sie das Debugging in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="19ada-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="19ada-109">Die Debugger-Funktionen sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="19ada-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="19ada-110">Folgende Szenarien sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="19ada-110">Available scenarios include:</span></span>

* <span data-ttu-id="19ada-111">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="19ada-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="19ada-112">Einzelschritt (`F10`) durch den Code oder die Fortsetzung (`F8`)-Codeausführung.</span><span class="sxs-lookup"><span data-stu-id="19ada-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="19ada-113">Beachten Sie in der *lokalen Anzeige die* Werte aller lokalen Variablen vom Typ `int`, `string`und `bool`.</span><span class="sxs-lookup"><span data-stu-id="19ada-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="19ada-114">Weitere Informationen finden Sie in der-Aufrufkette, einschließlich der von JavaScript in .net und von .net bis JavaScript aus.</span><span class="sxs-lookup"><span data-stu-id="19ada-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="19ada-115">Folgendes ist *nicht*möglich:</span><span class="sxs-lookup"><span data-stu-id="19ada-115">You *can't*:</span></span>

* <span data-ttu-id="19ada-116">Beachten Sie die Werte aller lokalen Variablen, die keine `int`, `string`oder `bool`sind.</span><span class="sxs-lookup"><span data-stu-id="19ada-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="19ada-117">Beachten Sie die Werte aller Klasseneigenschaften oder-Felder.</span><span class="sxs-lookup"><span data-stu-id="19ada-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="19ada-118">Zeigen Sie auf Variablen, um deren Werte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="19ada-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="19ada-119">Auswerten von Ausdrücken in der Konsole.</span><span class="sxs-lookup"><span data-stu-id="19ada-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="19ada-120">Schrittweises Ausführen von asynchronen Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="19ada-120">Step across async calls.</span></span>
* <span data-ttu-id="19ada-121">Führen Sie die meisten anderen normalen Debugszenarien aus.</span><span class="sxs-lookup"><span data-stu-id="19ada-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="19ada-122">Die Entwicklung weiterer debuggingszenarien ist ein Fortschritt des Engineering-Teams.</span><span class="sxs-lookup"><span data-stu-id="19ada-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="19ada-123">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="19ada-123">Prerequisites</span></span>

<span data-ttu-id="19ada-124">Das Debuggen erfordert einen der folgenden Browser:</span><span class="sxs-lookup"><span data-stu-id="19ada-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="19ada-125">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="19ada-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="19ada-126">Microsoft Edge-Vorschau ([Edge dev-Channel](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="19ada-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="19ada-127">-Prozedur</span><span class="sxs-lookup"><span data-stu-id="19ada-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19ada-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19ada-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="19ada-129">Die Debugunterstützung in Visual Studio ist in einer frühen Phase der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="19ada-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="19ada-130">**F5** -Debugging wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="19ada-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="19ada-131">Führen Sie eine [!OP.NO-LOC(Blazor)] Webassembly-app in `Debug` Konfiguration ohne Debuggen aus (**STRG**+**F5** anstelle von **F5**).</span><span class="sxs-lookup"><span data-stu-id="19ada-131">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="19ada-132">Öffnen Sie die Debugeigenschaften der APP (Letzter Eintrag im Menü **Debuggen** ), und kopieren Sie die URL der HTTP- **App**.</span><span class="sxs-lookup"><span data-stu-id="19ada-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="19ada-133">Navigieren Sie mit einem Chrom basierten Browser (Microsoft Edge Beta oder Chrome) zur http-Adresse (nicht zur HTTPS-Adresse) der app.</span><span class="sxs-lookup"><span data-stu-id="19ada-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="19ada-134">Platzieren Sie den Tastaturfokus in der APP im Browserfenster, nicht im Bereich Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="19ada-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="19ada-135">Es ist am besten, den Entwicklertools-Panel für dieses Verfahren geschlossen zu halten.</span><span class="sxs-lookup"><span data-stu-id="19ada-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="19ada-136">Nachdem das Debuggen gestartet wurde, können Sie den Bereich Entwicklertools erneut öffnen.</span><span class="sxs-lookup"><span data-stu-id="19ada-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="19ada-137">Wählen Sie die folgende [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus:</span><span class="sxs-lookup"><span data-stu-id="19ada-137">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="19ada-138">`Shift+Alt+D` unter Windows</span><span class="sxs-lookup"><span data-stu-id="19ada-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="19ada-139">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="19ada-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="19ada-140">Wenn Sie die **Registerkarte "Debugfähige Browser nicht gefunden**" erhalten, finden Sie weitere Informationen unter [Aktivieren von Remote Debugging](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="19ada-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="19ada-141">Nach dem Aktivieren von Remote Debugging:</span><span class="sxs-lookup"><span data-stu-id="19ada-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="19ada-142">1 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-142">1\.</span></span> <span data-ttu-id="19ada-143">Ein neues Browserfenster wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="19ada-143">A new browser window opens.</span></span> <span data-ttu-id="19ada-144">Schließen Sie das vorherige Fenster.</span><span class="sxs-lookup"><span data-stu-id="19ada-144">Close the prior window.</span></span>

   <span data-ttu-id="19ada-145">2 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-145">2\.</span></span> <span data-ttu-id="19ada-146">Platzieren Sie den Tastaturfokus in der APP im Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="19ada-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="19ada-147">3 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-147">3\.</span></span> <span data-ttu-id="19ada-148">Wählen Sie im neuen Browserfenster die [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.</span><span class="sxs-lookup"><span data-stu-id="19ada-148">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="19ada-149">4 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-149">4\.</span></span> <span data-ttu-id="19ada-150">Die Registerkarte **devtools** wird im Browser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="19ada-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="19ada-151">**Wählen Sie die Registerkarte der APP im Browserfenster erneut aus.**</span><span class="sxs-lookup"><span data-stu-id="19ada-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="19ada-152">Informationen zum Anfügen der APP an Visual Studio finden Sie [im Abschnitt Anfügen an den Prozess in Visual Studio](#attach-to-process-in-visual-studio) .</span><span class="sxs-lookup"><span data-stu-id="19ada-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="19ada-153">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="19ada-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="19ada-154">Führen Sie eine [!OP.NO-LOC(Blazor)] Webassembly-app in `Debug` Konfiguration aus, indem Sie die `--configuration Debug`-Option an den [dotnet Run](/dotnet/core/tools/dotnet-run) -Befehl übergeben: `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="19ada-154">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="19ada-155">Navigieren Sie zu der APP unter der http-URL, die im Fenster der Shell angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="19ada-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="19ada-156">Platzieren Sie den Tastaturfokus auf die APP, nicht auf den Bereich Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="19ada-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="19ada-157">Es ist am besten, den Entwicklertools-Panel für dieses Verfahren geschlossen zu halten.</span><span class="sxs-lookup"><span data-stu-id="19ada-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="19ada-158">Nachdem das Debuggen gestartet wurde, können Sie den Bereich Entwicklertools erneut öffnen.</span><span class="sxs-lookup"><span data-stu-id="19ada-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="19ada-159">Wählen Sie die folgende [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus:</span><span class="sxs-lookup"><span data-stu-id="19ada-159">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="19ada-160">`Shift+Alt+D` unter Windows</span><span class="sxs-lookup"><span data-stu-id="19ada-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="19ada-161">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="19ada-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="19ada-162">Wenn Sie die **Registerkarte "Debugfähige Browser nicht gefunden**" erhalten, finden Sie weitere Informationen unter [Aktivieren von Remote Debugging](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="19ada-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="19ada-163">Nach dem Aktivieren von Remote Debugging:</span><span class="sxs-lookup"><span data-stu-id="19ada-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="19ada-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-164">1\.</span></span> <span data-ttu-id="19ada-165">Ein neues Browserfenster wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="19ada-165">A new browser window opens.</span></span> <span data-ttu-id="19ada-166">Schließen Sie das vorherige Fenster.</span><span class="sxs-lookup"><span data-stu-id="19ada-166">Close the prior window.</span></span>

   <span data-ttu-id="19ada-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-167">2\.</span></span> <span data-ttu-id="19ada-168">Platzieren Sie den Tastaturfokus in der APP im Browserfenster, nicht im Bereich Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="19ada-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="19ada-169">3 \.</span><span class="sxs-lookup"><span data-stu-id="19ada-169">3\.</span></span> <span data-ttu-id="19ada-170">Wählen Sie im neuen Browserfenster die [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.</span><span class="sxs-lookup"><span data-stu-id="19ada-170">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="19ada-171">Aktivieren des Remotedebuggens</span><span class="sxs-lookup"><span data-stu-id="19ada-171">Enable remote debugging</span></span>

<span data-ttu-id="19ada-172">Wenn das Remote Debuggen deaktiviert ist, wird die Fehlerseite " **Debugfähige Browser Registerkarte nicht gefunden** " von Chrome generiert.</span><span class="sxs-lookup"><span data-stu-id="19ada-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="19ada-173">Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome mit dem geöffneten debugsport, sodass der Blazor debugerproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="19ada-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="19ada-174">*Schließen Sie alle Chrome-Instanzen* , und starten Sie Chrome wie beschrieben neu.</span><span class="sxs-lookup"><span data-stu-id="19ada-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="19ada-175">Debuggen der App</span><span class="sxs-lookup"><span data-stu-id="19ada-175">Debug the app</span></span>

<span data-ttu-id="19ada-176">Nachdem Chrome mit aktiviertem Remote Debugging ausgeführt wurde, öffnet die Tastenkombination Debuggen eine neue Registerkarte Debugger. Nach kurzer Zeit wird auf der Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App angezeigt.</span><span class="sxs-lookup"><span data-stu-id="19ada-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="19ada-177">Erweitern Sie jede Assembly, und suchen Sie die *. cs* -/ *. Razor* -Quelldateien, die zum Debuggen</span><span class="sxs-lookup"><span data-stu-id="19ada-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="19ada-178">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der APP, und die Breakpoints werden bei der Ausführung des Codes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="19ada-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="19ada-179">Nach dem Erreichen eines halte Punkts wird ein Einzelschritt (`F10`) durch den Code ausgeführt, oder die Codeausführung (`F8`) wird normal fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="19ada-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="19ada-180"> stellt einen debugproxy bereit, der das [Chrome devtools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll mit erweitert. Netzwerk spezifische Informationen.</span><span class="sxs-lookup"><span data-stu-id="19ada-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="19ada-181">Wenn das Debuggen der Tastenkombination gedrückt wird, zeigt Blazor die Chrome devtools auf dem Proxy an.</span><span class="sxs-lookup"><span data-stu-id="19ada-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="19ada-182">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher muss das Remote Debugging aktiviert werden).</span><span class="sxs-lookup"><span data-stu-id="19ada-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="19ada-183">Anfügen an den Prozess in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19ada-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="19ada-184">Das Anfügen an den Prozess der app in Visual Studio ist ein *temporäres* Debugszenario für Blazor Webassembly, während das **F5** -Debugging in der Entwicklung ist.</span><span class="sxs-lookup"><span data-stu-id="19ada-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="19ada-185">So fügen Sie den Prozess der laufenden app an Visual Studio an:</span><span class="sxs-lookup"><span data-stu-id="19ada-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="19ada-186">Wählen Sie in Visual Studio die Option **Debuggen** > **an den Prozess anhängen**.</span><span class="sxs-lookup"><span data-stu-id="19ada-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="19ada-187">Wählen Sie für **Verbindungstyp**die Option **Chrome devtools Protocol WebSocket (keine Authentifizierung)** aus.</span><span class="sxs-lookup"><span data-stu-id="19ada-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="19ada-188">Fügen Sie für das **Verbindungs Ziel**die http-Adresse (nicht die HTTPS-Adresse) der APP ein.</span><span class="sxs-lookup"><span data-stu-id="19ada-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="19ada-189">Wählen Sie **Aktualisieren** aus, um die Einträge unter **Verfügbare Prozesse**zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="19ada-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="19ada-190">Wählen Sie den zu debuggenden Browser Prozess und dann **Anfügen**aus.</span><span class="sxs-lookup"><span data-stu-id="19ada-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="19ada-191">Wählen Sie im Dialogfeld **Codetyp auswählen** den Codetyp für den jeweiligen Browser aus, an den Sie anhängen (Microsoft Edge oder Chrome), und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="19ada-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="19ada-192">Browser-Quell Zuordnungen</span><span class="sxs-lookup"><span data-stu-id="19ada-192">Browser source maps</span></span>

<span data-ttu-id="19ada-193">Browser-Quell Zuordnungen ermöglichen es dem Browser, kompilierte Dateien wieder den ursprünglichen Quelldateien zuzuordnen, und werden häufig für das Client seitige Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="19ada-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="19ada-194">Blazor wird jedoch derzeit nicht C# direkt zu JavaScript/WASM zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="19ada-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="19ada-195">Stattdessen Blazor die Il-Interpretation innerhalb des Browsers durchführt, sodass die Quell Zuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="19ada-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="19ada-196">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="19ada-196">Troubleshoot</span></span>

<span data-ttu-id="19ada-197">Wenn Fehler auftreten, hilft Ihnen der folgende Tipp möglicherweise:</span><span class="sxs-lookup"><span data-stu-id="19ada-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="19ada-198">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="19ada-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="19ada-199">Führen Sie in der-Konsole `localStorage.clear()` aus, um Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="19ada-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
