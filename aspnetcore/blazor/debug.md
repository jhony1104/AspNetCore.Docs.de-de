---
title: Debuggen ASP.net Core blazor
author: guardrex
description: Informationen zum Debuggen von blazor-apps.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/debug
ms.openlocfilehash: 9fc3f1d2dd7dc79d2ba3d64bff6e0f92ac2cf6dc
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391182"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="7bbaa-103">Debuggen ASP.net Core blazor</span><span class="sxs-lookup"><span data-stu-id="7bbaa-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="7bbaa-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7bbaa-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7bbaa-105">*Frühe* Unterstützung für das Debuggen von blazor Webassembly-apps, die auf Webassembly in Chrome ausgeführt werden</span><span class="sxs-lookup"><span data-stu-id="7bbaa-105">*Early* support exists for debugging Blazor WebAssembly apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="7bbaa-106">Die Debugger-Funktionen sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="7bbaa-107">Folgende Szenarien sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-107">Available scenarios include:</span></span>

* <span data-ttu-id="7bbaa-108">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7bbaa-109">Einzelschritt (`F10`) durch den Code oder die Fortsetzung (`F8`) Codeausführung.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="7bbaa-110">Beachten Sie in der *lokalen Anzeige die* Werte aller lokalen Variablen vom Typ `int`, `string` und `bool`.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="7bbaa-111">Weitere Informationen finden Sie in der-Aufrufkette, einschließlich der von JavaScript in .net und von .net bis JavaScript aus.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7bbaa-112">Folgendes ist *nicht*möglich:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-112">You *can't*:</span></span>

* <span data-ttu-id="7bbaa-113">Beachten Sie die Werte aller lokalen Variablen, die keine `int`, `string` oder `bool` sind.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="7bbaa-114">Beachten Sie die Werte aller Klasseneigenschaften oder-Felder.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="7bbaa-115">Zeigen Sie auf Variablen, um deren Werte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="7bbaa-116">Auswerten von Ausdrücken in der Konsole.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="7bbaa-117">Schrittweises Ausführen von asynchronen Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-117">Step across async calls.</span></span>
* <span data-ttu-id="7bbaa-118">Führen Sie die meisten anderen normalen Debugszenarien aus.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="7bbaa-119">Die Entwicklung weiterer debuggingszenarien ist ein Fortschritt des Engineering-Teams.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7bbaa-120">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="7bbaa-120">Prerequisites</span></span>

<span data-ttu-id="7bbaa-121">Das Debuggen erfordert einen der folgenden Browser:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7bbaa-122">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="7bbaa-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="7bbaa-123">Microsoft Edge-Vorschau ([Edge dev-Channel](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="7bbaa-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="7bbaa-124">Prozedur</span><span class="sxs-lookup"><span data-stu-id="7bbaa-124">Procedure</span></span>

1. <span data-ttu-id="7bbaa-125">Führen Sie eine blazor Webassembly-app in `Debug`-Konfiguration aus.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-125">Run a Blazor WebAssembly app in `Debug` configuration.</span></span> <span data-ttu-id="7bbaa-126">Übergeben Sie die Option `--configuration Debug` an den Befehl [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="7bbaa-127">Greifen Sie im Browser auf die APP zu.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="7bbaa-128">Platzieren Sie den Tastaturfokus auf die APP, nicht auf den Bereich Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="7bbaa-129">Der Bereich Entwicklertools kann geschlossen werden, wenn das Debuggen initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="7bbaa-130">Wählen Sie die folgende blazor-spezifische Tastenkombination aus:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="7bbaa-131">`Shift+Alt+D` unter Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="7bbaa-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="7bbaa-132">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="7bbaa-132">`Shift+Cmd+D` on macOS</span></span>
1. <span data-ttu-id="7bbaa-133">Führen Sie die auf dem Bildschirm aufgeführten Schritte aus, um den Browser mit aktiviertem Remote Debugging neu zu starten.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-133">Follow the steps listed on the screen to restart the browser with remote debugging enabled.</span></span>
1. <span data-ttu-id="7bbaa-134">Wählen Sie die folgende blazor-spezifische Tastenkombination erneut aus, um die Debugsitzung zu starten:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-134">Select the following Blazor-specific keyboard shortcut once again to start the debug session:</span></span>
   * <span data-ttu-id="7bbaa-135">`Shift+Alt+D` unter Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="7bbaa-135">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="7bbaa-136">`Shift+Cmd+D` unter macOS</span><span class="sxs-lookup"><span data-stu-id="7bbaa-136">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="7bbaa-137">Remote Debuggen aktivieren</span><span class="sxs-lookup"><span data-stu-id="7bbaa-137">Enable remote debugging</span></span>

<span data-ttu-id="7bbaa-138">Wenn das Remote Debuggen deaktiviert ist, wird die Fehlerseite " **Debugfähige Browser Registerkarte nicht gefunden** " von Chrome generiert.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-138">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="7bbaa-139">Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome mit dem geöffneten debugsport, sodass der blazor-debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-139">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="7bbaa-140">*Schließen Sie alle Chrome-Instanzen* , und starten Sie Chrome wie beschrieben neu.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-140">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="7bbaa-141">Debuggen der App</span><span class="sxs-lookup"><span data-stu-id="7bbaa-141">Debug the app</span></span>

<span data-ttu-id="7bbaa-142">Nachdem Chrome mit aktiviertem Remote Debugging ausgeführt wurde, öffnet die Tastenkombination Debuggen eine neue Registerkarte Debugger. Nach kurzer Zeit wird auf der Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-142">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="7bbaa-143">Erweitern Sie die einzelnen Assemblys, und suchen Sie die zu debuggingquelldateien *. cs*/ *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="7bbaa-143">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="7bbaa-144">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der APP, und die Breakpoints werden bei der Ausführung des Codes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-144">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="7bbaa-145">Nach dem Erreichen eines halte Punkts wird ein Einzelschritt (`F10`) durch den Code oder die Fortsetzung (`F8`) der Codeausführung normal ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-145">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="7bbaa-146">Blazor bietet einen debugproxy, der das [Chrome devtools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll mit erweitert. Netzwerk spezifische Informationen.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-146">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7bbaa-147">Wenn das Debuggen der Tastenkombination gedrückt wird, zeigt blazor die Chrome devtools auf dem Proxy an.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-147">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7bbaa-148">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher muss das Remote Debugging aktiviert werden).</span><span class="sxs-lookup"><span data-stu-id="7bbaa-148">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7bbaa-149">Browser-Quell Zuordnungen</span><span class="sxs-lookup"><span data-stu-id="7bbaa-149">Browser source maps</span></span>

<span data-ttu-id="7bbaa-150">Browser-Quell Zuordnungen ermöglichen es dem Browser, kompilierte Dateien wieder den ursprünglichen Quelldateien zuzuordnen, und werden häufig für das Client seitige Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-150">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7bbaa-151">Allerdings ist blazor derzeit nicht C# direkt auf JavaScript/WASM zuordnet.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-151">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7bbaa-152">Stattdessen führt blazor eine Il-Interpretation innerhalb des Browsers durch, sodass die Quell Zuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-152">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="7bbaa-153">Tipps zur Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="7bbaa-153">Troubleshooting tip</span></span>

<span data-ttu-id="7bbaa-154">Wenn Fehler auftreten, hilft Ihnen der folgende Tipp möglicherweise:</span><span class="sxs-lookup"><span data-stu-id="7bbaa-154">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="7bbaa-155">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-155">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7bbaa-156">Führen Sie in der-Konsole `localStorage.clear()` aus, um Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="7bbaa-156">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
