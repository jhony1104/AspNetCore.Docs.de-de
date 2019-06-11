---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: e4c93459f2030c7c0a55ea90e0cc8c8d30b76c51
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470459"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="fb00e-103">Problembehandlung bei ASP.NET Core in IIS</span><span class="sxs-lookup"><span data-stu-id="fb00e-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="fb00e-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fb00e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fb00e-105">Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="fb00e-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="fb00e-106">Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="fb00e-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fb00e-107">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="fb00e-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fb00e-108">Ein *502.5: Prozessfehler* oder ein *500.30: Startfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fb00e-109">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="fb00e-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="fb00e-110">Ein *502.5: Prozessfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="fb00e-111">Weitere Themen zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="fb00e-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="fb00e-112"><xref:host-and-deploy/azure-apps/troubleshoot> Obwohl App Service das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und IIS für das Hosten von Apps verwendet, finden Sie im entsprechenden Artikel weitere, für App Service spezifische Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="fb00e-113"><xref:fundamentals/error-handling> In diesem Artikel erfahren Sie, wie Sie Fehler in ASP.NET Core-Apps in der Entwicklungsphase auf einem lokalen System behandeln.</span><span class="sxs-lookup"><span data-stu-id="fb00e-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="fb00e-114">[Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="fb00e-115">[Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) In diesem Artikel erfahren Sie, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="fb00e-116">App-Startfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="fb00e-117">502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-117">502.5 Process Failure</span></span>

<span data-ttu-id="fb00e-118">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-118">The worker process fails.</span></span> <span data-ttu-id="fb00e-119">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-119">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-120">Das ASP.NET Core-Modul kann den .NET-Back-End-Prozess nicht starten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="fb00e-121">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="fb00e-122">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fb00e-123">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="fb00e-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="fb00e-124">Das *freigegebene Framework* ist der Satz der Assemblys (*DLL*-Dateien), die auf dem Computer installiert werden und auf die ein Metapaket wie `Microsoft.AspNetCore.App` verweist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="fb00e-125">Der Metapaketverweis kann eine mindestens erforderliche Version angeben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="fb00e-126">Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).</span><span class="sxs-lookup"><span data-stu-id="fb00e-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="fb00e-127">Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn ein falsch konfiguriertes Hosting oder eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="fb00e-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Browserfenster mit der Seite „502.5: Prozessfehler“](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="fb00e-129">500.30: Prozessinterner Startupfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="fb00e-130">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-130">The worker process fails.</span></span> <span data-ttu-id="fb00e-131">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-131">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-132">Das ASP.NET Core-Modul kann den .NET Core-CLR-In-Process nicht starten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="fb00e-133">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="fb00e-134">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="fb00e-135">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="fb00e-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fb00e-136">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="fb00e-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fb00e-137">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-137">The worker process fails.</span></span> <span data-ttu-id="fb00e-138">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-138">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-139">Das ASP.NET Core-Modul kann die .NET Core-CLR und den In-Process-Anforderungshandler (*aspnetcorev2_inprocess.dll*) nicht finden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="fb00e-140">Überprüfen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fb00e-140">Check that:</span></span>

* <span data-ttu-id="fb00e-141">Diese App legt entweder das NuGet-Paket [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) oder das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="fb00e-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="fb00e-142">Die Version des freigegebenen ASP.NET Core-Frameworks, die von der App als Ziel festgelegt ist, ist auf dem Zielcomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="fb00e-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="fb00e-143">500.0: Fehler beim Laden des prozessexternen Handlers</span><span class="sxs-lookup"><span data-stu-id="fb00e-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fb00e-144">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-144">The worker process fails.</span></span> <span data-ttu-id="fb00e-145">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-145">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-146">Das ASP.NET Core-Modul kann den Out-of-Process-Hostinganforderungshandler nicht finden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="fb00e-147">Stellen Sie sicher, dass sich *aspnetcorev2_outofprocess.dll* in einem Unterordner mit *aspnetcorev2.dll* befindet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="fb00e-148">500.31: Fehler bei der Suche nach nativen Abhängigkeiten in ANCM</span><span class="sxs-lookup"><span data-stu-id="fb00e-148">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="fb00e-149">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-149">The worker process fails.</span></span> <span data-ttu-id="fb00e-150">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-150">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-151">Das ASP.NET Core-Modul kann die prozessinterne .NET Core-Runtime nicht starten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-151">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="fb00e-152">Die häufigste Ursache dieses Startfehlers ist die fehlende Installation der Laufzeiten `Microsoft.NETCore.App` oder `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="fb00e-152">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="fb00e-153">Dieser Fehler tritt auf, wenn die App für die Zielversion 3.0 von ASP.NET Core bereitgestellt wurde, die aber auf dem Computer nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-153">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="fb00e-154">Es wird beispielsweise folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fb00e-154">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="fb00e-155">In der Fehlermeldung werden alle installierten .NET Core-Versionen aufgelistet sowie die für die App erforderliche Version.</span><span class="sxs-lookup"><span data-stu-id="fb00e-155">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="fb00e-156">Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:</span><span class="sxs-lookup"><span data-stu-id="fb00e-156">To fix this error, either:</span></span>

