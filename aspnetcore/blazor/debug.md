---
title: Debuggen ASP.net Core blazor
author: guardrex
description: Informationen zum Debuggen von blazor-apps.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/31/2019
uid: blazor/debug
ms.openlocfilehash: 37c6009727a4f62b61793adca0d83cdd53be4b9a
ms.sourcegitcommit: 3204bc89ae6354b61ee0a9b2770ebe5214b7790c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68948370"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="1f2cd-103">Debuggen ASP.net Core blazor</span><span class="sxs-lookup"><span data-stu-id="1f2cd-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="1f2cd-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="1f2cd-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="1f2cd-105">*Frühe* Unterstützung für das Debuggen von Client seitigen blazor-apps, die auf Webassembly in Chrome ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-105">*Early* support exists for debugging Blazor client-side apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="1f2cd-106">Die Debugger-Funktionen sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="1f2cd-107">Folgende Szenarien sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1f2cd-107">Available scenarios include:</span></span>

* <span data-ttu-id="1f2cd-108">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="1f2cd-109">Einzelschritt (`F10`) durch den Code oder die Fortsetzung`F8`()-Codeausführung.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="1f2cd-110">Beachten Sie in der lokalen Anzeige die Werte aller lokalen Variablen vom Typ `int`, `string`und `bool`.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="1f2cd-111">Weitere Informationen finden Sie in der-Aufrufkette, einschließlich der von JavaScript in .net und von .net bis JavaScript aus.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="1f2cd-112">Folgendes ist *nicht*möglich:</span><span class="sxs-lookup"><span data-stu-id="1f2cd-112">You *can't*:</span></span>

* <span data-ttu-id="1f2cd-113">Beachten Sie die Werte aller lokalen Variablen, die `int`nicht `string`, oder `bool`sind.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="1f2cd-114">Beachten Sie die Werte aller Klasseneigenschaften oder-Felder.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="1f2cd-115">Zeigen Sie auf Variablen, um deren Werte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="1f2cd-116">Auswerten von Ausdrücken in der Konsole.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="1f2cd-117">Schrittweises Ausführen von asynchronen Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-117">Step across async calls.</span></span>
* <span data-ttu-id="1f2cd-118">Führen Sie die meisten anderen normalen Debugszenarien aus.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="1f2cd-119">Die Entwicklung weiterer debuggingszenarien ist ein Fortschritt des Engineering-Teams.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f2cd-120">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="1f2cd-120">Prerequisites</span></span>

<span data-ttu-id="1f2cd-121">Das Debuggen erfordert einen der folgenden Browser:</span><span class="sxs-lookup"><span data-stu-id="1f2cd-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="1f2cd-122">Google Chrome (Version 70 oder höher)</span><span class="sxs-lookup"><span data-stu-id="1f2cd-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="1f2cd-123">Microsoft Edge-Vorschau ([Edge dev-Channel](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="1f2cd-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="1f2cd-124">Prozedur</span><span class="sxs-lookup"><span data-stu-id="1f2cd-124">Procedure</span></span>

1. <span data-ttu-id="1f2cd-125">Führen Sie eine Client seitige blazor-APP `Debug` in der Konfiguration aus.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-125">Run a Blazor client-side app in `Debug` configuration.</span></span> <span data-ttu-id="1f2cd-126">Übergeben Sie `--configuration Debug` die Option an den Befehl [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="1f2cd-127">Greifen Sie im Browser auf die APP zu.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="1f2cd-128">Platzieren Sie den Tastaturfokus auf die APP, nicht auf den Bereich Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="1f2cd-129">Der Bereich Entwicklertools kann geschlossen werden, wenn das Debuggen initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="1f2cd-130">Wählen Sie die folgende blazor-spezifische Tastenkombination aus:</span><span class="sxs-lookup"><span data-stu-id="1f2cd-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="1f2cd-131">`Shift+Alt+D`unter Windows/Linux</span><span class="sxs-lookup"><span data-stu-id="1f2cd-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="1f2cd-132">`Shift+Cmd+D`unter macOS</span><span class="sxs-lookup"><span data-stu-id="1f2cd-132">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="1f2cd-133">Remote Debuggen aktivieren</span><span class="sxs-lookup"><span data-stu-id="1f2cd-133">Enable remote debugging</span></span>

<span data-ttu-id="1f2cd-134">Wenn das Remote Debuggen deaktiviert ist, wird die Fehlerseite " **Debugfähige Browser Registerkarte nicht gefunden** " von Chrome generiert.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-134">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="1f2cd-135">Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome mit dem geöffneten debugsport, sodass der blazor-debugproxy eine Verbindung mit der App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-135">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="1f2cd-136">*Schließen Sie alle Chrome-Instanzen* , und starten Sie Chrome wie beschrieben neu.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-136">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="1f2cd-137">Debuggen der App</span><span class="sxs-lookup"><span data-stu-id="1f2cd-137">Debug the app</span></span>

<span data-ttu-id="1f2cd-138">Nachdem Chrome mit aktiviertem Remote Debugging ausgeführt wurde, öffnet die Tastenkombination Debuggen eine neue Registerkarte Debugger. Nach kurzer Zeit wird auf der Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-138">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="1f2cd-139">Erweitern Sie jede Assembly, und suchen Sie nach den zum Debuggen verfügbaren *CS*/ *. Razor* -Quelldateien.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-139">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="1f2cd-140">Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der APP, und die Breakpoints werden bei der Ausführung des Codes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-140">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="1f2cd-141">Nach dem Erreichen eines halte Punkts wird ein Einzelschritt (`F10`) durch den Code oder die fort`F8`Setzung ()-Codeausführung normal ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-141">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="1f2cd-142">Blazor bietet einen debugproxy, der das [Chrome devtools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll mit erweitert. Netzwerk spezifische Informationen.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-142">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="1f2cd-143">Wenn das Debuggen der Tastenkombination gedrückt wird, zeigt blazor die Chrome devtools auf dem Proxy an.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-143">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="1f2cd-144">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher muss das Remote Debugging aktiviert werden).</span><span class="sxs-lookup"><span data-stu-id="1f2cd-144">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="1f2cd-145">Browser-Quell Zuordnungen</span><span class="sxs-lookup"><span data-stu-id="1f2cd-145">Browser source maps</span></span>

<span data-ttu-id="1f2cd-146">Browser-Quell Zuordnungen ermöglichen es dem Browser, kompilierte Dateien wieder den ursprünglichen Quelldateien zuzuordnen, und werden häufig für das Client seitige Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-146">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="1f2cd-147">Allerdings ist blazor derzeit nicht C# direkt auf JavaScript/WASM zuordnet.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-147">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="1f2cd-148">Stattdessen führt blazor eine Il-Interpretation innerhalb des Browsers durch, sodass die Quell Zuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-148">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="1f2cd-149">Tipps zur Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="1f2cd-149">Troubleshooting tip</span></span>

<span data-ttu-id="1f2cd-150">Wenn Fehler auftreten, hilft Ihnen der folgende Tipp möglicherweise:</span><span class="sxs-lookup"><span data-stu-id="1f2cd-150">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="1f2cd-151">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-151">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="1f2cd-152">Führen `localStorage.clear()` Sie in der-Konsole aus, um Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="1f2cd-152">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
