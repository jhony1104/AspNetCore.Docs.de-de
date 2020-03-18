---
title: Debuggen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648313"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="34d6e-103">Debuggen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="34d6e-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="34d6e-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="34d6e-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="34d6e-105">Für das Debuggen von Blazor WebAssembly unter Verwendung der Entwicklertools des Browsers in Chromium-basierten Browsern (Chrome/Edge) gibt es *frühzeitige* Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="34d6e-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="34d6e-106">Daran wird derzeit gearbeitet:</span><span class="sxs-lookup"><span data-stu-id="34d6e-106">Work is in progress to:</span></span>

* <span data-ttu-id="34d6e-107">Vollständige Aktivierung des Debuggens in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34d6e-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="34d6e-108">Aktivieren des Debuggens in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="34d6e-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="34d6e-109">Debuggerfunktionen werden eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="34d6e-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="34d6e-110">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="34d6e-110">Available scenarios include:</span></span>

* <span data-ttu-id="34d6e-111">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="34d6e-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="34d6e-112">Einzelschritt (`F10`) durch den Code oder Wiederaufnahme (`F8`) der Codeausführung.</span><span class="sxs-lookup"><span data-stu-id="34d6e-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="34d6e-113">Beobachten Sie in der Anzeige für *lokale Variablen* die Werte beliebiger lokaler Variablen vom Typ `int`, `string` und `bool`.</span><span class="sxs-lookup"><span data-stu-id="34d6e-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="34d6e-114">Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="34d6e-115">Folgendes ist *nicht* möglich:</span><span class="sxs-lookup"><span data-stu-id="34d6e-115">You *can't*:</span></span>

* <span data-ttu-id="34d6e-116">Beobachten der Werte beliebiger lokaler Variablen, die nicht den Typ `int`, `string` oder `bool` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="34d6e-117">Beobachten der Werte beliebiger Klasseneigenschaften oder Felder.</span><span class="sxs-lookup"><span data-stu-id="34d6e-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="34d6e-118">Bewegen des Mauszeigers über Variablen, um deren Werte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="34d6e-119">Auswerten von Ausdrücken in der Konsole.</span><span class="sxs-lookup"><span data-stu-id="34d6e-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="34d6e-120">Wechseln zwischen asynchronen Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-120">Step across async calls.</span></span>
* <span data-ttu-id="34d6e-121">Durchführen der meisten anderen normalen Debugszenarien.</span><span class="sxs-lookup"><span data-stu-id="34d6e-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="34d6e-122">Die Entwicklung weiterer Debugszenarien ist ein ständiger Schwerpunkt des Entwicklungsteams.</span><span class="sxs-lookup"><span data-stu-id="34d6e-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34d6e-123">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="34d6e-123">Prerequisites</span></span>

