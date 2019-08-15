---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 703edff19d82d10415bedb9b92d83709c275100f
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913918"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="bc282-103">Hosten von ASP.NET Core in einem Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="bc282-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="bc282-104">Von [Luke Latham](https://github.com/guardrex) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="bc282-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="bc282-105">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="bc282-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="bc282-106">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="bc282-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="bc282-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc282-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bc282-108">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="bc282-108">Prerequisites</span></span>

* [<span data-ttu-id="bc282-109">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="bc282-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="bc282-110">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="bc282-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="bc282-111">Workerdienstvorlage</span><span class="sxs-lookup"><span data-stu-id="bc282-111">Worker Service template</span></span>

<span data-ttu-id="bc282-112">Die ASP.NET Core-Workerdienstvorlage dient als Ausgangspunkt für das Programmieren von zeitintensiven Dienstanwendungen.</span><span class="sxs-lookup"><span data-stu-id="bc282-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="bc282-113">So können Sie diese Vorlage als Grundlage für eine Windows-Dienstanwendung verwenden:</span><span class="sxs-lookup"><span data-stu-id="bc282-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="bc282-114">Erstellen Sie eine Workerdienstanwendung über die .NET Core-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="bc282-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="bc282-115">Befolgen Sie die Anweisungen im Abschnitt [App-Konfiguration](#app-configuration), um die Workerdienstanwendung so zu aktualisieren, dass sie als Windows-Dienst ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="bc282-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc282-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc282-116">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bc282-117">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc282-117">Create a new project.</span></span>
1. <span data-ttu-id="bc282-118">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="bc282-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bc282-119">Select **Next**.</span></span>
1. <span data-ttu-id="bc282-120">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="bc282-120">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="bc282-121">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-121">Select **Create**.</span></span>
1. <span data-ttu-id="bc282-122">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="bc282-122">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="bc282-123">Wählen Sie die Vorlage **Workerdienst** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-123">Select the **Worker Service** template.</span></span> <span data-ttu-id="bc282-124">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-124">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bc282-125">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bc282-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bc282-126">Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf.</span><span class="sxs-lookup"><span data-stu-id="bc282-126">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="bc282-127">Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorkerService` erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc282-127">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="bc282-128">Der Ordner für die App `ContosoWorkerService` wird automatisch erstellt, wenn der Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-128">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="bc282-129">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="bc282-129">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bc282-130">Die Erweiterung `IHostBuilder.UseWindowsService` ist im Paket [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) enthalten und wird aufgerufen, wenn der Host erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-130">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="bc282-131">Wenn die App als Windows-Dienst ausgeführt wird, sorgt die Methode für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bc282-131">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="bc282-132">Sie legt die Lebensdauer des Hosts auf `WindowsServiceLifetime` fest.</span><span class="sxs-lookup"><span data-stu-id="bc282-132">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="bc282-133">Sie legt den Inhaltsstamm fest.</span><span class="sxs-lookup"><span data-stu-id="bc282-133">Sets the content root.</span></span>
* <span data-ttu-id="bc282-134">Sie aktiviert die Protokollierung im Ereignisprotokoll mit dem Anwendungsnamen als Standardquellennamen.</span><span class="sxs-lookup"><span data-stu-id="bc282-134">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="bc282-135">Die Protokollebene kann mithilfe des Schlüssels `Logging:LogLevel:Default` in der Datei *appsettings.Production.json* konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bc282-135">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="bc282-136">Nur Administratoren können neue Ereignisquellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="bc282-136">Only administrators can create new event sources.</span></span> <span data-ttu-id="bc282-137">Wenn keine Ereignisquellen mithilfe des Anwendungsnamens erstellt werden können, wird in der *Anwendungsquelle* eine Warnung protokolliert, und die Ereignisprotokolle werden deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="bc282-137">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bc282-138">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="bc282-138">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="bc282-139">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-139">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="bc282-140">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="bc282-140">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="bc282-141">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="bc282-141">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="bc282-142">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="bc282-142">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="bc282-143">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="bc282-143">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="bc282-144">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-144">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="bc282-145">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="bc282-145">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="bc282-146">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-146">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="bc282-147">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc282-147">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="bc282-148">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="bc282-148">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="bc282-149">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc282-149">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="bc282-150">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="bc282-150">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="bc282-151">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="bc282-151">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="bc282-152">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="bc282-152">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="bc282-153">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="bc282-153">Deployment type</span></span>

<span data-ttu-id="bc282-154">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="bc282-154">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="bc282-155">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="bc282-155">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="bc282-156">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="bc282-156">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="bc282-157">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bc282-157">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bc282-158">Fügen Sie die folgenden Eigenschaftselemente zu der Projektdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc282-158">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="bc282-159">`<OutputType>`: der Ausgabetyp der App (`Exe` für ausführbare Dateien)</span><span class="sxs-lookup"><span data-stu-id="bc282-159">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="bc282-160">`<LangVersion>`: die C#-Sprachversion (`latest` oder `preview`)</span><span class="sxs-lookup"><span data-stu-id="bc282-160">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="bc282-161">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bc282-161">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bc282-162">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc282-162">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bc282-163">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="bc282-163">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="bc282-164">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bc282-164">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="bc282-165">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bc282-165">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="bc282-166">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="bc282-166">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="bc282-167">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bc282-167">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bc282-168">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc282-168">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="bc282-169">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="bc282-169">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="bc282-170">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bc282-170">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="bc282-171">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bc282-171">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="bc282-172">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="bc282-172">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="bc282-173">Die `<UseAppHost>`-Eigenschaft ist auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bc282-173">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="bc282-174">Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="bc282-174">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="bc282-175">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bc282-175">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bc282-176">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc282-176">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="bc282-177">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="bc282-177">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="bc282-178">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="bc282-178">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="bc282-179">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc282-179">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="bc282-180">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="bc282-180">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="bc282-181">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="bc282-181">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="bc282-182">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="bc282-182">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="bc282-183">Verwenden Sie den Eigenschaftsnamen [\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="bc282-183">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="bc282-184">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="bc282-184">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bc282-185">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="bc282-185">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="bc282-186">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="bc282-186">Service user account</span></span>

<span data-ttu-id="bc282-187">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="bc282-187">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="bc282-188">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="bc282-188">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="bc282-189">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="bc282-189">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="bc282-190">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="bc282-190">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="bc282-191">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="bc282-191">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="bc282-192">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="bc282-192">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="bc282-193">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="bc282-193">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="bc282-194">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="bc282-194">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="bc282-195">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="bc282-195">Log on as a service rights</span></span>

<span data-ttu-id="bc282-196">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="bc282-196">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="bc282-197">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="bc282-197">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="bc282-198">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="bc282-198">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="bc282-199">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="bc282-199">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="bc282-200">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-200">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="bc282-201">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="bc282-201">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="bc282-202">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="bc282-202">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="bc282-203">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-203">Select **Advanced**.</span></span> <span data-ttu-id="bc282-204">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="bc282-204">Select **Find Now**.</span></span> <span data-ttu-id="bc282-205">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="bc282-205">Select the user account from the list.</span></span> <span data-ttu-id="bc282-206">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="bc282-206">Select **OK**.</span></span> <span data-ttu-id="bc282-207">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="bc282-207">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="bc282-208">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="bc282-208">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="bc282-209">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-209">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="bc282-210">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-210">Create a service</span></span>

<span data-ttu-id="bc282-211">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="bc282-211">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="bc282-212">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bc282-212">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="bc282-213">`{EXE PATH}`: Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="bc282-213">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="bc282-214">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="bc282-214">Don't include the app's executable in the path.</span></span> <span data-ttu-id="bc282-215">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="bc282-215">A trailing slash isn't required.</span></span>
* <span data-ttu-id="bc282-216">`{DOMAIN OR COMPUTER NAME\USER}`: Benutzerkonto des Diensts (z. B. `Contoso\ServiceUser`)</span><span class="sxs-lookup"><span data-stu-id="bc282-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="bc282-217">`{NAME}`: Dienstname (z. B. `MyService`)</span><span class="sxs-lookup"><span data-stu-id="bc282-217">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="bc282-218">`{EXE FILE PATH}`: der Pfad der ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="bc282-218">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="bc282-219">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc282-219">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="bc282-220">`{DESCRIPTION}`: Dienstbeschreibung (z. B. `My sample service`)</span><span class="sxs-lookup"><span data-stu-id="bc282-220">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="bc282-221">`{DISPLAY NAME}`: Anzeigename des Diensts (z. B. `My Service`)</span><span class="sxs-lookup"><span data-stu-id="bc282-221">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="bc282-222">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-222">Start a service</span></span>

<span data-ttu-id="bc282-223">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="bc282-223">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="bc282-224">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="bc282-224">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="bc282-225">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-225">Determine a service's status</span></span>

<span data-ttu-id="bc282-226">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="bc282-226">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="bc282-227">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="bc282-227">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="bc282-228">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-228">Stop a service</span></span>

<span data-ttu-id="bc282-229">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="bc282-229">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="bc282-230">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="bc282-230">Remove a service</span></span>

<span data-ttu-id="bc282-231">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="bc282-231">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="bc282-232">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="bc282-232">Handle starting and stopping events</span></span>

<span data-ttu-id="bc282-233">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="bc282-233">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="bc282-234">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="bc282-234">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="bc282-235">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="bc282-235">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="bc282-236">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="bc282-236">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="bc282-237">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="bc282-237">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="bc282-238">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="bc282-238">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="bc282-239">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="bc282-239">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="bc282-240">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="bc282-240">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="bc282-241">HTTPS konfigurieren</span><span class="sxs-lookup"><span data-stu-id="bc282-241">Configure HTTPS</span></span>

<span data-ttu-id="bc282-242">So konfigurieren Sie Dienste mit einem sicheren Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="bc282-242">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="bc282-243">Erstellen Sie über die Mechanismen zum Abrufen und Bereitstellen eines Zertifikats für Ihre Plattform ein X.509-Zertifikat für das Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="bc282-243">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="bc282-244">Geben Sie eine [Kestrel-Server-HTTPS-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) an, um das Zertifikat zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bc282-244">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="bc282-245">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bc282-245">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="bc282-246">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="bc282-246">Current directory and content root</span></span>

<span data-ttu-id="bc282-247">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="bc282-247">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="bc282-248">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="bc282-248">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="bc282-249">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="bc282-249">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="bc282-250">Verwenden von ContentRootPath oder ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="bc282-250">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="bc282-251">Verwenden Sie [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) oder <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>, um die Ressourcen einer App zu finden.</span><span class="sxs-lookup"><span data-stu-id="bc282-251">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="bc282-252">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="bc282-252">Set the content root path to the app's folder</span></span>

<span data-ttu-id="bc282-253"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="bc282-253">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="bc282-254">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum Inhaltsstamm der App auf.</span><span class="sxs-lookup"><span data-stu-id="bc282-254">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="bc282-255">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="bc282-255">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="bc282-256">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="bc282-256">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="bc282-257">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="bc282-257">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc282-258">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bc282-258">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="bc282-259">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="bc282-259">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="bc282-260">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="bc282-260">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
