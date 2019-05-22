---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 80994cb84e9e0658ee90198b6bf992e5b374bf3c
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970037"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="833ba-103">Problembehandlung bei ASP.NET Core in IIS</span><span class="sxs-lookup"><span data-stu-id="833ba-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="833ba-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="833ba-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="833ba-105">Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="833ba-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="833ba-106">Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="833ba-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="833ba-107">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="833ba-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="833ba-108">Ein *502.5: Prozessfehler* oder ein *500.30: Startfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="833ba-109">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="833ba-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="833ba-110">Ein *502.5: Prozessfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="833ba-111">Weitere Themen zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="833ba-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="833ba-112"><xref:host-and-deploy/azure-apps/troubleshoot> Obwohl App Service das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und IIS für das Hosten von Apps verwendet, finden Sie im entsprechenden Artikel weitere, für App Service spezifische Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="833ba-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="833ba-113"><xref:fundamentals/error-handling> In diesem Artikel erfahren Sie, wie Sie Fehler in ASP.NET Core-Apps in der Entwicklungsphase auf einem lokalen System behandeln.</span><span class="sxs-lookup"><span data-stu-id="833ba-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="833ba-114">[Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="833ba-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="833ba-115">[Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) In diesem Artikel erfahren Sie, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.</span><span class="sxs-lookup"><span data-stu-id="833ba-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="833ba-116">App-Startfehler</span><span class="sxs-lookup"><span data-stu-id="833ba-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="833ba-117">502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="833ba-117">502.5 Process Failure</span></span>

<span data-ttu-id="833ba-118">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="833ba-118">The worker process fails.</span></span> <span data-ttu-id="833ba-119">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="833ba-119">The app doesn't start.</span></span>

