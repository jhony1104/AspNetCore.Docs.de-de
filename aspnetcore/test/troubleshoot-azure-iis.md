---
title: Problembehandlung ASP.net Core auf Azure App Service und IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit Azure App Service und Internetinformationsdienste (IIS) bereit Stellungen von ASP.net Core-apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/17/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 46d4a11c594844e059fa8555255d7321f7b48631
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308793"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="57314-103">Problembehandlung ASP.net Core auf Azure App Service und IIS</span><span class="sxs-lookup"><span data-stu-id="57314-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="57314-104">Von [Luke Latham](https://github.com/guardrex) und [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="57314-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="57314-105">Dieser Artikel enthält Informationen zu häufigen App-Start Fehlern und Anweisungen zur Diagnose von Fehlern, wenn eine APP für Azure App Service oder IIS bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="57314-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="57314-106">App-Start Fehler</span><span class="sxs-lookup"><span data-stu-id="57314-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="57314-107">Erläutert gängige HTTP-Statuscode Szenarien für den Start.</span><span class="sxs-lookup"><span data-stu-id="57314-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="57314-108">Problembehandlung bei Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57314-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="57314-109">Bietet Tipps zur Problembehandlung für apps, die in Azure App Service bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="57314-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="57314-110">Problembehandlung bei den Internetinformationsdiensten (IIS)</span><span class="sxs-lookup"><span data-stu-id="57314-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="57314-111">Bietet Tipps zur Problembehandlung für apps, die in IIS bereitgestellt oder lokal auf IIS Express ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="57314-112">Die Anleitung gilt sowohl für Windows Server-als auch für Windows-Desktop Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="57314-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="57314-113">Löschen von Paket Caches</span><span class="sxs-lookup"><span data-stu-id="57314-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="57314-114">Erläutert, was zu tun ist, wenn inkohärente Pakete eine APP beim Ausführen wichtiger Upgrades oder beim Ändern von Paketversionen unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="57314-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="57314-115">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="57314-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="57314-116">Listet zusätzliche Problem Behandlungs Themen auf.</span><span class="sxs-lookup"><span data-stu-id="57314-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="57314-117">App-Startfehler</span><span class="sxs-lookup"><span data-stu-id="57314-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="57314-118">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="57314-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="57314-119">Ein *502,5-Prozessfehler* oder ein *500,30-Start-Fehler* , der auftritt, wenn das lokale Debuggen mithilfe der in diesem Thema genannten Ratschläge diagnostiziert werden kann.</span><span class="sxs-lookup"><span data-stu-id="57314-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57314-120">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="57314-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="57314-121">Ein *502,5-Prozessfehler* , der auftritt, wenn das lokale Debuggen mithilfe der Ratschläge in diesem Thema diagnostiziert werden kann.</span><span class="sxs-lookup"><span data-stu-id="57314-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="57314-122">500: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="57314-122">500 Internal Server Error</span></span>

<span data-ttu-id="57314-123">Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.</span><span class="sxs-lookup"><span data-stu-id="57314-123">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="57314-124">Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf.</span><span class="sxs-lookup"><span data-stu-id="57314-124">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="57314-125">Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57314-125">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="57314-126">Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="57314-126">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="57314-127">Aus Sicht des Servers ist dies richtig.</span><span class="sxs-lookup"><span data-stu-id="57314-127">From the server's perspective, that's correct.</span></span> <span data-ttu-id="57314-128">Die App wurde gestartet, aber sie kann keine gültige Antwort generieren.</span><span class="sxs-lookup"><span data-stu-id="57314-128">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="57314-129">Führen Sie die APP an einer Eingabeaufforderung auf dem Server aus, oder aktivieren Sie das ASP.net Core Modul stdout Log, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="57314-129">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="57314-130">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="57314-130">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="57314-131">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-131">The worker process fails.</span></span> <span data-ttu-id="57314-132">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-132">The app doesn't start.</span></span>

<span data-ttu-id="57314-133">Das [ASP.net Core-Modul](xref:host-and-deploy/aspnet-core-module) kann die .net Core-CLR nicht finden und den in-Process-Anforderungs Handler (*aspnetcorev2_inprocess. dll*) finden.</span><span class="sxs-lookup"><span data-stu-id="57314-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="57314-134">Überprüfen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="57314-134">Check that:</span></span>

* <span data-ttu-id="57314-135">Diese App legt entweder das NuGet-Paket [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) oder das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="57314-135">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="57314-136">Die Version des freigegebenen ASP.NET Core-Frameworks, die von der App als Ziel festgelegt ist, ist auf dem Zielcomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="57314-136">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="57314-137">500.0: Fehler beim Laden des prozessexternen Handlers</span><span class="sxs-lookup"><span data-stu-id="57314-137">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="57314-138">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-138">The worker process fails.</span></span> <span data-ttu-id="57314-139">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-139">The app doesn't start.</span></span>

<span data-ttu-id="57314-140">Das [ASP.net Core Modul](xref:host-and-deploy/aspnet-core-module) kann den Out-of-Process-hostinganforderungshandler nicht finden.</span><span class="sxs-lookup"><span data-stu-id="57314-140">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="57314-141">Stellen Sie sicher, dass sich *aspnetcorev2_outofprocess.dll* in einem Unterordner mit *aspnetcorev2.dll* befindet.</span><span class="sxs-lookup"><span data-stu-id="57314-141">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="57314-142">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="57314-142">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="57314-143">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-143">The worker process fails.</span></span> <span data-ttu-id="57314-144">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-144">The app doesn't start.</span></span>

<span data-ttu-id="57314-145">Beim Laden [ASP.net Core Modul](xref:host-and-deploy/aspnet-core-module) Komponenten ist ein unbekannter Fehler aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="57314-145">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="57314-146">Führen Sie eine der folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="57314-146">Take one of the following actions:</span></span>

* <span data-ttu-id="57314-147">Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (über **Entwicklertools** > **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="57314-147">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="57314-148">Stellen Sie Ihre Frage auf Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="57314-148">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="57314-149">Melden Sie das Problem im [GitHub-Repository](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="57314-149">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="57314-150">500.30: Prozessinterner Startupfehler</span><span class="sxs-lookup"><span data-stu-id="57314-150">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="57314-151">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-151">The worker process fails.</span></span> <span data-ttu-id="57314-152">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-152">The app doesn't start.</span></span>

<span data-ttu-id="57314-153">Das [Modul ASP.net Core](xref:host-and-deploy/aspnet-core-module) versucht, die .net Core-CLR in einem Prozess zu starten, aber es kann nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-153">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="57314-154">Die Ursache für den Fehler eines Prozess Starts kann in der Regel von Einträgen im Anwendungs Ereignisprotokoll und vom ASP.net Core-Modul "stdout"-Protokoll bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-154">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="57314-155">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="57314-155">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="57314-156">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="57314-156">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="57314-157">500.31: Fehler bei der Suche nach nativen Abhängigkeiten in ANCM</span><span class="sxs-lookup"><span data-stu-id="57314-157">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="57314-158">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-158">The worker process fails.</span></span> <span data-ttu-id="57314-159">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-159">The app doesn't start.</span></span>

<span data-ttu-id="57314-160">Das [Modul ASP.net Core](xref:host-and-deploy/aspnet-core-module) versucht, die .net Core-Laufzeit in-Process zu starten, aber es kann nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-160">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="57314-161">Die häufigste Ursache dieses Startfehlers ist die fehlende Installation der Laufzeiten `Microsoft.NETCore.App` oder `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="57314-161">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="57314-162">Dieser Fehler tritt auf, wenn die App für die Zielversion 3.0 von ASP.NET Core bereitgestellt wurde, die aber auf dem Computer nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="57314-162">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="57314-163">Es wird beispielsweise folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="57314-163">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="57314-164">In der Fehlermeldung werden alle installierten .NET Core-Versionen aufgelistet sowie die für die App erforderliche Version.</span><span class="sxs-lookup"><span data-stu-id="57314-164">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="57314-165">Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:</span><span class="sxs-lookup"><span data-stu-id="57314-165">To fix this error, either:</span></span>

* <span data-ttu-id="57314-166">Installieren Sie die entsprechende .NET Core-Version auf Ihrem Computer.</span><span class="sxs-lookup"><span data-stu-id="57314-166">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="57314-167">Ändern Sie die Zielversion der App in eine .NET Core-Version, die auf Ihrem Computer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="57314-167">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="57314-168">Veröffentlichen Sie die App als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="57314-168">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="57314-169">Beim Ausführen im Entwicklungsmodus (wenn die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist) wird der jeweilige Fehler in die HTTP-Antwort geschrieben.</span><span class="sxs-lookup"><span data-stu-id="57314-169">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="57314-170">Die Ursache für den Fehler eines Prozess Starts finden Sie auch im Anwendungs Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="57314-170">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="57314-171">500.32: Fehler beim Laden der DLL in ANCM</span><span class="sxs-lookup"><span data-stu-id="57314-171">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="57314-172">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-172">The worker process fails.</span></span> <span data-ttu-id="57314-173">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-173">The app doesn't start.</span></span>

<span data-ttu-id="57314-174">Die häufigste Ursache für diesen Fehler ist die Veröffentlichung der App für eine nicht kompatible Prozessorarchitektur.</span><span class="sxs-lookup"><span data-stu-id="57314-174">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="57314-175">Der Fehler tritt auf, wenn der Arbeitsprozess als 32-Bit-App ausgeführt wird, die App aber für eine 64-Bit-Ausführung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="57314-175">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="57314-176">Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:</span><span class="sxs-lookup"><span data-stu-id="57314-176">To fix this error, either:</span></span>

* <span data-ttu-id="57314-177">Veröffentlichen Sie die App erneut, sodass Prozessorarchitektur und Arbeitsprozess übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="57314-177">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="57314-178">Veröffentlichen Sie die App als eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="57314-178">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="57314-179">500.33: Fehler beim Laden des Anforderungshandlers in ANCM</span><span class="sxs-lookup"><span data-stu-id="57314-179">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="57314-180">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-180">The worker process fails.</span></span> <span data-ttu-id="57314-181">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-181">The app doesn't start.</span></span>

<span data-ttu-id="57314-182">Die App verweist nicht auf das Framework `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="57314-182">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="57314-183">Nur apps, die `Microsoft.AspNetCore.App` das Framework als Ziel haben, können vom [ASP.net Core Modul](xref:host-and-deploy/aspnet-core-module)gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-183">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="57314-184">Vergewissern Sie sich, dass das Zielframework der App `Microsoft.AspNetCore.App` ist, um den Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="57314-184">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="57314-185">Überprüfen Sie `.runtimeconfig.json`, um das Zielframework der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="57314-185">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="57314-186">500.34: Gemischte Hostingmodelle in ANCM nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="57314-186">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="57314-187">Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-187">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="57314-188">Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="57314-188">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="57314-189">500.35: Mehrere In-Process-Anwendungen im selben Prozess in ANCM</span><span class="sxs-lookup"><span data-stu-id="57314-189">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="57314-190">Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-190">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="57314-191">Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="57314-191">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="57314-192">500.36: Fehler beim Laden des Out-of-Process-Handlers in ANCM</span><span class="sxs-lookup"><span data-stu-id="57314-192">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="57314-193">Der Out-of-Process-Anforderungshandler *aspnetcorev2_outofprocess.dll* befindet sich nicht neben der Datei *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="57314-193">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="57314-194">Dies deutet auf eine beschädigte Installation des [ASP.net Core Moduls](xref:host-and-deploy/aspnet-core-module)hin.</span><span class="sxs-lookup"><span data-stu-id="57314-194">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="57314-195">Reparieren Sie die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (für IIS) oder von Visual Studio (für IIS Express), um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="57314-195">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="57314-196">500.37: Fehler beim Starten von ANCM innerhalb des Zeitlimits für den Start</span><span class="sxs-lookup"><span data-stu-id="57314-196">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="57314-197">ANCM konnte nicht innerhalb des angegebenen Zeitlimits für den Start gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-197">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="57314-198">Das Standardzeitlimit beträgt 120 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="57314-198">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="57314-199">Dieser Fehler kann beim Starten einer großen Anzahl von Apps auf dem gleichen Computer auftreten.</span><span class="sxs-lookup"><span data-stu-id="57314-199">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="57314-200">Überprüfen Sie, ob beim Starten CPU-/Speicherauslastungsspitzen auf dem Server vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="57314-200">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="57314-201">In diesem Fall sollten Sie möglicherweise den Startvorgang einiger Apps staffeln.</span><span class="sxs-lookup"><span data-stu-id="57314-201">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="57314-202">502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="57314-202">502.5 Process Failure</span></span>

<span data-ttu-id="57314-203">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="57314-203">The worker process fails.</span></span> <span data-ttu-id="57314-204">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="57314-204">The app doesn't start.</span></span>

<span data-ttu-id="57314-205">Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kann den Workerprozess nicht starten.</span><span class="sxs-lookup"><span data-stu-id="57314-205">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="57314-206">Die Ursache für den Fehler eines Prozess Starts kann in der Regel von Einträgen im Anwendungs Ereignisprotokoll und vom ASP.net Core-Modul "stdout"-Protokoll bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-206">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="57314-207">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="57314-207">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="57314-208">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="57314-208">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="57314-209">Das *freigegebene Framework* ist der Satz der Assemblys (*DLL*-Dateien), die auf dem Computer installiert werden und auf die ein Metapaket wie `Microsoft.AspNetCore.App` verweist.</span><span class="sxs-lookup"><span data-stu-id="57314-209">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="57314-210">Der Metapaketverweis kann eine mindestens erforderliche Version angeben.</span><span class="sxs-lookup"><span data-stu-id="57314-210">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="57314-211">Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).</span><span class="sxs-lookup"><span data-stu-id="57314-211">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="57314-212">Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn ein falsch konfiguriertes Hosting oder eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="57314-212">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="57314-213">Fehler beim Starten der Anwendung (ErrorCode 0x800700c1)</span><span class="sxs-lookup"><span data-stu-id="57314-213">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="57314-214">Die App konnte nicht gestartet werden, weil die Assembly der App ( *.dll*) nicht geladen werden konnte.</span><span class="sxs-lookup"><span data-stu-id="57314-214">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="57314-215">Dieser Fehler tritt bei einem Bitanzahlkonflikt zwischen der veröffentlichten App und dem w3wp/iisexpress-Prozess auf.</span><span class="sxs-lookup"><span data-stu-id="57314-215">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="57314-216">Überprüfen Sie, ob die 32-Bit-Einstellung des Anwendungspools korrekt ist:</span><span class="sxs-lookup"><span data-stu-id="57314-216">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="57314-217">Wählen Sie im IIS-Manager unter **Anwendungspools** den Anwendungspool aus.</span><span class="sxs-lookup"><span data-stu-id="57314-217">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="57314-218">Wählen Sie im Bereich **Aktionen** unter **Anwendungspool bearbeiten** **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-218">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="57314-219">Wählen Sie **32-Bit-Anwendungen aktivieren** aus:</span><span class="sxs-lookup"><span data-stu-id="57314-219">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="57314-220">Wenn Sie eine 32-Bit-(x86)-App bereitstellen, legen Sie den Wert auf `True` fest.</span><span class="sxs-lookup"><span data-stu-id="57314-220">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="57314-221">Wenn Sie eine 64-Bit-(x64)-App bereitstellen, legen Sie den Wert auf `False` fest.</span><span class="sxs-lookup"><span data-stu-id="57314-221">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="57314-222">Verbindungszurücksetzung</span><span class="sxs-lookup"><span data-stu-id="57314-222">Connection reset</span></span>

<span data-ttu-id="57314-223">Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="57314-223">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="57314-224">Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="57314-224">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="57314-225">Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt.</span><span class="sxs-lookup"><span data-stu-id="57314-225">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="57314-226">Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.</span><span class="sxs-lookup"><span data-stu-id="57314-226">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="57314-227">Standardstarteinschränkungen</span><span class="sxs-lookup"><span data-stu-id="57314-227">Default startup limits</span></span>

<span data-ttu-id="57314-228">Das [ASP.net Core-Modul](xref:host-and-deploy/aspnet-core-module) ist mit einem Standardwert von " *StartupTimeLimit* " von 120 Sekunden konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="57314-228">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="57314-229">Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren.</span><span class="sxs-lookup"><span data-stu-id="57314-229">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="57314-230">Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="57314-230">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="57314-231">Problembehandlung bei Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57314-231">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="57314-232">Anwendungs Ereignisprotokoll (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="57314-232">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="57314-233">Verwenden Sie das Blatt **Diagnose und Problembehandlung** im Azure-Portal, um auf das Anwendungsereignisprotokoll zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="57314-233">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="57314-234">Öffnen Sie die App im Azure-Portal unter **App Services**.</span><span class="sxs-lookup"><span data-stu-id="57314-234">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="57314-235">Klicken Sie auf **Diagnose und Problembehandlung**.</span><span class="sxs-lookup"><span data-stu-id="57314-235">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="57314-236">Wählen Sie die Überschrift **Diagnosetools** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-236">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="57314-237">Klicken Sie unter **Supporttools** auf die Schaltfläche **Anwendungsereignisse**.</span><span class="sxs-lookup"><span data-stu-id="57314-237">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="57314-238">Überprüfen Sie den letzten vom Eintrag *IIS AspNetCoreModule* oder *IIS AspNetCoreModule V2* in der Spalte **Quelle** angegebenen Fehler.</span><span class="sxs-lookup"><span data-stu-id="57314-238">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="57314-239">Anstatt das Blatt **Diagnose und Problembehandlung** zu verwenden, können Sie die Anwendungsereignisprotokoll-Datei auch direkt mit [Kudu](https://github.com/projectkudu/kudu/wiki) untersuchen:</span><span class="sxs-lookup"><span data-stu-id="57314-239">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="57314-240">Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**.</span><span class="sxs-lookup"><span data-stu-id="57314-240">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="57314-241">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-241">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-242">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-242">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="57314-243">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-243">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="57314-244">Öffnen Sie den Ordner **LogFiles**.</span><span class="sxs-lookup"><span data-stu-id="57314-244">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="57314-245">Wählen Sie den Bleistift neben der Datei *eventlog.xml* aus.</span><span class="sxs-lookup"><span data-stu-id="57314-245">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="57314-246">Untersuchen Sie das Protokoll.</span><span class="sxs-lookup"><span data-stu-id="57314-246">Examine the log.</span></span> <span data-ttu-id="57314-247">Scrollen Sie zum Ende des Protokolls, um die aktuellsten Ereignisse anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="57314-247">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="57314-248">Führen Sie die App in der Kudu-Konsole aus</span><span class="sxs-lookup"><span data-stu-id="57314-248">Run the app in the Kudu console</span></span>

<span data-ttu-id="57314-249">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="57314-249">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="57314-250">Sie können die App in der [Kudu](https://github.com/projectkudu/kudu/wiki)-Remote-Ausführungskonsole ausführen, um den Fehler zu ermitteln:</span><span class="sxs-lookup"><span data-stu-id="57314-250">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="57314-251">Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**.</span><span class="sxs-lookup"><span data-stu-id="57314-251">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="57314-252">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-253">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="57314-254">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="57314-255">Testen einer 32-Bit-App (x86)</span><span class="sxs-lookup"><span data-stu-id="57314-255">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="57314-256">**Aktuelle Version**</span><span class="sxs-lookup"><span data-stu-id="57314-256">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="57314-257">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="57314-257">Run the app:</span></span>
   * <span data-ttu-id="57314-258">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="57314-258">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="57314-259">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="57314-259">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="57314-260">Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="57314-260">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="57314-261">**Framework-abhängige Bereitstellung, die in einem Vorschau Release ausgeführt wird**</span><span class="sxs-lookup"><span data-stu-id="57314-261">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="57314-262">*Erfordert die Installation der Runtimeerweiterung für ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="57314-262">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="57314-263">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` ist die Runtimeversion.)</span><span class="sxs-lookup"><span data-stu-id="57314-263">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="57314-264">Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="57314-264">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="57314-265">Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="57314-265">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="57314-266">Testen einer 64-Bit-App (x64)</span><span class="sxs-lookup"><span data-stu-id="57314-266">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="57314-267">**Aktuelle Version**</span><span class="sxs-lookup"><span data-stu-id="57314-267">**Current release**</span></span>

* <span data-ttu-id="57314-268">Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) (64-Bit, x64) handelt:</span><span class="sxs-lookup"><span data-stu-id="57314-268">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="57314-269">Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="57314-269">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="57314-270">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="57314-270">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="57314-271">Führen Sie die App aus: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="57314-271">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="57314-272">Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="57314-272">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="57314-273">**Framework-abhängige Bereitstellung, die in einem Vorschau Release ausgeführt wird**</span><span class="sxs-lookup"><span data-stu-id="57314-273">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="57314-274">*Erfordert die Installation der Runtimeerweiterung für ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="57314-274">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="57314-275">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` ist die Runtimeversion.)</span><span class="sxs-lookup"><span data-stu-id="57314-275">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="57314-276">Führen Sie die App aus: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="57314-276">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="57314-277">Die Konsolenausgabe der App, die Fehler anzeigt, wird an die Kudu-Konsole weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="57314-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="57314-278">ASP.net Core Modul "stdout"-Protokoll (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="57314-278">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="57314-279">Das stdout-Protokoll des ASP.NET Core-Moduls zeichnet häufig nützliche Fehlermeldungen auf, die nicht im Anwendungsereignisprotokoll enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="57314-279">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="57314-280">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="57314-280">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="57314-281">Navigieren Sie zum Blatt **Diagnose und Problembehandlung** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="57314-281">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="57314-282">Wählen Sie unter **SELECT PROBLEM CATEGORY** (PROBLEMKATEGORIE WÄHLEN) die Schaltfläche **Web App Down** (Web-App ausgefallen) aus.</span><span class="sxs-lookup"><span data-stu-id="57314-282">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="57314-283">Klicken Sie unter **Suggested Solutions** (Vorgeschlagene Lösungen)  > **Enable Stdout Log Redirection** (stdout-Protokollumleitung aktivieren) auf **Open Kudu Console to edit Web.Config** (Kudu-Konsole öffnen, um web.config zu bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="57314-283">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="57314-284">Öffnen Sie in der **Diagnosekonsole** die Ordner unter dem Pfad **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="57314-284">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="57314-285">Scrollen Sie nach unten, um die Datei *web.config* am Ende der Liste einzublenden.</span><span class="sxs-lookup"><span data-stu-id="57314-285">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="57314-286">Klicken Sie auf den Bleistift neben der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-286">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="57314-287">Setzen Sie **stdoutLogEnabled** auf `true`, und ändern Sie den Pfad **stdoutLogFile** in: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="57314-287">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="57314-288">Wählen Sie **Speichern** aus, um die aktualisierte Datei *web.config* zu speichern.</span><span class="sxs-lookup"><span data-stu-id="57314-288">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="57314-289">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="57314-289">Make a request to the app.</span></span>
1. <span data-ttu-id="57314-290">Kehren Sie zum Azure-Portal zurück.</span><span class="sxs-lookup"><span data-stu-id="57314-290">Return to the Azure portal.</span></span> <span data-ttu-id="57314-291">Wählen Sie das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-291">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="57314-292">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-292">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-293">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-293">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="57314-294">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-294">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="57314-295">Wählen Sie den Ordner **LogFiles** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-295">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="57314-296">Überprüfen Sie die Spalte **Geändert**, und wählen Sie den Bleistift aus, um das stdout-Protokoll mit dem letzten Änderungsdatum zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="57314-296">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="57314-297">Wenn die Protokolldatei geöffnet wird, wird der Fehler angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57314-297">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="57314-298">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="57314-298">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="57314-299">Kehren Sie in der Kudu-**Diagnosekonsole** zum Pfad **site** > **wwwroot** zurück, um die Datei *web.config* einzublenden.</span><span class="sxs-lookup"><span data-stu-id="57314-299">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="57314-300">Öffnen Sie die Datei **web.config** erneut, indem Sie den Bleistift auswählen.</span><span class="sxs-lookup"><span data-stu-id="57314-300">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="57314-301">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="57314-301">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="57314-302">Wählen Sie **Speichern** aus, um die Datei zu speichern.</span><span class="sxs-lookup"><span data-stu-id="57314-302">Select **Save** to save the file.</span></span>

<span data-ttu-id="57314-303">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="57314-303">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="57314-304">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="57314-304">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="57314-305">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="57314-305">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="57314-306">Verwenden Sie die stdout-Protokollierung nur für die Behandlung von App-Startproblemen.</span><span class="sxs-lookup"><span data-stu-id="57314-306">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="57314-307">Verwenden Sie für die allgemeine Protokollierung in einer ASP.NET Core-App nach dem Start eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="57314-307">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="57314-308">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="57314-308">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="57314-309">ASP.net Core moduldebugprotokoll (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="57314-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="57314-310">Das Debugprotokoll des ASP.NET Core-Moduls ermöglicht die zusätzliche und ausführlichere Protokollierung über das ASP.NET Core-Modul.</span><span class="sxs-lookup"><span data-stu-id="57314-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="57314-311">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="57314-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="57314-312">Führen Sie einen der folgenden Schritte aus, um das erweiterte Diagnoseprotokoll zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="57314-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="57314-313">Befolgen Sie den Anweisungen in den [erweiterten Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs), um die App für die erweiterte Diagnoseprotokollierung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="57314-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="57314-314">Stellen Sie die App erneut bereit.</span><span class="sxs-lookup"><span data-stu-id="57314-314">Redeploy the app.</span></span>
   * <span data-ttu-id="57314-315">Fügen Sie die in unter [Erweiterte Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) enthaltene `<handlerSettings>`-Klasse für die *web.config*-Datei der Live-App über die Kudu-Konsole hinzu:</span><span class="sxs-lookup"><span data-stu-id="57314-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="57314-316">Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**.</span><span class="sxs-lookup"><span data-stu-id="57314-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="57314-317">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-318">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="57314-319">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="57314-320">Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="57314-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="57314-321">Bearbeiten Sie die Datei *web.config*, indem Sie auf die Bleistiftschaltfläche klicken.</span><span class="sxs-lookup"><span data-stu-id="57314-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="57314-322">Fügen Sie den Abschnitt `<handlerSettings>` wie in den [erweiterten Diagnoseprotokollen](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) dargestellt hinzu.</span><span class="sxs-lookup"><span data-stu-id="57314-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="57314-323">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="57314-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="57314-324">Öffnen Sie **Erweiterte Tools** im Bereich **Entwicklungstools**.</span><span class="sxs-lookup"><span data-stu-id="57314-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="57314-325">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-326">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="57314-327">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="57314-328">Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="57314-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="57314-329">Wenn Sie keinen Pfad für die *aspnetcore-debug.log*-Datei angegeben haben, wird die Datei in der Liste aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="57314-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="57314-330">Wenn Sie jedoch einen Pfad angegeben haben, navigieren Sie zum Speicherort der Protokolldatei.</span><span class="sxs-lookup"><span data-stu-id="57314-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="57314-331">Öffnen Sie die Protokolldatei über die Bleistiftschaltfläche neben dem Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="57314-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="57314-332">Deaktivieren Sie die Debugprotokollierung, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="57314-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="57314-333">Führen Sie einen der folgenden Schritte aus, um das erweiterte Debugprotokoll zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="57314-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="57314-334">Entfernen Sie `<handlerSettings>` aus der lokalen *web.config*-Datei, und stellen Sie die App erneut bereit.</span><span class="sxs-lookup"><span data-stu-id="57314-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="57314-335">Verwenden Sie die Kudu-Konsole, um die *web.config*-Datei zu bearbeiten und den Abschnitt `<handlerSettings>` zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="57314-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="57314-336">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="57314-336">Save the file.</span></span>

<span data-ttu-id="57314-337">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="57314-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="57314-338">Wenn das Debugprotokoll nicht deaktiviert werden kann, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="57314-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="57314-339">Es gibt keine Begrenzung für die Größe der Protokolldatei.</span><span class="sxs-lookup"><span data-stu-id="57314-339">There's no limit on log file size.</span></span> <span data-ttu-id="57314-340">Nutzen Sie die Debugprotokollierung nur für die Behandlung von App-Startproblemen.</span><span class="sxs-lookup"><span data-stu-id="57314-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="57314-341">Verwenden Sie für die allgemeine Protokollierung in einer ASP.NET Core-App nach dem Start eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="57314-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="57314-342">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="57314-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="57314-343">Langsame oder hängende app (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="57314-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="57314-344">Wenn eine App bei einer Anforderung langsam reagiert oder hängt, lesen Sie folgende Artikel:</span><span class="sxs-lookup"><span data-stu-id="57314-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="57314-345">Problembehandlung von Leistungsproblemen in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57314-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="57314-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (Verwenden der Absturzdiagnose-Websiteerweiterung zum Erfassen des Speicherabbilds bei vorübergehenden Ausnahmeproblemen oder Leistungsproblemen der Azure-Web-App)</span><span class="sxs-lookup"><span data-stu-id="57314-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="57314-347">Überwachungsblätter</span><span class="sxs-lookup"><span data-stu-id="57314-347">Monitoring blades</span></span>

<span data-ttu-id="57314-348">Überwachungsblätter bieten eine Alternative zur Problembehandlung mit den weiter oben in diesem Thema beschriebenen Methoden.</span><span class="sxs-lookup"><span data-stu-id="57314-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="57314-349">Die Blätter können zur Diagnose von Serie 500-Fehlern verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="57314-350">Vergewissern Sie sich, dass ASP.NET Core-Erweiterungen installiert sind.</span><span class="sxs-lookup"><span data-stu-id="57314-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="57314-351">Wenn die Erweiterungen nicht installiert sind, installieren Sie diese manuell:</span><span class="sxs-lookup"><span data-stu-id="57314-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="57314-352">Wählen Sie im Blattabschnitt **ENTWICKLUNGSTOOLS** das Blatt **Erweiterungen** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="57314-353">Die **ASP.NET Core-Erweiterungen** werden in der Liste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57314-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="57314-354">Wenn die Erweiterungen nicht installiert sind, klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="57314-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="57314-355">Wählen Sie die **ASP.NET Core-Erweiterungen** aus der Liste.</span><span class="sxs-lookup"><span data-stu-id="57314-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="57314-356">Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="57314-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="57314-357">Klicken Sie auf **OK** auf dem Blatt **Erweiterung hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="57314-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="57314-358">Mit einer Popup-Informationsmeldung wird die erfolgreiche Installation der Erweiterungen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57314-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="57314-359">Wenn die stdout-Protokollierung nicht aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="57314-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="57314-360">Wählen Sie im Azure-Portal das Blatt **Erweiterte Tools** im Bereich **ENTWICKLUNGSTOOLS** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="57314-361">Klicken Sie auf **Los&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="57314-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="57314-362">Die Kudu-Konsole wird in einer neuen Browserregisterkarte oder in einem neuen Fenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="57314-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="57314-363">Öffnen Sie mithilfe der Navigationsleiste am oberen Rand der Seite die **Debugging-Konsole**, und wählen Sie **CMD** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="57314-364">Öffnen Sie die Ordner unter dem Pfad **site** > **wwwroot**, und scrollen Sie nach unten, um die Datei *web.config* am Ende der Liste einzublenden.</span><span class="sxs-lookup"><span data-stu-id="57314-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="57314-365">Klicken Sie auf den Bleistift neben der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="57314-366">Setzen Sie **stdoutLogEnabled** auf `true`, und ändern Sie den Pfad **stdoutLogFile** in: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="57314-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="57314-367">Wählen Sie **Speichern** aus, um die aktualisierte Datei *web.config* zu speichern.</span><span class="sxs-lookup"><span data-stu-id="57314-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="57314-368">Fahren Sie mit der Aktivierung der Diagnoseprotokollierung fort:</span><span class="sxs-lookup"><span data-stu-id="57314-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="57314-369">Wählen Sie im Azure-Portal das Blatt **Diagnoseprotokolle** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="57314-370">Wählen Sie den Parameter **On** für **Anwendungsprotokollierung (Dateisystem)** und **Detaillierte Fehlermeldungen** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="57314-371">Klicken Sie auf **Speichern** am oberen Rand des Blatts.</span><span class="sxs-lookup"><span data-stu-id="57314-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="57314-372">Um die Ablaufverfolgung für Anforderungsfehler, auch bekannt als Protokollierung der Anforderungsfehler-Ereignispufferung (FREB), einzuschließen, wählen Sie den Parameter **On** für **Ablaufverfolgung für Anforderungsfehler** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="57314-373">Wählen Sie das Blatt **Protokollstream** aus, das direkt unter dem Blatt **Diagnoseprotokolle** im Portal angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="57314-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="57314-374">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="57314-374">Make a request to the app.</span></span>
1. <span data-ttu-id="57314-375">In den Protokollstreamdaten wird die Ursache des Fehlers angegeben.</span><span class="sxs-lookup"><span data-stu-id="57314-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="57314-376">Achten Sie darauf, die stdout-Protokollierung zu deaktivieren, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="57314-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="57314-377">So zeigen Sie die Ablaufverfolgungsprotokolle für Anforderungsfehler (FREB-Protokolle) an:</span><span class="sxs-lookup"><span data-stu-id="57314-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="57314-378">Navigieren Sie zum Blatt **Diagnose und Problembehandlung** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="57314-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="57314-379">Wählen Sie **Failed Request Tracing Logs** (Ablaufverfolgungsprotokolle für Anforderungsfehler) im Bereich **SUPPORTTOOLS** der Seitenleiste aus.</span><span class="sxs-lookup"><span data-stu-id="57314-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="57314-380">Weitere Informationen finden Sie im [Abschnitt „Failed request traces“ im Thema „Enable diagnostics logging for apps in Azure App Service“](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) und unter [Häufig gestellte Fragen zur Anwendungsleistung von Web-Apps in Azure: Wie schalte ich die Ablaufverfolgung für Anforderungsfehler ein?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="57314-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="57314-381">[Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="57314-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="57314-382">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="57314-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="57314-383">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="57314-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="57314-384">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="57314-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="57314-385">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="57314-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="57314-386">Problembehandlung bei IIS</span><span class="sxs-lookup"><span data-stu-id="57314-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="57314-387">Anwendungs Ereignisprotokoll (IIS)</span><span class="sxs-lookup"><span data-stu-id="57314-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="57314-388">Greifen Sie auf das Anwendungsereignisprotokoll zu:</span><span class="sxs-lookup"><span data-stu-id="57314-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="57314-389">Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="57314-389">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="57314-390">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="57314-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="57314-391">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="57314-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="57314-392">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="57314-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="57314-393">Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.</span><span class="sxs-lookup"><span data-stu-id="57314-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="57314-394">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="57314-394">Run the app at a command prompt</span></span>

<span data-ttu-id="57314-395">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="57314-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="57314-396">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="57314-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="57314-397">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="57314-397">Framework-dependent deployment</span></span>

<span data-ttu-id="57314-398">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="57314-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="57314-399">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen.</span><span class="sxs-lookup"><span data-stu-id="57314-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="57314-400">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="57314-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="57314-401">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="57314-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="57314-402">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="57314-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="57314-403">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="57314-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="57314-404">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="57314-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="57314-405">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="57314-405">Self-contained deployment</span></span>

<span data-ttu-id="57314-406">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="57314-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="57314-407">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus.</span><span class="sxs-lookup"><span data-stu-id="57314-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="57314-408">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="57314-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="57314-409">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="57314-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="57314-410">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="57314-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="57314-411">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="57314-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="57314-412">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="57314-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="57314-413">ASP.net Core Modul stdout-Protokoll (IIS)</span><span class="sxs-lookup"><span data-stu-id="57314-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="57314-414">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="57314-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="57314-415">Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="57314-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="57314-416">Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="57314-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="57314-417">Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="57314-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="57314-418">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-418">Edit the *web.config* file.</span></span> <span data-ttu-id="57314-419">Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="57314-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="57314-420">`stdout` im Pfad ist das Präfix des Protokolldateinamens.</span><span class="sxs-lookup"><span data-stu-id="57314-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="57314-421">Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="57314-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="57314-422">Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.</span><span class="sxs-lookup"><span data-stu-id="57314-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="57314-423">Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.</span><span class="sxs-lookup"><span data-stu-id="57314-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="57314-424">Speichern Sie die aktualisierte Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="57314-425">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="57314-425">Make a request to the app.</span></span>
1. <span data-ttu-id="57314-426">Navigieren Sie zu dem Ordner *logs*.</span><span class="sxs-lookup"><span data-stu-id="57314-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="57314-427">Suchen und öffnen Sie das aktuelle stdout-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="57314-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="57314-428">Untersuchen Sie das Protokoll auf Fehler.</span><span class="sxs-lookup"><span data-stu-id="57314-428">Study the log for errors.</span></span>

<span data-ttu-id="57314-429">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="57314-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="57314-430">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="57314-431">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="57314-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="57314-432">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="57314-432">Save the file.</span></span>

<span data-ttu-id="57314-433">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="57314-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="57314-434">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="57314-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="57314-435">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="57314-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="57314-436">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="57314-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="57314-437">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="57314-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="57314-438">Debugprotokoll des ASP.net Core Moduls (IIS)</span><span class="sxs-lookup"><span data-stu-id="57314-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="57314-439">Fügen Sie der Datei " *Web. config* " der APP die folgenden Handlereinstellungen hinzu, um ASP.net Core moduldebugprotokoll zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="57314-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="57314-440">Überprüfen Sie, ob der für das Protokoll angegebene Pfad vorhanden ist, und ob die Identität des Anwendungspools Schreibberechtigungen für den Speicherort hat.</span><span class="sxs-lookup"><span data-stu-id="57314-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="57314-441">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="57314-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="57314-442">Aktivieren der Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="57314-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="57314-443">Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="57314-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="57314-444">Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="57314-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="57314-445">Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="57314-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="57314-446">Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="57314-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="57314-447">Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="57314-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="57314-448">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="57314-448">Obtain data from an app</span></span>

<span data-ttu-id="57314-449">Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals.</span><span class="sxs-lookup"><span data-stu-id="57314-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="57314-450">Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="57314-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="57314-451">Langsame oder hängende app (IIS)</span><span class="sxs-lookup"><span data-stu-id="57314-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="57314-452">Ein *Absturz* Abbild ist eine Momentaufnahme des System Speichers und kann helfen, die Ursache für einen Absturz der APP, einen Start Fehler oder eine langsame APP zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="57314-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="57314-453">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="57314-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="57314-454">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="57314-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="57314-455">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="57314-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="57314-456">Der App-Pool muss über Schreibzugriff auf den Ordner verfügen.</span><span class="sxs-lookup"><span data-stu-id="57314-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="57314-457">Führen Sie das [PowerShell-Skript „EnableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="57314-457">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="57314-458">Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="57314-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="57314-459">Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="57314-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="57314-460">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="57314-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="57314-461">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="57314-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="57314-462">Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="57314-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="57314-463">Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="57314-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="57314-464">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="57314-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="57314-465">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="57314-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="57314-466">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="57314-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="57314-467">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="57314-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="57314-468">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="57314-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="57314-469">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="57314-469">Analyze the dump</span></span>

<span data-ttu-id="57314-470">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="57314-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="57314-471">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="57314-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="57314-472">Löschen von Paket Caches</span><span class="sxs-lookup"><span data-stu-id="57314-472">Clear package caches</span></span>

<span data-ttu-id="57314-473">In manchen Fällen tritt ein funktionierender App direkt nach dem Upgrade der .net Core SDK auf dem Entwicklungs Computer oder dem Ändern der Paketversionen in der APP auf.</span><span class="sxs-lookup"><span data-stu-id="57314-473">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="57314-474">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57314-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="57314-475">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="57314-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="57314-476">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="57314-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="57314-477">Löschen Sie die Paket Caches, `dotnet nuget locals all --clear` indem Sie von einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="57314-477">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="57314-478">Das Löschen von Paket Caches kann auch mit dem [nuget. exe](https://www.nuget.org/downloads) -Tool und dem Ausführen `nuget locals all -clear`des Befehls erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="57314-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="57314-479">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="57314-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="57314-480">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="57314-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="57314-481">Löschen Sie alle Dateien im Bereitstellungs Ordner auf dem Server, bevor Sie die APP erneut bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="57314-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57314-482">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="57314-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="57314-483">Azure-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="57314-483">Azure documentation</span></span>

* [<span data-ttu-id="57314-484">Application Insights für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57314-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="57314-485">Abschnitt zum Remote Debuggen von Web-Apps unter Problembehandlung für eine Web-App in Azure App Service mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57314-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="57314-486">Übersicht: Azure App Service-Diagnose</span><span class="sxs-lookup"><span data-stu-id="57314-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="57314-487">Vorgehensweise: Überwachen von Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57314-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="57314-488">Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57314-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="57314-489">Problembehandlung bei HTTP-Fehler „502 Ungültiges Gateway“ und „503 Dienst nicht verfügbar“ in Ihren Azure-Web-Apps</span><span class="sxs-lookup"><span data-stu-id="57314-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="57314-490">Problembehandlung von Leistungsproblemen in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57314-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="57314-491">FAQ zur Anwendungsleistung von Web-Apps in Azure</span><span class="sxs-lookup"><span data-stu-id="57314-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="57314-492">Einschränkungen der App Service-Laufzeiteinschränkung</span><span class="sxs-lookup"><span data-stu-id="57314-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="57314-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)</span><span class="sxs-lookup"><span data-stu-id="57314-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="57314-494">Visual Studio-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="57314-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="57314-495">Remote debugASP.net Core auf IIS in Azure in Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="57314-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="57314-496">Remote Debuggen ASP.net Core auf einem IIS-Remote Computer in Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="57314-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="57314-497">Lernen Sie das Debuggen mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57314-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="57314-498">Dokumentation zu Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57314-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="57314-499">Debuggen mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57314-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