* <span data-ttu-id="fb00e-157">Installieren Sie die entsprechende .NET Core-Version auf Ihrem Computer.</span><span class="sxs-lookup"><span data-stu-id="fb00e-157">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="fb00e-158">Ändern Sie die Zielversion der App in eine .NET Core-Version, die auf Ihrem Computer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-158">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="fb00e-159">Veröffentlichen Sie die App als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fb00e-159">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="fb00e-160">Beim Ausführen im Entwicklungsmodus (wenn die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist) wird der jeweilige Fehler in die HTTP-Antwort geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-160">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="fb00e-161">Die Ursache für einen Fehler beim Starten eines Prozesses kann auch über das [Anwendungsereignisprotokoll](#application-event-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-161">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="fb00e-162">500.32: Fehler beim Laden der DLL in ANCM</span><span class="sxs-lookup"><span data-stu-id="fb00e-162">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="fb00e-163">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-163">The worker process fails.</span></span> <span data-ttu-id="fb00e-164">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-164">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-165">Die häufigste Ursache für diesen Fehler ist die Veröffentlichung der App für eine nicht kompatible Prozessorarchitektur.</span><span class="sxs-lookup"><span data-stu-id="fb00e-165">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="fb00e-166">Der Fehler tritt auf, wenn der Arbeitsprozess als 32-Bit-App ausgeführt wird, die App aber für eine 64-Bit-Ausführung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="fb00e-166">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="fb00e-167">Sie haben folgende Möglichkeiten, um diesen Fehler zu beheben:</span><span class="sxs-lookup"><span data-stu-id="fb00e-167">To fix this error, either:</span></span>