<span data-ttu-id="34d6e-124">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="34d6e-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="34d6e-125">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="34d6e-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="34d6e-126">Microsoft Edge Preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="34d6e-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="34d6e-127">Prozedur</span><span class="sxs-lookup"><span data-stu-id="34d6e-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="34d6e-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34d6e-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="34d6e-129">Die Debugunterstützung in Visual Studio befindet sich in einem frühen Stadium der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="34d6e-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="34d6e-130">**F5**-Debuggen wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="34d6e-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="34d6e-131">Führen Sie eine Blazor WebAssembly-App in der `Debug`-Konfiguration ohne Debuggen aus (**STRG** + **F5** statt **F5**).</span><span class="sxs-lookup"><span data-stu-id="34d6e-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="34d6e-132">Öffnen Sie die Debugeigenschaften der App (letzter Eintrag im Menü **Debuggen**), und kopieren Sie die HTTP **App-URL**.</span><span class="sxs-lookup"><span data-stu-id="34d6e-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="34d6e-133">Navigieren Sie mit einem Chromium-basierten Browser (Edge Beta oder Chrome) zur HTTP-Adresse (nicht zur HTTPS-Adresse) der App.</span><span class="sxs-lookup"><span data-stu-id="34d6e-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="34d6e-134">Platzieren Sie den Tastaturfokus auf die App im Browserfenster, nicht auf den Bereich der Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="34d6e-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="34d6e-135">Für dieses Verfahren ist es am besten, den Bereich der Entwicklertools geschlossen zu halten.</span><span class="sxs-lookup"><span data-stu-id="34d6e-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="34d6e-136">Nachdem das Debuggen begonnen hat, können Sie den Bereich der Entwicklertools wieder öffnen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="34d6e-137">Wählen Sie die folgende Blazor-spezifische Tastenkombination:</span><span class="sxs-lookup"><span data-stu-id="34d6e-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="34d6e-138">`Shift+Alt+D` unter Windows</span><span class="sxs-lookup"><span data-stu-id="34d6e-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="34d6e-139">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="34d6e-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="34d6e-140">Wenn Sie die Meldung darüber erhalten, dass keine **debugfähige Browserregisterkarte gefunden werden konnte**, finden Sie weitere Informationen unter [Aktivieren des Remotedebuggens](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="34d6e-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="34d6e-141">Nachdem Sie das Remotedebuggen aktiviert haben:</span><span class="sxs-lookup"><span data-stu-id="34d6e-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="34d6e-142">1\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-142">1\.</span></span> <span data-ttu-id="34d6e-143">Ein neues Browserfenster wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="34d6e-143">A new browser window opens.</span></span> <span data-ttu-id="34d6e-144">Schließen Sie das vorherige Fenster.</span><span class="sxs-lookup"><span data-stu-id="34d6e-144">Close the prior window.</span></span>

   <span data-ttu-id="34d6e-145">2\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-145">2\.</span></span> <span data-ttu-id="34d6e-146">Platzieren Sie den Tastaturfokus auf die App im Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="34d6e-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="34d6e-147">3\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-147">3\.</span></span> <span data-ttu-id="34d6e-148">Wählen Sie die Blazor-spezifische Tastenkombination im neuen Browserfenster aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.</span><span class="sxs-lookup"><span data-stu-id="34d6e-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="34d6e-149">4\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-149">4\.</span></span> <span data-ttu-id="34d6e-150">Die Registerkarte **DevTools** wird im Browser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="34d6e-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="34d6e-151">**Wählen Sie die Registerkarte der App im Browserfenster erneut aus.**</span><span class="sxs-lookup"><span data-stu-id="34d6e-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="34d6e-152">Informationen zum Anhängen der App an Visual Studio finden Sie im Abschnitt [Anhängen an den Prozess in Visual Studio](#attach-to-process-in-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="34d6e-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="34d6e-153">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="34d6e-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="34d6e-154">Führen Sie eine Blazor WebAssembly-App in der `Debug`-Konfiguration aus, indem Sie die Option `--configuration Debug` an den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) übergeben: `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="34d6e-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="34d6e-155">Navigieren Sie zu der App an der im Fenster der Shell angezeigten HTTP-URL.</span><span class="sxs-lookup"><span data-stu-id="34d6e-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="34d6e-156">Platzieren Sie den Tastaturfokus auf die App, nicht auf den Bereich der Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="34d6e-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="34d6e-157">Für dieses Verfahren ist es am besten, den Bereich der Entwicklertools geschlossen zu halten.</span><span class="sxs-lookup"><span data-stu-id="34d6e-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="34d6e-158">Nachdem das Debuggen begonnen hat, können Sie den Bereich der Entwicklertools wieder öffnen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="34d6e-159">Wählen Sie die folgende Blazor-spezifische Tastenkombination:</span><span class="sxs-lookup"><span data-stu-id="34d6e-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="34d6e-160">`Shift+Alt+D` unter Windows</span><span class="sxs-lookup"><span data-stu-id="34d6e-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="34d6e-161">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="34d6e-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="34d6e-162">Wenn Sie die Meldung darüber erhalten, dass keine **debugfähige Browserregisterkarte gefunden werden konnte**, finden Sie weitere Informationen unter [Aktivieren des Remotedebuggens](#enable-remote-debugging).</span><span class="sxs-lookup"><span data-stu-id="34d6e-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="34d6e-163">Nachdem Sie das Remotedebuggen aktiviert haben:</span><span class="sxs-lookup"><span data-stu-id="34d6e-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="34d6e-164">1\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-164">1\.</span></span> <span data-ttu-id="34d6e-165">Ein neues Browserfenster wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="34d6e-165">A new browser window opens.</span></span> <span data-ttu-id="34d6e-166">Schließen Sie das vorherige Fenster.</span><span class="sxs-lookup"><span data-stu-id="34d6e-166">Close the prior window.</span></span>

   <span data-ttu-id="34d6e-167">2\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-167">2\.</span></span> <span data-ttu-id="34d6e-168">Platzieren Sie den Tastaturfokus auf die App im Browserfenster, nicht auf den Bereich der Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="34d6e-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="34d6e-169">3\.</span><span class="sxs-lookup"><span data-stu-id="34d6e-169">3\.</span></span> <span data-ttu-id="34d6e-170">Wählen Sie die Blazor-spezifische Tastenkombination im neuen Browserfenster aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.</span><span class="sxs-lookup"><span data-stu-id="34d6e-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="34d6e-171">Aktivieren des Remotedebuggens</span><span class="sxs-lookup"><span data-stu-id="34d6e-171">Enable remote debugging</span></span>

<span data-ttu-id="34d6e-172">Wenn das Remote-Debuggen deaktiviert ist, wird von Chrome die Fehlerseite mit der Aussage generiert, dass eine **debugfähige Browserregisterkarte nicht gefunden werden konnte**.</span><span class="sxs-lookup"><span data-stu-id="34d6e-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="34d6e-173">Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="34d6e-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="34d6e-174">*Schließen Sie alle Chrome-Instanzen*, und starten Sie Chrome wie angewiesen neu.</span><span class="sxs-lookup"><span data-stu-id="34d6e-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="34d6e-175">Debuggen der App</span><span class="sxs-lookup"><span data-stu-id="34d6e-175">Debug the app</span></span>

<span data-ttu-id="34d6e-176">Sobald Chrome mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination für das Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an.</span><span class="sxs-lookup"><span data-stu-id="34d6e-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="34d6e-177">Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="34d6e-178">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="34d6e-179">Nachdem ein Breakpoint getroffen wurde, durchlaufen Sie den Code per Einzelschritt (`F10`), oder setzen Sie die Codeausführung normal fort (`F8`).</span><span class="sxs-lookup"><span data-stu-id="34d6e-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="34d6e-180"> bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="34d6e-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="34d6e-181">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="34d6e-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="34d6e-182">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="34d6e-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="34d6e-183">Anhängen an den Prozess in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34d6e-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="34d6e-184">Das Anhängen an den Prozess der App in Visual Studio ist ein *temporäres* Debugszenario für Blazor WebAssembly, während sich das **F5**-Debuggen in der Entwicklung befindet.</span><span class="sxs-lookup"><span data-stu-id="34d6e-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="34d6e-185">So fügen Sie den Prozess der aktiven App an Visual Studio an</span><span class="sxs-lookup"><span data-stu-id="34d6e-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="34d6e-186">Klicken Sie in Visual Studio auf **Debuggen** > **An den Prozess anhängen**.</span><span class="sxs-lookup"><span data-stu-id="34d6e-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="34d6e-187">Wählen Sie für den **Verbindungstyp** die Option **WebSocket mit Chrome DevTools-Protokoll (keine Authentifizierung)** aus.</span><span class="sxs-lookup"><span data-stu-id="34d6e-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="34d6e-188">Für das **Verbindungsziel** fügen Sie die HTTP-Adresse (nicht die HTTPS-Adresse) der App ein.</span><span class="sxs-lookup"><span data-stu-id="34d6e-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="34d6e-189">Wählen Sie **Aktualisieren** aus, um die Einträge unter **Verfügbare Prozesse** zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="34d6e-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="34d6e-190">Wählen Sie den zu debuggenden Browserprozess und dann **Anfügen** aus.</span><span class="sxs-lookup"><span data-stu-id="34d6e-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="34d6e-191">Wählen Sie im Dialogfeld **Codetyp auswählen** den Codetyp für den spezifischen Browser aus, den Sie anfügen (Edge oder Chrome), und wählen Sie dann **OK**.</span><span class="sxs-lookup"><span data-stu-id="34d6e-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="34d6e-192">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="34d6e-192">Browser source maps</span></span>

<span data-ttu-id="34d6e-193">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="34d6e-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="34d6e-194">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="34d6e-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="34d6e-195">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="34d6e-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="34d6e-196">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="34d6e-196">Troubleshoot</span></span>

<span data-ttu-id="34d6e-197">Wenn Sie auf Fehler stoßen, könnte der folgende Tipp helfen:</span><span class="sxs-lookup"><span data-stu-id="34d6e-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="34d6e-198">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="34d6e-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="34d6e-199">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="34d6e-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
