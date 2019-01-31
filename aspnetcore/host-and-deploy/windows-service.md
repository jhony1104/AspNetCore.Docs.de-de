---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: eedaf64710506f2a2aac65c178a9888d2ab33d38
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837480"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="4e1ab-103">Hosten von ASP.NET Core in einem Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="4e1ab-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="4e1ab-104">Von [Luke Latham](https://github.com/guardrex) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4e1ab-105">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="4e1ab-106">Wenn die App als Windows-Dienst gehostet wird, erfolgen Neustarts automatisch.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="4e1ab-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4e1ab-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="4e1ab-108">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="4e1ab-108">Deployment type</span></span>

<span data-ttu-id="4e1ab-109">Sie können entweder eine Framework-abhängige oder eine eigenständige Windows-Dienstbereitstellung erstellen.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="4e1ab-110">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="4e1ab-111">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-111">Framework-dependent deployment</span></span>

<span data-ttu-id="4e1ab-112">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="4e1ab-113">Wenn das FDD-Szenario mit einer ASP.NET Core-Windows-Dienst-App verwendet wird, erzeugt das SDK eine ausführbare Datei (*\*.exe*). Diese wird *Framework-abhängige ausführbare Datei* genannt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="4e1ab-114">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-114">Self-contained deployment</span></span>

<span data-ttu-id="4e1ab-115">Bei einer eigenständigen Bereitstellung (Self-Contained Deployment, SCD) müssen die freigegebenen Komponenten nicht auf dem Zielsystem vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="4e1ab-116">Die Runtime und die App Abhängigkeiten werden mit der App auf dem Hostsystem bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="4e1ab-117">Konvertieren eines Projekts in einen Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="4e1ab-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="4e1ab-118">Nehmen Sie die folgenden Änderungen an einem vorhandenen ASP.NET Core-Projekt vor, um die App als Dienst auszuführen:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="4e1ab-119">Projektdateiupdates</span><span class="sxs-lookup"><span data-stu-id="4e1ab-119">Project file updates</span></span>

<span data-ttu-id="4e1ab-120">Aktualisieren Sie die Projektdatei basierend auf dem von Ihnen gewählten [Bereitstellungstyp](#deployment-type):</span><span class="sxs-lookup"><span data-stu-id="4e1ab-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="4e1ab-121">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="4e1ab-122">Fügen Sie einen Windows [Runtime-Bezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) zu dem `<PropertyGroup>` hinzu, der das Zielframework enthält.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="4e1ab-123">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-123">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="4e1ab-124">Fügen Sie den `<SelfContained>` -Eigenschaftensatz zu `false` hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-124">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="4e1ab-125">Diese Eigenschaften weisen das SDK an, eine ausführbare Datei (*EXE*) für Windows zu generieren.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-125">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="4e1ab-126">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-126">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="4e1ab-127">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-127">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

<span data-ttu-id="4e1ab-128">Fügen Sie den `<UseAppHost>` -Eigenschaftensatz zu `true` hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-128">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="4e1ab-129">Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-129">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="4e1ab-130">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-130">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="4e1ab-131">Bestätigen Sie das Vorhandensein eines Windows [Runtime-Bezeichners](/dotnet/core/rid-catalog), oder fügen Sie einen solchen Bezeichner zu der `<PropertyGroup>` hinzu, die das Zielframework enthält.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-131">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="4e1ab-132">Deaktivieren Sie die Erstellung einer *web.config"*-Datei durch Hinzufügen des `<IsTransformWebConfigDisabled>`-Eigenschaftensatzes zu `true`.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-132">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="4e1ab-133">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-133">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="4e1ab-134">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-134">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="4e1ab-135">Verwenden Sie den Eigenschaftennamen `<RuntimeIdentifiers>` (Plural).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-135">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="4e1ab-136">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-136">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="4e1ab-137">Fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-137">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="4e1ab-138">Um die Protokollierung für Windows-Ereignisprotokolle zu aktivieren, fügen Sie einen Paketverweis für [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-138">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="4e1ab-139">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-139">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="4e1ab-140">Program.Main-Updates</span><span class="sxs-lookup"><span data-stu-id="4e1ab-140">Program.Main updates</span></span>

<span data-ttu-id="4e1ab-141">Nehmen Sie in `Program.Main` die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-141">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="4e1ab-142">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-142">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="4e1ab-143">Überprüfen Sie, ob der Debugger angefügt ist, oder ob ein `--console`-Befehlszeilenargument vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-143">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="4e1ab-144">Wenn eine der Bedingungen zutrifft (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf dem Webhost auf.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-144">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="4e1ab-145">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="4e1ab-145">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="4e1ab-146">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-146">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="4e1ab-147">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn `GetCurrentDirectory` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-147">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when `GetCurrentDirectory` is called.</span></span> <span data-ttu-id="4e1ab-148">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-148">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="4e1ab-149">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-149">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="4e1ab-150">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der `--console`-Schalter aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-150">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="4e1ab-151">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-151">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="4e1ab-152">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-152">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="4e1ab-153">Zu Demonstrations- und Testzwecken setzt die Produktionseinstellungsdatei der Beispiel-App den Protokollierungsgrad auf `Information`.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-153">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="4e1ab-154">In der Produktion wird der Wert in der Regel auf `Error` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-154">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="4e1ab-155">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-155">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="4e1ab-156">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="4e1ab-156">Publish the app</span></span>

<span data-ttu-id="4e1ab-157">Veröffentlichen Sie die App mit [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), einem [Visual Studio-Veröffentlichungsprofil](xref:host-and-deploy/visual-studio-publish-profiles) oder Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-157">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="4e1ab-158">Wählen Sie bei Verwendung von Visual Studio das **FolderProfile** aus, und konfigurieren Sie den **Zielspeicherort**, bevor Sie auf die Schaltfläche **Veröffentlichen** klicken.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-158">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="4e1ab-159">Um die Beispiel-App mit CLI-Tools (Befehlszeilenschnittstelle) zu veröffentlichen, führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) an einer Eingabeaufforderung aus dem Projektordner mit einer Releasekonfiguration aus, die an die [-c|--configuration](/dotnet/core/tools/dotnet-publish#options)-Option übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-159">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="4e1ab-160">Verwenden Sie die [-o|--output](/dotnet/core/tools/dotnet-publish#options)-Option mit einem Pfad für die Veröffentlichung in einem Ordner außerhalb der App.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-160">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="4e1ab-161">Veröffentlichen einer Framework-abhängigen Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-161">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="4e1ab-162">Im folgenden Beispiel wird die App im Ordner *c:\\svc* veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-162">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="4e1ab-163">Veröffentlichen einer eigenständigen Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="4e1ab-163">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="4e1ab-164">Der RID muss in der Eigenschaft `<RuntimeIdenfifier>` (oder `<RuntimeIdentifiers>`) der Projektdatei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-164">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="4e1ab-165">Stellen Sie die Runtime für die [-r|--runtime](/dotnet/core/tools/dotnet-publish#options)-Option des `dotnet publish`-Befehls bereit.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-165">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="4e1ab-166">Im folgenden Beispiel wird die App für die `win7-x64`-Runtime im Ordner *c:\\svc* veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-166">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="4e1ab-167">Erstellen eines Benutzerkontos</span><span class="sxs-lookup"><span data-stu-id="4e1ab-167">Create a user account</span></span>

<span data-ttu-id="4e1ab-168">Erstellen Sie mit dem `net user`-Befehl ein Benutzerkonto für den Dienst:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-168">Create a user account for the service using the `net user` command:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="4e1ab-169">Erstellen Sie für die Beispiel-App ein Benutzerkonto mit dem Namen `ServiceUser` und einem Kennwort.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-169">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="4e1ab-170">Ersetzen Sie im folgenden Befehl `{PASSWORD}` durch ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-170">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="4e1ab-171">Wenn Sie den Benutzer einer Gruppe hinzufügen müssen, verwenden Sie den Befehl `net localgroup`. Hierbei steht `{GROUP}` für den Namen der Gruppe:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-171">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="4e1ab-172">Weitere Informationen finden Sie unter [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-172">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="4e1ab-173">Festlegen von Berechtigungen</span><span class="sxs-lookup"><span data-stu-id="4e1ab-173">Set permissions</span></span>

<span data-ttu-id="4e1ab-174">Gewähren Sie mit dem Befehl [icacls](/windows-server/administration/windows-commands/icacls) Schreib-/Lese-/Ausführungszugriff für den App-Ordner:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-174">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="4e1ab-175">`{PATH}` &ndash; Pfad zum App-Ordner.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-175">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="4e1ab-176">`{USER ACCOUNT}` &ndash; Das Benutzerkonto (SID).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-176">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="4e1ab-177">`(OI)`&ndash; Mit dem Flag für die Objektvererbung werden Berechtigungen an untergeordnete Dateien weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-177">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="4e1ab-178">`(CI)`&ndash; Mit dem Flag für die Containervererbung werden Berechtigungen an untergeordnete Ordner weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-178">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="4e1ab-179">`{PERMISSION FLAGS}` &ndash; Legt die Zugriffsberechtigungen für die App fest.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-179">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="4e1ab-180">Schreiben (`W`)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-180">Write (`W`)</span></span>
  * <span data-ttu-id="4e1ab-181">Lesen (`R`)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-181">Read (`R`)</span></span>
  * <span data-ttu-id="4e1ab-182">Ausführen (`X`)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-182">Execute (`X`)</span></span>
  * <span data-ttu-id="4e1ab-183">Vollzugriff (`F`)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-183">Full (`F`)</span></span>
  * <span data-ttu-id="4e1ab-184">Ändern (`M`)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-184">Modify (`M`)</span></span>
* <span data-ttu-id="4e1ab-185">`/t` &ndash; Rekursive Anwendung auf vorhandene untergeordnete Ordner und Dateien.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-185">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="4e1ab-186">Verwenden Sie für die im Ordner *c:\\svc* veröffentlichte Beispiel-App und das Konto `ServiceUser` mit Schreib-/Lese-/Ausführungsberechtigungen den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-186">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="4e1ab-187">Weitere Informationen finden Sie unter [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-187">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="4e1ab-188">Verwalten des Diensts</span><span class="sxs-lookup"><span data-stu-id="4e1ab-188">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="4e1ab-189">Erstellen Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-189">Create the service</span></span>

<span data-ttu-id="4e1ab-190">Verwenden Sie das Befehlszeilentool [sc.exe](https://technet.microsoft.com/library/bb490995), um den Dienst zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-190">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service.</span></span> <span data-ttu-id="4e1ab-191">Der Wert `binPath` ist der Pfad zu der ausführbaren Datei der App, der den Namen der ausführbaren Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-191">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="4e1ab-192">**Das Leerzeichen zwischen dem Gleichheitszeichen und dem Anführungszeichen für jeden Parameter und Wert ist erforderlich.**</span><span class="sxs-lookup"><span data-stu-id="4e1ab-192">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="4e1ab-193">`{SERVICE NAME}` &ndash; Der Name, der dem Dienst im [Dienststeuerungs-Manager](/windows/desktop/services/service-control-manager) zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-193">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="4e1ab-194">`{PATH}`&ndash; Der Pfad zur ausführbaren Datei des Diensts.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-194">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="4e1ab-195">`{DOMAIN}` &ndash; Die Domäne eines in eine Domäne eingebundenen Computers.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-195">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="4e1ab-196">Wenn der Computer nicht in die Domäne eingebunden ist, ist dies der Name des lokalen Computers.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-196">If the machine isn't domain-joined, the local machine name.</span></span>
* <span data-ttu-id="4e1ab-197">`{USER ACCOUNT}` &ndash; Das Benutzerkonto, unter dem der Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-197">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="4e1ab-198">`{PASSWORD}` &ndash; Das Kennwort für das Benutzerkonto.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-198">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="4e1ab-199">Lassen Sie den Parameter`obj` **nicht** aus.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-199">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="4e1ab-200">Der Standardwert für `obj` ist das [LocalSystem-Konto](/windows/desktop/services/localsystem-account).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-200">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="4e1ab-201">Die Ausführung eines Diensts mit dem `LocalSystem`-Konto stellt ein erhebliches Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-201">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="4e1ab-202">Führen Sie einen Dienst immer mit einem Benutzerkonto mit eingeschränkten Berechtigungen aus.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-202">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="4e1ab-203">Im folgenden Beispiel für die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-203">In the following example for the sample app:</span></span>

* <span data-ttu-id="4e1ab-204">Der Dienst heißt **MyService**.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-204">The service is named **MyService**.</span></span>
* <span data-ttu-id="4e1ab-205">Der veröffentlichte Dienst ist im Ordner *c:\\svc* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-205">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="4e1ab-206">Die ausführbare Datei der App heißt *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-206">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="4e1ab-207">Setzen Sie den `binPath`-Wert in doppelte Anführungszeichen (").</span><span class="sxs-lookup"><span data-stu-id="4e1ab-207">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="4e1ab-208">Der Dienst wird mit dem Konto `ServiceUser` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-208">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="4e1ab-209">Ersetzen Sie `{DOMAIN}` durch den Domänennamen oder den lokalen Computernamen für das Benutzerkonto.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-209">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="4e1ab-210">Setzen Sie den `obj`-Wert in doppelte Anführungszeichen (").</span><span class="sxs-lookup"><span data-stu-id="4e1ab-210">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="4e1ab-211">Beispiel: Wenn das Hostsystem ein lokaler Computer namens `MairaPC` ist, legen Sie `obj` auf `"MairaPC\ServiceUser"` fest.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-211">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="4e1ab-212">Ersetzen Sie `{PASSWORD}` durch das Kennwort für das Benutzerkonto.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-212">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="4e1ab-213">Setzen Sie den `password`-Wert in doppelte Anführungszeichen (").</span><span class="sxs-lookup"><span data-stu-id="4e1ab-213">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="4e1ab-214">Stellen Sie sicher, dass Leerzeichen zwischen den Gleichheitszeichen des Parameters und den Parameterwerten vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-214">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="4e1ab-215">Starten des Diensts</span><span class="sxs-lookup"><span data-stu-id="4e1ab-215">Start the service</span></span>

<span data-ttu-id="4e1ab-216">Starten Sie den Dienst mithilfe des Befehls `sc start {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-216">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="4e1ab-217">Verwenden Sie zum Starten des Diensts der Beispiel-App den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-217">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="4e1ab-218">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-218">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="4e1ab-219">Ermitteln des Dienststatus</span><span class="sxs-lookup"><span data-stu-id="4e1ab-219">Determine the service status</span></span>

<span data-ttu-id="4e1ab-220">Um den Status des Diensts zu überprüfen, verwenden Sie den `sc query {SERVICE NAME}`-Befehl.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-220">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="4e1ab-221">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-221">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="4e1ab-222">Verwenden Sie den folgenden Befehl, um den Status des Diensts der Beispiel-App zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-222">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="4e1ab-223">Durchsuchen eines Web-App-Diensts</span><span class="sxs-lookup"><span data-stu-id="4e1ab-223">Browse a web app service</span></span>

<span data-ttu-id="4e1ab-224">Befindet sich der Dienst im Status `RUNNING`, und ist der Dienst gleichzeitig eine Web-App, rufen Sie die App über ihren Pfad auf (Standardpfad ist `http://localhost:5000`, der umgeleitet wird zu `https://localhost:5001`, wenn [Middleware für HTTPS-Umleitung](xref:security/enforcing-ssl) verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-224">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="4e1ab-225">Rufen Sie die App für den Dienst der Beispiel-App über `http://localhost:5000` auf.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-225">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="4e1ab-226">Dienst beenden</span><span class="sxs-lookup"><span data-stu-id="4e1ab-226">Stop the service</span></span>

<span data-ttu-id="4e1ab-227">Verwenden Sie zum Beenden des Diensts den Befehl `sc stop {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-227">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="4e1ab-228">Verwenden Sie zum Beenden des Diensts der Beispiel-App den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-228">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="4e1ab-229">Löschen des Diensts</span><span class="sxs-lookup"><span data-stu-id="4e1ab-229">Delete the service</span></span>

<span data-ttu-id="4e1ab-230">Deinstallieren Sie den Dienst nach einer kurzen Verzögerung zum Beenden des Diensts mit dem Befehl `sc delete {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-230">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="4e1ab-231">Überprüfen Sie den Status des Diensts der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-231">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="4e1ab-232">Befindet sich der Dienst der Beispiel-App im Status `STOPPED`, verwenden Sie zum Deinstallieren des Diensts der Beispiel-App den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-232">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="4e1ab-233">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="4e1ab-233">Handle starting and stopping events</span></span>

<span data-ttu-id="4e1ab-234">Nehmen Sie die folgenden zusätzlichen Änderungen vor, um Ereignisse vom Typ <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zu handhaben:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-234">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="4e1ab-235">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-235">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="4e1ab-236">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-236">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="4e1ab-237">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-237">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="4e1ab-238">Um den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` anzuzeigen, lesen Sie bitte das Codebeispiel im Abschnitt [Konvertieren eines Projekts in einen Windows-Dienst](#convert-a-project-into-a-windows-service).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-238">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4e1ab-239">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="4e1ab-239">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4e1ab-240">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-240">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="4e1ab-241">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-241">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="4e1ab-242">HTTPS konfigurieren</span><span class="sxs-lookup"><span data-stu-id="4e1ab-242">Configure HTTPS</span></span>

<span data-ttu-id="4e1ab-243">So konfigurieren Sie den Dienst mit einem sicheren Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-243">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="4e1ab-244">Erstellen Sie über die Mechanismen zum Abrufen und Bereitstellen eines Zertifikats für Ihre Plattform ein X.509-Zertifikat für das Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-244">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="4e1ab-245">Geben Sie eine [Kestrel-Server-HTTPS-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) an, um das Zertifikat zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-245">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="4e1ab-246">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-246">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="4e1ab-247">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4e1ab-247">Current directory and content root</span></span>

<span data-ttu-id="4e1ab-248">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-248">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="4e1ab-249">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="4e1ab-249">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="4e1ab-250">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-250">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="4e1ab-251">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-251">Set the content root path to the app's folder</span></span>

<span data-ttu-id="4e1ab-252"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem gleichen Pfad, der für das Argument `binPath` bereitgestellt wird, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-252">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="4e1ab-253">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum Inhaltsstamm der App auf.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-253">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="4e1ab-254">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4e1ab-254">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="4e1ab-255">Speichern Sie die Dateien des Dienstes an einem geeigneten Speicherort auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-255">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="4e1ab-256">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="4e1ab-256">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e1ab-257">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4e1ab-257">Additional resources</span></span>

* <span data-ttu-id="4e1ab-258">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="4e1ab-258">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