<span data-ttu-id="833ba-120">Das ASP.NET Core-Modul kann den .NET-Back-End-Prozess nicht starten.</span><span class="sxs-lookup"><span data-stu-id="833ba-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="833ba-121">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="833ba-122">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="833ba-123">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="833ba-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="833ba-124">Das *freigegebene Framework* ist der Satz der Assemblys (*DLL*-Dateien), die auf dem Computer installiert werden und auf die ein Metapaket wie `Microsoft.AspNetCore.App` verweist.</span><span class="sxs-lookup"><span data-stu-id="833ba-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="833ba-125">Der Metapaketverweis kann eine mindestens erforderliche Version angeben.</span><span class="sxs-lookup"><span data-stu-id="833ba-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="833ba-126">Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).</span><span class="sxs-lookup"><span data-stu-id="833ba-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="833ba-127">Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn ein falsch konfiguriertes Hosting oder eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="833ba-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Browserfenster mit der Seite „502.5: Prozessfehler“](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="833ba-129">500.30: Prozessinterner Startupfehler</span><span class="sxs-lookup"><span data-stu-id="833ba-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="833ba-130">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="833ba-130">The worker process fails.</span></span> <span data-ttu-id="833ba-131">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="833ba-131">The app doesn't start.</span></span>

<span data-ttu-id="833ba-132">Das ASP.NET Core-Modul kann den .NET Core-CLR-In-Process nicht starten.</span><span class="sxs-lookup"><span data-stu-id="833ba-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="833ba-133">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="833ba-134">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="833ba-135">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="833ba-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="833ba-136">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="833ba-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="833ba-137">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="833ba-137">The worker process fails.</span></span> <span data-ttu-id="833ba-138">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="833ba-138">The app doesn't start.</span></span>

<span data-ttu-id="833ba-139">Das ASP.NET Core-Modul kann die .NET Core-CLR und den In-Process-Anforderungshandler (*aspnetcorev2_inprocess.dll*) nicht finden.</span><span class="sxs-lookup"><span data-stu-id="833ba-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="833ba-140">Überprüfen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="833ba-140">Check that:</span></span>

* <span data-ttu-id="833ba-141">Diese App legt entweder das NuGet-Paket [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) oder das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="833ba-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="833ba-142">Die Version des freigegebenen ASP.NET Core-Frameworks, die von der App als Ziel festgelegt ist, ist auf dem Zielcomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="833ba-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="833ba-143">500.0: Fehler beim Laden des prozessexternen Handlers</span><span class="sxs-lookup"><span data-stu-id="833ba-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="833ba-144">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="833ba-144">The worker process fails.</span></span> <span data-ttu-id="833ba-145">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="833ba-145">The app doesn't start.</span></span>

<span data-ttu-id="833ba-146">Das ASP.NET Core-Modul kann den Out-of-Process-Hostinganforderungshandler nicht finden.</span><span class="sxs-lookup"><span data-stu-id="833ba-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="833ba-147">Stellen Sie sicher, dass sich *aspnetcorev2_outofprocess.dll* in einem Unterordner mit *aspnetcorev2.dll* befindet.</span><span class="sxs-lookup"><span data-stu-id="833ba-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="833ba-148">500: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="833ba-148">500 Internal Server Error</span></span>

<span data-ttu-id="833ba-149">Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.</span><span class="sxs-lookup"><span data-stu-id="833ba-149">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="833ba-150">Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf.</span><span class="sxs-lookup"><span data-stu-id="833ba-150">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="833ba-151">Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt.</span><span class="sxs-lookup"><span data-stu-id="833ba-151">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="833ba-152">Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="833ba-152">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="833ba-153">Aus Sicht des Servers ist dies richtig.</span><span class="sxs-lookup"><span data-stu-id="833ba-153">From the server's perspective, that's correct.</span></span> <span data-ttu-id="833ba-154">Die App wurde gestartet, sie kann jedoch keine gültige Antwort generieren.</span><span class="sxs-lookup"><span data-stu-id="833ba-154">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="833ba-155">[Führen Sie die App in einer Eingabeaufforderung](#run-the-app-at-a-command-prompt) auf dem Server aus oder [aktivieren Sie das stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log), um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="833ba-155">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="833ba-156">Fehler beim Starten der Anwendung (ErrorCode 0x800700c1)</span><span class="sxs-lookup"><span data-stu-id="833ba-156">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="833ba-157">Die App konnte nicht gestartet werden, weil die Assembly der App (*.dll*) nicht geladen werden konnte.</span><span class="sxs-lookup"><span data-stu-id="833ba-157">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="833ba-158">Dieser Fehler tritt bei einem Bitanzahlkonflikt zwischen der veröffentlichten App und dem w3wp/iisexpress-Prozess auf.</span><span class="sxs-lookup"><span data-stu-id="833ba-158">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="833ba-159">Überprüfen Sie, ob die 32-Bit-Einstellung des Anwendungspools korrekt ist:</span><span class="sxs-lookup"><span data-stu-id="833ba-159">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="833ba-160">Wählen Sie im IIS-Manager unter **Anwendungspools** den Anwendungspool aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-160">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="833ba-161">Wählen Sie im Bereich **Aktionen** unter **Anwendungspool bearbeiten** **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-161">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="833ba-162">Wählen Sie **32-Bit-Anwendungen aktivieren** aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-162">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="833ba-163">Wenn Sie eine 32-Bit-(x86)-App bereitstellen, legen Sie den Wert auf `True` fest.</span><span class="sxs-lookup"><span data-stu-id="833ba-163">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="833ba-164">Wenn Sie eine 64-Bit-(x64)-App bereitstellen, legen Sie den Wert auf `False` fest.</span><span class="sxs-lookup"><span data-stu-id="833ba-164">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="833ba-165">Verbindungszurücksetzung</span><span class="sxs-lookup"><span data-stu-id="833ba-165">Connection reset</span></span>

<span data-ttu-id="833ba-166">Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="833ba-166">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="833ba-167">Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="833ba-167">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="833ba-168">Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt.</span><span class="sxs-lookup"><span data-stu-id="833ba-168">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="833ba-169">Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-169">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="833ba-170">Standardstarteinschränkungen</span><span class="sxs-lookup"><span data-stu-id="833ba-170">Default startup limits</span></span>

<span data-ttu-id="833ba-171">Das ASP.NET Core-Modul wurde mit dem Standardwert 120 Sekunden für das *StartupTimeLimit* konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="833ba-171">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="833ba-172">Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren.</span><span class="sxs-lookup"><span data-stu-id="833ba-172">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="833ba-173">Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="833ba-173">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="833ba-174">Problembehandlung bei App-Startfehlern</span><span class="sxs-lookup"><span data-stu-id="833ba-174">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="833ba-175">Aktivieren des Debugprotokolls des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="833ba-175">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="833ba-176">Fügen Sie der *web.config*-Datei der App die folgenden Handlereinstellungen hinzu, um Debugprotokolle des ASP.NET Core-Moduls zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="833ba-176">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="833ba-177">Überprüfen Sie, ob der für das Protokoll angegebene Pfad vorhanden ist, und ob die Identität des Anwendungspools Schreibberechtigungen für den Speicherort hat.</span><span class="sxs-lookup"><span data-stu-id="833ba-177">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="833ba-178">Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="833ba-178">Application Event Log</span></span>

<span data-ttu-id="833ba-179">Greifen Sie auf das Anwendungsereignisprotokoll zu:</span><span class="sxs-lookup"><span data-stu-id="833ba-179">Access the Application Event Log:</span></span>

1. <span data-ttu-id="833ba-180">Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-180">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="833ba-181">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="833ba-181">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="833ba-182">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="833ba-182">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="833ba-183">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="833ba-183">Search for errors associated with the failing app.</span></span> <span data-ttu-id="833ba-184">Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.</span><span class="sxs-lookup"><span data-stu-id="833ba-184">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="833ba-185">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="833ba-185">Run the app at a command prompt</span></span>

<span data-ttu-id="833ba-186">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="833ba-186">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="833ba-187">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="833ba-187">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="833ba-188">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="833ba-188">Framework-dependent deployment</span></span>

<span data-ttu-id="833ba-189">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="833ba-189">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="833ba-190">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen.</span><span class="sxs-lookup"><span data-stu-id="833ba-190">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="833ba-191">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="833ba-191">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="833ba-192">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="833ba-192">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="833ba-193">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-193">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="833ba-194">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-194">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="833ba-195">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="833ba-195">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="833ba-196">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="833ba-196">Self-contained deployment</span></span>

<span data-ttu-id="833ba-197">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="833ba-197">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="833ba-198">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-198">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="833ba-199">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="833ba-199">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="833ba-200">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="833ba-200">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="833ba-201">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-201">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="833ba-202">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-202">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="833ba-203">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="833ba-203">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="833ba-204">stdout-Protokoll des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="833ba-204">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="833ba-205">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="833ba-205">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="833ba-206">Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="833ba-206">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="833ba-207">Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-207">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="833ba-208">Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="833ba-208">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="833ba-209">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="833ba-209">Edit the *web.config* file.</span></span> <span data-ttu-id="833ba-210">Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="833ba-210">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="833ba-211">`stdout` im Pfad ist das Präfix des Protokolldateinamens.</span><span class="sxs-lookup"><span data-stu-id="833ba-211">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="833ba-212">Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="833ba-212">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="833ba-213">Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.</span><span class="sxs-lookup"><span data-stu-id="833ba-213">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="833ba-214">Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.</span><span class="sxs-lookup"><span data-stu-id="833ba-214">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="833ba-215">Speichern Sie die aktualisierte Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="833ba-215">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="833ba-216">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="833ba-216">Make a request to the app.</span></span>
1. <span data-ttu-id="833ba-217">Navigieren Sie zu dem Ordner *logs*.</span><span class="sxs-lookup"><span data-stu-id="833ba-217">Navigate to the *logs* folder.</span></span> <span data-ttu-id="833ba-218">Suchen und öffnen Sie das aktuelle stdout-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="833ba-218">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="833ba-219">Untersuchen Sie das Protokoll auf Fehler.</span><span class="sxs-lookup"><span data-stu-id="833ba-219">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="833ba-220">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="833ba-220">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="833ba-221">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="833ba-221">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="833ba-222">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="833ba-222">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="833ba-223">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="833ba-223">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="833ba-224">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="833ba-224">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="833ba-225">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="833ba-225">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="833ba-226">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="833ba-226">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="833ba-227">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="833ba-227">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="833ba-228">Aktivieren der Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="833ba-228">Enable the Developer Exception Page</span></span>

<span data-ttu-id="833ba-229">Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="833ba-229">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="833ba-230">Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="833ba-230">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="833ba-231">Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-231">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="833ba-232">Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="833ba-232">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="833ba-233">Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="833ba-233">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="833ba-234">Häufige Startfehler</span><span class="sxs-lookup"><span data-stu-id="833ba-234">Common startup errors</span></span>

<span data-ttu-id="833ba-235">Siehe <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="833ba-235">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="833ba-236">Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="833ba-236">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="833ba-237">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="833ba-237">Obtain data from an app</span></span>

<span data-ttu-id="833ba-238">Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals.</span><span class="sxs-lookup"><span data-stu-id="833ba-238">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="833ba-239">Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="833ba-239">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="833ba-240">Erstellen eines Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="833ba-240">Create a dump</span></span>

<span data-ttu-id="833ba-241">Ein *Speicherabbild* ist eine Momentaufnahme des Systemarbeitsspeichers. Ein Speicherabbild kann hilfreich sein, um die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="833ba-241">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="833ba-242">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="833ba-242">App crashes or encounters an exception</span></span>

<span data-ttu-id="833ba-243">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="833ba-243">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="833ba-244">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="833ba-244">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="833ba-245">Der App-Pool muss über Schreibzugriff auf den Ordner verfügen.</span><span class="sxs-lookup"><span data-stu-id="833ba-245">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="833ba-246">Führen Sie das [PowerShell-Skript „EnableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-246">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="833ba-247">Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-247">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="833ba-248">Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-248">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="833ba-249">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="833ba-249">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="833ba-250">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-250">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="833ba-251">Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-251">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="833ba-252">Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="833ba-252">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="833ba-253">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-253">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="833ba-254">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="833ba-254">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="833ba-255">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="833ba-255">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="833ba-256">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="833ba-256">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="833ba-257">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="833ba-257">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="833ba-258">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="833ba-258">Analyze the dump</span></span>

<span data-ttu-id="833ba-259">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-259">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="833ba-260">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="833ba-260">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="833ba-261">Remotedebuggen</span><span class="sxs-lookup"><span data-stu-id="833ba-261">Remote debugging</span></span>

<span data-ttu-id="833ba-262">Siehe [Remotedebuggen von ASP.NET Core auf einem IIS-Remotecomputer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in der Dokumentation zu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="833ba-262">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="833ba-263">Application Insights</span><span class="sxs-lookup"><span data-stu-id="833ba-263">Application Insights</span></span>

<span data-ttu-id="833ba-264">[Application Insights](/azure/application-insights/) stellt Telemetriedaten von Apps bereit, die von IIS gehostet werden, einschließlich Features für die Fehlerprotokollierung und die Berichterstellung.</span><span class="sxs-lookup"><span data-stu-id="833ba-264">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="833ba-265">Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-265">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="833ba-266">Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="833ba-266">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="833ba-267">Zusätzliche Hinweise</span><span class="sxs-lookup"><span data-stu-id="833ba-267">Additional advice</span></span>

<span data-ttu-id="833ba-268">Manchmal schlägt eine funktionsfähige App direkt nach der Durchführung eines Upgrades des .NET Core SDK auf dem Entwicklungscomputer oder der Paketversionen in der App fehl.</span><span class="sxs-lookup"><span data-stu-id="833ba-268">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="833ba-269">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-269">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="833ba-270">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="833ba-270">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="833ba-271">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="833ba-271">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="833ba-272">Löschen Sie die Paketcaches unter *%UserProfile%\\.nuget\\packages* und *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="833ba-272">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="833ba-273">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="833ba-273">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="833ba-274">Überprüfen Sie, ob die vorherige Bereitstellung auf dem Server vollständig gelöscht wurde, bevor Sie die App erneut bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="833ba-274">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="833ba-275">Sie können Paketcaches ganz einfach löschen, indem Sie `dotnet nuget locals all --clear` über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="833ba-275">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="833ba-276">Darüber hinaus können Paketcaches mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und durch Ausführung des Befehls `nuget locals all -clear` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-276">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="833ba-277">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="833ba-277">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="833ba-278">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="833ba-278">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
