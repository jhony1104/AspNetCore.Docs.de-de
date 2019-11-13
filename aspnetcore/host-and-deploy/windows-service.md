---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659844"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="7e397-103">Hosten von ASP.NET Core in einem Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="7e397-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="7e397-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7e397-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7e397-105">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="7e397-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="7e397-106">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="7e397-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="7e397-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7e397-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e397-108">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="7e397-108">Prerequisites</span></span>

* [<span data-ttu-id="7e397-109">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="7e397-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="7e397-110">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="7e397-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="7e397-111">Workerdienstvorlage</span><span class="sxs-lookup"><span data-stu-id="7e397-111">Worker Service template</span></span>

<span data-ttu-id="7e397-112">Die ASP.NET Core-Workerdienstvorlage dient als Ausgangspunkt für das Programmieren von zeitintensiven Dienstanwendungen.</span><span class="sxs-lookup"><span data-stu-id="7e397-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="7e397-113">So können Sie diese Vorlage als Grundlage für eine Windows-Dienstanwendung verwenden:</span><span class="sxs-lookup"><span data-stu-id="7e397-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="7e397-114">Erstellen Sie eine Workerdienstanwendung über die .NET Core-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="7e397-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="7e397-115">Befolgen Sie die Anweisungen im Abschnitt [App-Konfiguration](#app-configuration), um die Workerdienstanwendung so zu aktualisieren, dass sie als Windows-Dienst ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e397-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="7e397-116">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="7e397-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e397-117">Die App erfordert einen Paketverweis auf [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="7e397-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="7e397-118">Beim Erstellen des Hosts wird `IHostBuilder.UseWindowsService` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7e397-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="7e397-119">Wenn die App als Windows-Dienst ausgeführt wird, sorgt die Methode für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7e397-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="7e397-120">Sie legt die Lebensdauer des Hosts auf `WindowsServiceLifetime` fest.</span><span class="sxs-lookup"><span data-stu-id="7e397-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="7e397-121">Sie legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) fest.</span><span class="sxs-lookup"><span data-stu-id="7e397-121">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="7e397-122">Sie aktiviert die Protokollierung im Ereignisprotokoll mit dem Anwendungsnamen als Standardquellennamen.</span><span class="sxs-lookup"><span data-stu-id="7e397-122">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="7e397-123">Die Protokollebene kann mithilfe des Schlüssels `Logging:LogLevel:Default` in der Datei *appsettings.Production.json* konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e397-123">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="7e397-124">Nur Administratoren können neue Ereignisquellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e397-124">Only administrators can create new event sources.</span></span> <span data-ttu-id="7e397-125">Wenn keine Ereignisquellen mithilfe des Anwendungsnamens erstellt werden können, wird in der *Anwendungsquelle* eine Warnung protokolliert, und die Ereignisprotokolle werden deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e397-125">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="7e397-126">In `CreateHostBuilder` von *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e397-126">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="7e397-127">In diesem Thema werden die folgenden Beispiel-Apps behandelt:</span><span class="sxs-lookup"><span data-stu-id="7e397-127">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="7e397-128">Beispiel eines Hintergrundworkerdiensts &ndash; Ein Beispiel einer nicht webbasierten App, die auf der [Workerdienstvorlage](#worker-service-template) basiert, die [gehostete Dienste](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e397-128">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="7e397-129">Beispiel eines Web-App-Diensts &ndash; Ein Razor Pages-Web-App-Beispiel, das als Windows-Dienst mit [gehosteten Diensten](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-129">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="7e397-130">MVC-Anleitungen finden Sie in den Artikeln unter <xref:mvc/overview> und <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="7e397-130">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e397-131">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="7e397-131">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="7e397-132">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-132">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="7e397-133">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="7e397-133">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="7e397-134">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="7e397-134">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="7e397-135">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="7e397-135">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="7e397-136">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="7e397-136">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="7e397-137">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-137">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="7e397-138">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="7e397-138">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="7e397-139">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-139">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="7e397-140">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7e397-140">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="7e397-141">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e397-141">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="7e397-142">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="7e397-142">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="7e397-143">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="7e397-143">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="7e397-144">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="7e397-144">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="7e397-145">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="7e397-145">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="7e397-146">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="7e397-146">Deployment type</span></span>

<span data-ttu-id="7e397-147">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="7e397-147">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="7e397-148">SDK</span><span class="sxs-lookup"><span data-stu-id="7e397-148">SDK</span></span>

<span data-ttu-id="7e397-149">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="7e397-149">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7e397-150">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="7e397-150">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="7e397-151">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="7e397-151">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="7e397-152">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="7e397-152">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="7e397-153">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7e397-153">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e397-154">Bei Verwendung des [Web-SDK](#sdk) ist eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7e397-154">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7e397-155">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="7e397-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7e397-156">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="7e397-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="7e397-157">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7e397-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="7e397-158">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7e397-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="7e397-159">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="7e397-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="7e397-160">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7e397-160">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7e397-161">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="7e397-161">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7e397-162">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="7e397-162">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="7e397-163">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7e397-163">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="7e397-164">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7e397-164">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="7e397-165">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="7e397-165">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="7e397-166">Die `<UseAppHost>`-Eigenschaft ist auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7e397-166">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="7e397-167">Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="7e397-167">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="7e397-168">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7e397-168">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7e397-169">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="7e397-169">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="7e397-170">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="7e397-170">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="7e397-171">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="7e397-171">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="7e397-172">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7e397-172">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="7e397-173">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="7e397-173">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="7e397-174">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="7e397-174">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="7e397-175">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="7e397-175">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="7e397-176">Verwenden Sie den Eigenschaftsnamen [\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="7e397-176">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="7e397-177">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7e397-177">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e397-178">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7e397-178">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="7e397-179">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="7e397-179">Service user account</span></span>

<span data-ttu-id="7e397-180">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="7e397-180">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="7e397-181">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="7e397-181">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="7e397-182">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="7e397-182">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="7e397-183">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="7e397-183">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="7e397-184">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="7e397-184">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="7e397-185">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="7e397-185">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="7e397-186">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="7e397-186">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="7e397-187">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="7e397-187">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="7e397-188">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="7e397-188">Log on as a service rights</span></span>

<span data-ttu-id="7e397-189">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="7e397-189">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="7e397-190">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="7e397-190">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="7e397-191">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="7e397-191">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="7e397-192">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="7e397-192">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="7e397-193">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="7e397-193">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="7e397-194">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="7e397-194">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="7e397-195">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="7e397-195">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="7e397-196">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="7e397-196">Select **Advanced**.</span></span> <span data-ttu-id="7e397-197">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="7e397-197">Select **Find Now**.</span></span> <span data-ttu-id="7e397-198">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="7e397-198">Select the user account from the list.</span></span> <span data-ttu-id="7e397-199">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="7e397-199">Select **OK**.</span></span> <span data-ttu-id="7e397-200">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="7e397-200">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="7e397-201">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="7e397-201">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="7e397-202">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-202">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="7e397-203">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-203">Create a service</span></span>

<span data-ttu-id="7e397-204">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="7e397-204">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="7e397-205">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="7e397-205">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="7e397-206">`{EXE PATH}`: Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="7e397-206">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="7e397-207">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="7e397-207">Don't include the app's executable in the path.</span></span> <span data-ttu-id="7e397-208">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="7e397-208">A trailing slash isn't required.</span></span>
* <span data-ttu-id="7e397-209">`{DOMAIN OR COMPUTER NAME\USER}`: Benutzerkonto des Diensts (z. B. `Contoso\ServiceUser`)</span><span class="sxs-lookup"><span data-stu-id="7e397-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="7e397-210">`{NAME}`: Dienstname (z. B. `MyService`)</span><span class="sxs-lookup"><span data-stu-id="7e397-210">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="7e397-211">`{EXE FILE PATH}`: der Pfad der ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="7e397-211">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="7e397-212">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="7e397-212">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="7e397-213">`{DESCRIPTION}`: Dienstbeschreibung (z. B. `My sample service`)</span><span class="sxs-lookup"><span data-stu-id="7e397-213">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="7e397-214">`{DISPLAY NAME}`: Anzeigename des Diensts (z. B. `My Service`)</span><span class="sxs-lookup"><span data-stu-id="7e397-214">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="7e397-215">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-215">Start a service</span></span>

<span data-ttu-id="7e397-216">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="7e397-216">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="7e397-217">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7e397-217">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="7e397-218">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-218">Determine a service's status</span></span>

<span data-ttu-id="7e397-219">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="7e397-219">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="7e397-220">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="7e397-220">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="7e397-221">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-221">Stop a service</span></span>

<span data-ttu-id="7e397-222">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="7e397-222">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="7e397-223">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="7e397-223">Remove a service</span></span>

<span data-ttu-id="7e397-224">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="7e397-224">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="7e397-225">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="7e397-225">Handle starting and stopping events</span></span>

<span data-ttu-id="7e397-226">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="7e397-226">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="7e397-227">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="7e397-227">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="7e397-228">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="7e397-228">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="7e397-229">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="7e397-229">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="7e397-230">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="7e397-230">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7e397-231">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="7e397-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7e397-232">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="7e397-232">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="7e397-233">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7e397-233">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="7e397-234">Endpunkte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e397-234">Configure endpoints</span></span>

<span data-ttu-id="7e397-235">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="7e397-235">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7e397-236">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="7e397-236">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="7e397-237">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="7e397-237">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="7e397-238">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="7e397-238">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="7e397-239">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-239">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="7e397-240">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7e397-240">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="7e397-241">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="7e397-241">Current directory and content root</span></span>

<span data-ttu-id="7e397-242">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="7e397-242">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="7e397-243">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="7e397-243">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="7e397-244">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="7e397-244">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="7e397-245">Verwenden von ContentRootPath oder ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="7e397-245">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="7e397-246">Verwenden Sie [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) oder <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>, um die Ressourcen einer App zu finden.</span><span class="sxs-lookup"><span data-stu-id="7e397-246">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="7e397-247">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="7e397-247">Set the content root path to the app's folder</span></span>

<span data-ttu-id="7e397-248"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e397-248">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="7e397-249">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.</span><span class="sxs-lookup"><span data-stu-id="7e397-249">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="7e397-250">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="7e397-250">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="7e397-251">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="7e397-251">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="7e397-252">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="7e397-252">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e397-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e397-253">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7e397-254">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="7e397-254">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="7e397-255">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="7e397-255">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