* <span data-ttu-id="fb00e-168">Veröffentlichen Sie die App erneut, sodass Prozessorarchitektur und Arbeitsprozess übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-168">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="fb00e-169">Veröffentlichen Sie die App als eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="fb00e-169">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="fb00e-170">500.33: Fehler beim Laden des Anforderungshandlers in ANCM</span><span class="sxs-lookup"><span data-stu-id="fb00e-170">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="fb00e-171">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-171">The worker process fails.</span></span> <span data-ttu-id="fb00e-172">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-172">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-173">Die App verweist nicht auf das Framework `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="fb00e-173">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fb00e-174">Das ASP.NET Core-Modul hostet nur Apps mit `Microsoft.AspNetCore.App` als Zielframework.</span><span class="sxs-lookup"><span data-stu-id="fb00e-174">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="fb00e-175">Vergewissern Sie sich, dass das Zielframework der App `Microsoft.AspNetCore.App` ist, um den Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-175">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="fb00e-176">Überprüfen Sie `.runtimeconfig.json`, um das Zielframework der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-176">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="fb00e-177">500.34: Gemischte Hostingmodelle in ANCM nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="fb00e-177">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="fb00e-178">Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-178">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="fb00e-179">Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-179">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="fb00e-180">500.35: Mehrere In-Process-Anwendungen im selben Prozess in ANCM</span><span class="sxs-lookup"><span data-stu-id="fb00e-180">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="fb00e-181">Im selben Arbeitsprozess können nicht gleichzeitig eine In-Process- und eine Out-of-Process-App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-181">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="fb00e-182">Führen Sie die Apps in separaten IIS-Anwendungspools aus, um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-182">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="fb00e-183">500.36: Fehler beim Laden des Out-of-Process-Handlers in ANCM</span><span class="sxs-lookup"><span data-stu-id="fb00e-183">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="fb00e-184">Der Out-of-Process-Anforderungshandler *aspnetcorev2_outofprocess.dll* befindet sich nicht neben der Datei *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-184">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="fb00e-185">Dies deutet auf eine fehlerhafte Installation des ASP.NET Core-Moduls hin.</span><span class="sxs-lookup"><span data-stu-id="fb00e-185">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="fb00e-186">Reparieren Sie die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (für IIS) oder von Visual Studio (für IIS Express), um diesen Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-186">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="fb00e-187">500.37: Fehler beim Starten von ANCM innerhalb des Zeitlimits für den Start</span><span class="sxs-lookup"><span data-stu-id="fb00e-187">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="fb00e-188">ANCM konnte nicht innerhalb des angegebenen Zeitlimits für den Start gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-188">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="fb00e-189">Das Standardzeitlimit beträgt 120 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-189">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="fb00e-190">Dieser Fehler kann beim Starten einer großen Anzahl von Apps auf dem gleichen Computer auftreten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-190">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="fb00e-191">Überprüfen Sie, ob beim Starten CPU-/Speicherauslastungsspitzen auf dem Server vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="fb00e-191">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="fb00e-192">In diesem Fall sollten Sie möglicherweise den Startvorgang einiger Apps staffeln.</span><span class="sxs-lookup"><span data-stu-id="fb00e-192">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="fb00e-193">500.30: Prozessinterner Startupfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-193">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="fb00e-194">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-194">The worker process fails.</span></span> <span data-ttu-id="fb00e-195">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-195">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-196">Das ASP.NET Core-Modul kann die prozessinterne .NET Core-Runtime nicht starten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-196">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="fb00e-197">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-197">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="fb00e-198">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="fb00e-198">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="fb00e-199">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-199">The worker process fails.</span></span> <span data-ttu-id="fb00e-200">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="fb00e-200">The app doesn't start.</span></span>

<span data-ttu-id="fb00e-201">Beim Laden von Komponenten des ASP.NET Core-Moduls ist ein unbekannter Fehler aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-201">An unknown error occurred loading ASP.NET Core Module components.</span></span> <span data-ttu-id="fb00e-202">Führen Sie eine der folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-202">Take one of the following actions:</span></span>

* <span data-ttu-id="fb00e-203">Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (über **Entwicklertools** > **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="fb00e-203">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="fb00e-204">Stellen Sie Ihre Frage auf Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="fb00e-204">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="fb00e-205">Melden Sie das Problem im [GitHub-Repository](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fb00e-205">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="fb00e-206">500: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-206">500 Internal Server Error</span></span>

<span data-ttu-id="fb00e-207">Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.</span><span class="sxs-lookup"><span data-stu-id="fb00e-207">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="fb00e-208">Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf.</span><span class="sxs-lookup"><span data-stu-id="fb00e-208">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="fb00e-209">Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-209">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="fb00e-210">Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="fb00e-210">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="fb00e-211">Aus Sicht des Servers ist dies richtig.</span><span class="sxs-lookup"><span data-stu-id="fb00e-211">From the server's perspective, that's correct.</span></span> <span data-ttu-id="fb00e-212">Die App wurde gestartet, sie kann jedoch keine gültige Antwort generieren.</span><span class="sxs-lookup"><span data-stu-id="fb00e-212">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="fb00e-213">[Führen Sie die App in einer Eingabeaufforderung](#run-the-app-at-a-command-prompt) auf dem Server aus oder [aktivieren Sie das stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log), um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-213">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="fb00e-214">Fehler beim Starten der Anwendung (ErrorCode 0x800700c1)</span><span class="sxs-lookup"><span data-stu-id="fb00e-214">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="fb00e-215">Die App konnte nicht gestartet werden, weil die Assembly der App ( *.dll*) nicht geladen werden konnte.</span><span class="sxs-lookup"><span data-stu-id="fb00e-215">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="fb00e-216">Dieser Fehler tritt bei einem Bitanzahlkonflikt zwischen der veröffentlichten App und dem w3wp/iisexpress-Prozess auf.</span><span class="sxs-lookup"><span data-stu-id="fb00e-216">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="fb00e-217">Überprüfen Sie, ob die 32-Bit-Einstellung des Anwendungspools korrekt ist:</span><span class="sxs-lookup"><span data-stu-id="fb00e-217">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="fb00e-218">Wählen Sie im IIS-Manager unter **Anwendungspools** den Anwendungspool aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-218">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="fb00e-219">Wählen Sie im Bereich **Aktionen** unter **Anwendungspool bearbeiten** **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-219">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="fb00e-220">Wählen Sie **32-Bit-Anwendungen aktivieren** aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-220">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="fb00e-221">Wenn Sie eine 32-Bit-(x86)-App bereitstellen, legen Sie den Wert auf `True` fest.</span><span class="sxs-lookup"><span data-stu-id="fb00e-221">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="fb00e-222">Wenn Sie eine 64-Bit-(x64)-App bereitstellen, legen Sie den Wert auf `False` fest.</span><span class="sxs-lookup"><span data-stu-id="fb00e-222">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="fb00e-223">Verbindungszurücksetzung</span><span class="sxs-lookup"><span data-stu-id="fb00e-223">Connection reset</span></span>

<span data-ttu-id="fb00e-224">Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-224">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="fb00e-225">Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-225">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="fb00e-226">Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-226">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="fb00e-227">Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-227">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="fb00e-228">Standardstarteinschränkungen</span><span class="sxs-lookup"><span data-stu-id="fb00e-228">Default startup limits</span></span>

<span data-ttu-id="fb00e-229">Das ASP.NET Core-Modul wurde mit dem Standardwert 120 Sekunden für das *StartupTimeLimit* konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="fb00e-229">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="fb00e-230">Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren.</span><span class="sxs-lookup"><span data-stu-id="fb00e-230">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="fb00e-231">Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="fb00e-231">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="fb00e-232">Problembehandlung bei App-Startfehlern</span><span class="sxs-lookup"><span data-stu-id="fb00e-232">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="fb00e-233">Aktivieren des Debugprotokolls des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="fb00e-233">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="fb00e-234">Fügen Sie der *web.config*-Datei der App die folgenden Handlereinstellungen hinzu, um Debugprotokolle des ASP.NET Core-Moduls zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="fb00e-234">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fb00e-235">Überprüfen Sie, ob der für das Protokoll angegebene Pfad vorhanden ist, und ob die Identität des Anwendungspools Schreibberechtigungen für den Speicherort hat.</span><span class="sxs-lookup"><span data-stu-id="fb00e-235">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="fb00e-236">Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="fb00e-236">Application Event Log</span></span>

<span data-ttu-id="fb00e-237">Greifen Sie auf das Anwendungsereignisprotokoll zu:</span><span class="sxs-lookup"><span data-stu-id="fb00e-237">Access the Application Event Log:</span></span>

1. <span data-ttu-id="fb00e-238">Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-238">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="fb00e-239">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="fb00e-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="fb00e-240">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-240">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="fb00e-241">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-241">Search for errors associated with the failing app.</span></span> <span data-ttu-id="fb00e-242">Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.</span><span class="sxs-lookup"><span data-stu-id="fb00e-242">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="fb00e-243">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="fb00e-243">Run the app at a command prompt</span></span>

<span data-ttu-id="fb00e-244">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="fb00e-244">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="fb00e-245">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="fb00e-246">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="fb00e-246">Framework-dependent deployment</span></span>

<span data-ttu-id="fb00e-247">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="fb00e-247">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="fb00e-248">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-248">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="fb00e-249">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="fb00e-249">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="fb00e-250">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-250">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fb00e-251">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-251">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fb00e-252">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-252">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fb00e-253">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="fb00e-253">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="fb00e-254">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="fb00e-254">Self-contained deployment</span></span>

<span data-ttu-id="fb00e-255">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="fb00e-255">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="fb00e-256">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-256">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="fb00e-257">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="fb00e-257">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="fb00e-258">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fb00e-258">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="fb00e-259">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-259">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="fb00e-260">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-260">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="fb00e-261">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="fb00e-261">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="fb00e-262">stdout-Protokoll des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="fb00e-262">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="fb00e-263">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="fb00e-263">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="fb00e-264">Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="fb00e-264">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="fb00e-265">Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-265">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="fb00e-266">Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="fb00e-266">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="fb00e-267">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-267">Edit the *web.config* file.</span></span> <span data-ttu-id="fb00e-268">Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="fb00e-268">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="fb00e-269">`stdout` im Pfad ist das Präfix des Protokolldateinamens.</span><span class="sxs-lookup"><span data-stu-id="fb00e-269">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="fb00e-270">Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-270">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="fb00e-271">Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-271">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="fb00e-272">Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-272">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="fb00e-273">Speichern Sie die aktualisierte Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-273">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="fb00e-274">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="fb00e-274">Make a request to the app.</span></span>
1. <span data-ttu-id="fb00e-275">Navigieren Sie zu dem Ordner *logs*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-275">Navigate to the *logs* folder.</span></span> <span data-ttu-id="fb00e-276">Suchen und öffnen Sie das aktuelle stdout-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="fb00e-276">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="fb00e-277">Untersuchen Sie das Protokoll auf Fehler.</span><span class="sxs-lookup"><span data-stu-id="fb00e-277">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fb00e-278">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="fb00e-278">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="fb00e-279">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-279">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="fb00e-280">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="fb00e-280">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="fb00e-281">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="fb00e-281">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="fb00e-282">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="fb00e-282">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="fb00e-283">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-283">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="fb00e-284">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="fb00e-284">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fb00e-285">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="fb00e-285">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="fb00e-286">Aktivieren der Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="fb00e-286">Enable the Developer Exception Page</span></span>

<span data-ttu-id="fb00e-287">Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-287">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="fb00e-288">Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fb00e-288">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="fb00e-289">Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-289">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="fb00e-290">Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-290">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="fb00e-291">Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="fb00e-291">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="fb00e-292">Häufige Startfehler</span><span class="sxs-lookup"><span data-stu-id="fb00e-292">Common startup errors</span></span>

<span data-ttu-id="fb00e-293">Siehe <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="fb00e-293">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="fb00e-294">Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-294">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="fb00e-295">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="fb00e-295">Obtain data from an app</span></span>

<span data-ttu-id="fb00e-296">Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals.</span><span class="sxs-lookup"><span data-stu-id="fb00e-296">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="fb00e-297">Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="fb00e-297">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="fb00e-298">Erstellen eines Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="fb00e-298">Create a dump</span></span>

<span data-ttu-id="fb00e-299">Ein *Speicherabbild* ist eine Momentaufnahme des Systemarbeitsspeichers. Ein Speicherabbild kann hilfreich sein, um die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="fb00e-299">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="fb00e-300">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="fb00e-300">App crashes or encounters an exception</span></span>

<span data-ttu-id="fb00e-301">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="fb00e-301">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="fb00e-302">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="fb00e-302">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="fb00e-303">Der App-Pool muss über Schreibzugriff auf den Ordner verfügen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-303">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="fb00e-304">Führen Sie das [PowerShell-Skript „EnableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-304">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="fb00e-305">Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-305">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="fb00e-306">Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-306">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="fb00e-307">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="fb00e-307">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="fb00e-308">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-308">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="fb00e-309">Wenn die App das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-309">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="fb00e-310">Wenn die App das [Out-of-Process-Hostingmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="fb00e-310">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="fb00e-311">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-311">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="fb00e-312">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="fb00e-312">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="fb00e-313">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="fb00e-313">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="fb00e-314">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="fb00e-314">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="fb00e-315">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-315">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="fb00e-316">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="fb00e-316">Analyze the dump</span></span>

<span data-ttu-id="fb00e-317">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-317">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="fb00e-318">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="fb00e-318">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="fb00e-319">Remotedebuggen</span><span class="sxs-lookup"><span data-stu-id="fb00e-319">Remote debugging</span></span>

<span data-ttu-id="fb00e-320">Siehe [Remotedebuggen von ASP.NET Core auf einem IIS-Remotecomputer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in der Dokumentation zu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fb00e-320">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="fb00e-321">Application Insights</span><span class="sxs-lookup"><span data-stu-id="fb00e-321">Application Insights</span></span>

<span data-ttu-id="fb00e-322">[Application Insights](/azure/application-insights/) stellt Telemetriedaten von Apps bereit, die von IIS gehostet werden, einschließlich Features für die Fehlerprotokollierung und die Berichterstellung.</span><span class="sxs-lookup"><span data-stu-id="fb00e-322">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="fb00e-323">Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-323">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="fb00e-324">Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="fb00e-324">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="fb00e-325">Zusätzliche Hinweise</span><span class="sxs-lookup"><span data-stu-id="fb00e-325">Additional advice</span></span>

<span data-ttu-id="fb00e-326">Manchmal schlägt eine funktionsfähige App direkt nach der Durchführung eines Upgrades des .NET Core SDK auf dem Entwicklungscomputer oder der Paketversionen in der App fehl.</span><span class="sxs-lookup"><span data-stu-id="fb00e-326">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="fb00e-327">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-327">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="fb00e-328">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="fb00e-328">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="fb00e-329">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-329">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="fb00e-330">Löschen Sie die Paketcaches unter *%UserProfile%\\.nuget\\packages* und *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="fb00e-330">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="fb00e-331">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="fb00e-331">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="fb00e-332">Überprüfen Sie, ob die vorherige Bereitstellung auf dem Server vollständig gelöscht wurde, bevor Sie die App erneut bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-332">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="fb00e-333">Sie können Paketcaches ganz einfach löschen, indem Sie `dotnet nuget locals all --clear` über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="fb00e-333">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="fb00e-334">Darüber hinaus können Paketcaches mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und durch Ausführung des Befehls `nuget locals all -clear` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-334">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="fb00e-335">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="fb00e-335">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb00e-336">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fb00e-336">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
