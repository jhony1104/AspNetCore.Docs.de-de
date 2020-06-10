---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 84612ccfdf00497b11cd93cef2837c5a897cc905
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84105349"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="21975-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-103">'Identity'</span></span>

<span data-ttu-id="21975-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21975-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-105">'Razor'</span></span>

* <span data-ttu-id="21975-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-106">'SignalR' uid:</span></span>
* <span data-ttu-id="21975-107">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="21975-107">ASP.NET Core Module</span></span>

<span data-ttu-id="21975-108">Von [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [ Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="21975-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

* <span data-ttu-id="21975-109">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="21975-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>
* <span data-ttu-id="21975-110">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>

<span data-ttu-id="21975-111">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="21975-112">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="21975-112">Supported Windows versions:</span></span> <span data-ttu-id="21975-113">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-113">Windows 7 or later</span></span>

## <a name="hosting-models"></a><span data-ttu-id="21975-114">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-114">Windows Server 2008 R2 or later</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="21975-115">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="21975-116">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="21975-116">When hosting out-of-process, the module only works with Kestrel.</span></span>

<span data-ttu-id="21975-117">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="21975-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

* <span data-ttu-id="21975-118">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="21975-118">Hosting models</span></span> <span data-ttu-id="21975-119">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="21975-119">In-process hosting model</span></span>

  * <span data-ttu-id="21975-120">ASP.NET Core-Apps verwenden standardmäßig das In-Process-Hostingmodell.</span><span class="sxs-lookup"><span data-stu-id="21975-120">ASP.NET Core apps default to the in-process hosting model.</span></span>
  * <span data-ttu-id="21975-121">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="21975-121">The following characteristics apply when hosting in-process:</span></span>
  * <span data-ttu-id="21975-122">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="21975-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

* <span data-ttu-id="21975-123">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="21975-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

* <span data-ttu-id="21975-124">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="21975-124">Register the `IISHttpServer`.</span></span> <span data-ttu-id="21975-125">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>

* <span data-ttu-id="21975-126">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="21975-126">Configure the host to capture startup errors.</span></span> <span data-ttu-id="21975-127">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="21975-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="21975-128">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-128">Sharing an app pool among apps isn't supported.</span></span>

* <span data-ttu-id="21975-129">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="21975-129">Use one app pool per app.</span></span> <span data-ttu-id="21975-130">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="21975-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span>

* <span data-ttu-id="21975-131">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="21975-131">For example, a websocket connection may delay app shut down.</span></span>

  <span data-ttu-id="21975-132">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="21975-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="21975-133">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="21975-133">Client disconnects are detected.</span></span> <span data-ttu-id="21975-134">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="21975-135">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="21975-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span> <span data-ttu-id="21975-136">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="21975-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="21975-137">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="21975-137">Call the `SetCurrentDirectory` method.</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="21975-138">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="21975-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="21975-139">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="21975-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span>

<span data-ttu-id="21975-140">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="21975-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="21975-141">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="21975-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

<span data-ttu-id="21975-142">[Bereitstellungen von Webpaketen (Einzeldateien)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

<span data-ttu-id="21975-143">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="21975-143">Out-of-process hosting model</span></span>

<span data-ttu-id="21975-144">Um eine App für Out-of-Process-Hosting zu konfigurieren, legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei ( *.csproj*) auf `OutOfProcess` fest:</span><span class="sxs-lookup"><span data-stu-id="21975-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

* <span data-ttu-id="21975-145">Das In-Process-Hosting wird mithilfe von `InProcess` (Standardwert) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>
* <span data-ttu-id="21975-146">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="21975-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="21975-147">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="21975-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="21975-148">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="21975-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

<span data-ttu-id="21975-149">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span> <span data-ttu-id="21975-150">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="21975-150">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="21975-151">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="21975-151">Hosting model changes</span></span>

<span data-ttu-id="21975-152">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="21975-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="21975-153">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="21975-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="21975-154">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="21975-154">The next request to the app spawns a new IIS Express process.</span></span>

<span data-ttu-id="21975-155">Prozessname</span><span class="sxs-lookup"><span data-stu-id="21975-155">Process name</span></span>

* <span data-ttu-id="21975-156">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="21975-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>
* <span data-ttu-id="21975-157">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="21975-157">Many native modules, such as Windows Authentication, remain active.</span></span>
* <span data-ttu-id="21975-158">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="21975-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="21975-159">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="21975-159">The ASP.NET Core Module can also:</span></span>

<span data-ttu-id="21975-160">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="21975-160">Set environment variables for the worker process.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="21975-161">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="21975-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>

<span data-ttu-id="21975-162">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="21975-162">Forward Windows authentication tokens.</span></span>

<span data-ttu-id="21975-163">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="21975-163">How to install and use the ASP.NET Core Module</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="21975-164">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="21975-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="21975-165">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="21975-165">Configuration with web.config</span></span>

<span data-ttu-id="21975-166">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="21975-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span> <span data-ttu-id="21975-167">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="21975-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

<span data-ttu-id="21975-168">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="21975-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="21975-169">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="21975-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

| <span data-ttu-id="21975-170">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="21975-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> | <span data-ttu-id="21975-171">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span> | <span data-ttu-id="21975-172">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="21975-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="21975-173">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="21975-173">Attributes of the aspNetCore element</span></span></p><p><span data-ttu-id="21975-174">Attribut</span><span class="sxs-lookup"><span data-stu-id="21975-174">Attribute</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="21975-175">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="21975-175">Description</span></span></p><p><span data-ttu-id="21975-176">Standard</span><span class="sxs-lookup"><span data-stu-id="21975-176">Default</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="21975-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p><p><span data-ttu-id="21975-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-178">'Blazor'</span></span> <span data-ttu-id="21975-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-179">'Identity'</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="21975-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-180">'Let's Encrypt'</span></span></p><p><span data-ttu-id="21975-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-181">'Razor'</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="21975-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-182">'SignalR' uid:</span></span></p><p><span data-ttu-id="21975-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p><p><span data-ttu-id="21975-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-184">'Blazor'</span></span></p><p><span data-ttu-id="21975-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-185">'Identity'</span></span> <span data-ttu-id="21975-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-186">'Let's Encrypt'</span></span></p> | <span data-ttu-id="21975-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-187">'Razor'</span></span><br><span data-ttu-id="21975-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-188">'SignalR' uid:</span></span><br><span data-ttu-id="21975-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `processPath` | <p><span data-ttu-id="21975-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-190">'Blazor'</span></span></p><p><span data-ttu-id="21975-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-191">'Identity'</span></span> <span data-ttu-id="21975-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-192">'Let's Encrypt'</span></span> <span data-ttu-id="21975-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-193">'Razor'</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="21975-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-194">'SignalR' uid:</span></span></p><p><span data-ttu-id="21975-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="21975-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-196">'Blazor'</span></span></p><p><span data-ttu-id="21975-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-197">'Identity'</span></span></p> | <span data-ttu-id="21975-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-198">'Let's Encrypt'</span></span><br><span data-ttu-id="21975-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-199">'Razor'</span></span><br><span data-ttu-id="21975-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-200">'SignalR' uid:</span></span> |
| `requestTimeout` | <p><span data-ttu-id="21975-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p><p><span data-ttu-id="21975-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-202">'Blazor'</span></span></p><p><span data-ttu-id="21975-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-203">'Identity'</span></span></p><p><span data-ttu-id="21975-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-204">'Let's Encrypt'</span></span> <span data-ttu-id="21975-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-205">'Razor'</span></span></p><p><span data-ttu-id="21975-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-206">'SignalR' uid:</span></span> <span data-ttu-id="21975-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="21975-207">------ | :-----: | | `arguments` | </span></span></p> | <span data-ttu-id="21975-208">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-208">Optional string attribute.</span></span><br><span data-ttu-id="21975-209">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-209">Arguments to the executable specified in **processPath**.</span></span><br><span data-ttu-id="21975-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="21975-210">| | | `disableStartUpErrorPage` | </span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="21975-211">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="21975-212">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="21975-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | <span data-ttu-id="21975-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="21975-213">| `false` | | `forwardWindowsAuthToken` | </span></span><br><span data-ttu-id="21975-214">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-214">Optional Boolean attribute.</span></span><br><span data-ttu-id="21975-215">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="21975-216">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="21975-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p><p><span data-ttu-id="21975-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="21975-217">| `true` | | `hostingModel` | </span></span> <span data-ttu-id="21975-218">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-218">Optional string attribute.</span></span> <span data-ttu-id="21975-219">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="21975-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p><p><span data-ttu-id="21975-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="21975-220">`inprocess` | | `processesPerApplication` | </span></span></p> | <span data-ttu-id="21975-221">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-221">Optional integer attribute.</span></span><br><span data-ttu-id="21975-222">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="21975-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span><br><span data-ttu-id="21975-223">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="21975-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="21975-224">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="21975-224">Setting `processesPerApplication` is discouraged.</span></span></p><p><span data-ttu-id="21975-225">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="21975-225">This attribute will be removed in a future release.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="21975-226">| Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-226">| Default: `1`</span></span></p><p><span data-ttu-id="21975-227">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-227">Min: `1`</span></span> <span data-ttu-id="21975-228">Max: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="21975-228">Max: `100`&dagger; | | `processPath` | </span></span> <span data-ttu-id="21975-229">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-229">Required string attribute.</span></span> <span data-ttu-id="21975-230">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="21975-231">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-231">Relative paths are supported.</span></span> <span data-ttu-id="21975-232">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="21975-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="21975-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="21975-233">| | | `rapidFailsPerMinute` | </span></span>

<span data-ttu-id="21975-234">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-234">Optional integer attribute.</span></span> <span data-ttu-id="21975-235">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="21975-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="21975-236">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="21975-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span>

<span data-ttu-id="21975-237">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-237">Not supported with in-process hosting.</span></span> <span data-ttu-id="21975-238">| Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="21975-238">| Default: `10`</span></span> <span data-ttu-id="21975-239">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-239">Min: `0`</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="21975-240">Max: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="21975-240">Max: `100` | | `requestTimeout` | </span></span> <span data-ttu-id="21975-241">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-241">Optional timespan attribute.</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="21975-242">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="21975-243">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="21975-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span>

<span data-ttu-id="21975-244">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="21975-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="21975-245">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="21975-245">For in-process hosting, the module waits for the app to process the request.</span></span>

<span data-ttu-id="21975-246">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="21975-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="21975-247">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="21975-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span>

<span data-ttu-id="21975-248">| Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="21975-248">| Default: `00:02:00`</span></span> <span data-ttu-id="21975-249">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="21975-249">Min: `00:00:00`</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="21975-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="21975-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span>

<span data-ttu-id="21975-251">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-251">Optional integer attribute.</span></span>

<span data-ttu-id="21975-252">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span>

<span data-ttu-id="21975-253">| Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="21975-253">| Default: `10`</span></span>

<span data-ttu-id="21975-254">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-254">Min: `0`</span></span>

<span data-ttu-id="21975-255">Max: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="21975-255">Max: `600` | | `startupTimeLimit` | </span></span> <span data-ttu-id="21975-256">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-256">Optional integer attribute.</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="21975-257">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span>

<span data-ttu-id="21975-258">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="21975-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="21975-259">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="21975-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span> <span data-ttu-id="21975-260">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="21975-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span>

<span data-ttu-id="21975-261">| Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="21975-261">| Default: `120`</span></span> <span data-ttu-id="21975-262">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-262">Min: `0`</span></span>

<span data-ttu-id="21975-263">Max: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="21975-263">Max: `3600` | | `stdoutLogEnabled` | </span></span>

<span data-ttu-id="21975-264">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-264">Optional Boolean attribute.</span></span> <span data-ttu-id="21975-265">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span> <span data-ttu-id="21975-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="21975-266">| `false` | | `stdoutLogFile` | </span></span>

<span data-ttu-id="21975-267">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-267">Optional string attribute.</span></span> <span data-ttu-id="21975-268">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="21975-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="21975-269">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="21975-269">Relative paths are relative to the root of the site.</span></span>

<span data-ttu-id="21975-270">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="21975-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="21975-271">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-271">Any folders provided in the path are created by the module when the log file is created.</span></span>

<span data-ttu-id="21975-272">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="21975-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="21975-273">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="21975-274">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="21975-274">Set environment variables</span></span> <span data-ttu-id="21975-275">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="21975-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span>

<span data-ttu-id="21975-276">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="21975-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

<span data-ttu-id="21975-277">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="21975-277">Environment variables set in this section take precedence over system environment variables.</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="21975-278">Im folgenden Beispiel werden zwei Umgebungsvariablen in *web.config* festgelegt. `ASPNETCORE_ENVIRONMENT` legt die Umgebung der App auf `Development` fest.</span><span class="sxs-lookup"><span data-stu-id="21975-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span>

<span data-ttu-id="21975-279">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="21975-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="21975-280">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="21975-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="21975-281">Eine Alternative zum direkten Festlegen der Umgebung in *web.config* ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="21975-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="21975-282">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="21975-282">This approach sets the environment in *web.config* when the project is published:</span></span>

<span data-ttu-id="21975-283">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="21975-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

<span data-ttu-id="21975-284">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="21975-284">app_offline.htm</span></span>

* <span data-ttu-id="21975-285">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="21975-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span>
* <span data-ttu-id="21975-286">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="21975-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>
* <span data-ttu-id="21975-287">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="21975-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span>
* <span data-ttu-id="21975-288">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="21975-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="21975-289">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="21975-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span>

* <span data-ttu-id="21975-290">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="21975-290">For example, a websocket connection may delay app shut down.</span></span>
* <span data-ttu-id="21975-291">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="21975-291">Start-up error page</span></span>
* <span data-ttu-id="21975-292">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="21975-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="21975-293">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

* <span data-ttu-id="21975-294">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span> <span data-ttu-id="21975-295">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>
* <span data-ttu-id="21975-296">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="21975-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span>

> [!WARNING]
> <span data-ttu-id="21975-297">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="21975-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span> <span data-ttu-id="21975-298">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="21975-298">Log creation and redirection</span></span> <span data-ttu-id="21975-299">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="21975-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span>

<span data-ttu-id="21975-300">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="21975-301">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="21975-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="21975-302">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="21975-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span>

<span data-ttu-id="21975-303">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="21975-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="21975-304">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="21975-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="21975-305">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="21975-305">Don't use the stdout log for general app logging purposes.</span></span>

<span data-ttu-id="21975-306">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="21975-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="21975-307">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="21975-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="21975-308">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="21975-309">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="21975-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="21975-310">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="21975-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span> <span data-ttu-id="21975-311">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="21975-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="21975-312">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="21975-312">After startup, all additional logs are discarded.</span></span> <span data-ttu-id="21975-313">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="21975-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="21975-314">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="21975-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="21975-315">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="21975-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span>

<span data-ttu-id="21975-316">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="21975-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span> <span data-ttu-id="21975-317">Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="21975-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="21975-318">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="21975-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="21975-319">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="21975-319">Enhanced diagnostic logs</span></span>

1. <span data-ttu-id="21975-320">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="21975-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span>
1. <span data-ttu-id="21975-321">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="21975-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>
1. <span data-ttu-id="21975-322">Ordner, die im Pfad angegeben werden (*logs* im vorherigen Beispiel), werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span>
1. <span data-ttu-id="21975-323">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="21975-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="21975-324">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="21975-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="21975-325">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="21975-325">Levels (in order from least to most verbose):</span></span>

1. <span data-ttu-id="21975-326">ERROR</span><span class="sxs-lookup"><span data-stu-id="21975-326">ERROR</span></span>
1. <span data-ttu-id="21975-327">WARNING</span><span class="sxs-lookup"><span data-stu-id="21975-327">WARNING</span></span>
1. <span data-ttu-id="21975-328">INFO</span><span class="sxs-lookup"><span data-stu-id="21975-328">INFO</span></span>
1. <span data-ttu-id="21975-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="21975-329">TRACE</span></span>

<span data-ttu-id="21975-330">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="21975-330">Locations (multiple locations are permitted):</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="21975-331">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="21975-331">CONSOLE</span></span>

### <a name="module"></a><span data-ttu-id="21975-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="21975-332">EVENTLOG</span></span>

<span data-ttu-id="21975-333">DATEI</span><span class="sxs-lookup"><span data-stu-id="21975-333">FILE</span></span>

* <span data-ttu-id="21975-334">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="21975-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="21975-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="21975-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span>

* <span data-ttu-id="21975-336">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="21975-336">(Default: *aspnetcore-debug.log*)</span></span>

* <span data-ttu-id="21975-337">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="21975-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

<span data-ttu-id="21975-338">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="21975-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span>

* <span data-ttu-id="21975-339">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="21975-339">The size of the log isn't limited.</span></span>

* <span data-ttu-id="21975-340">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="21975-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

* <span data-ttu-id="21975-341">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="21975-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

* <span data-ttu-id="21975-342">Ändern der Stapelgröße</span><span class="sxs-lookup"><span data-stu-id="21975-342">Modify the stack size</span></span>

### <a name="schema"></a><span data-ttu-id="21975-343">*Gilt nur bei Verwendung des In-Process-Hostingmodells.*</span><span class="sxs-lookup"><span data-stu-id="21975-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="21975-344">Konfigurieren Sie die Größe des verwalteten Stapels in der Datei *web.config* mithilfe der Einstellung `stackSize` (in Byte). Die Standardgröße beträgt `1048576` Byte (1 MB).</span><span class="sxs-lookup"><span data-stu-id="21975-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

* <span data-ttu-id="21975-345">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="21975-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

* <span data-ttu-id="21975-346">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="21975-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="21975-347">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="21975-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span>

* <span data-ttu-id="21975-348">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="21975-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

* <span data-ttu-id="21975-349">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="21975-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span>

### <a name="configuration"></a><span data-ttu-id="21975-350">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span>

<span data-ttu-id="21975-351">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span>

* <span data-ttu-id="21975-352">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="21975-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span>

<span data-ttu-id="21975-353">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="21975-353">If the token values are mismatched, the request is logged and rejected.</span></span>

* <span data-ttu-id="21975-354">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="21975-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span>

* <span data-ttu-id="21975-355">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="21975-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

<span data-ttu-id="21975-356">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="21975-357">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="21975-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span>

* <span data-ttu-id="21975-358">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="21975-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>
* <span data-ttu-id="21975-359">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="21975-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

<span data-ttu-id="21975-360">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="21975-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

* <span data-ttu-id="21975-361">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="21975-361">Disable the IIS Shared Configuration.</span></span>
* <span data-ttu-id="21975-362">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="21975-362">Run the installer.</span></span>

<span data-ttu-id="21975-363">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="21975-363">Export the updated *applicationHost.config* file to the share.</span></span>

<span data-ttu-id="21975-364">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="21975-364">Re-enable the IIS Shared Configuration.</span></span> <span data-ttu-id="21975-365">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="21975-365">Module version and Hosting Bundle installer logs</span></span>

## <a name="hosting-models"></a><span data-ttu-id="21975-366">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="21975-366">To determine the version of the installed ASP.NET Core Module:</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="21975-367">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="21975-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>

<span data-ttu-id="21975-368">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="21975-368">Locate the *aspnetcore.dll* file.</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="21975-369">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="21975-369">Right-click the file and select **Properties** from the contextual menu.</span></span>

<span data-ttu-id="21975-370">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="21975-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="21975-371">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="21975-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

<span data-ttu-id="21975-372">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-372">Module, schema, and configuration file locations</span></span>

* <span data-ttu-id="21975-373">Modul</span><span class="sxs-lookup"><span data-stu-id="21975-373">Module</span></span> <span data-ttu-id="21975-374">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="21975-374">**IIS (x86/amd64):**</span></span>

  * <span data-ttu-id="21975-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>
  * <span data-ttu-id="21975-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>
  * <span data-ttu-id="21975-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="21975-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="21975-379">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="21975-379">**IIS Express (x86/amd64):**</span></span> <span data-ttu-id="21975-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="21975-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span> <span data-ttu-id="21975-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="21975-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="21975-384">Schema</span><span class="sxs-lookup"><span data-stu-id="21975-384">Schema</span></span> <span data-ttu-id="21975-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="21975-385">**IIS**</span></span>

* <span data-ttu-id="21975-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="21975-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

  <span data-ttu-id="21975-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="21975-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span> <span data-ttu-id="21975-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21975-388">**IIS Express**</span></span> <span data-ttu-id="21975-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="21975-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="21975-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="21975-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span> <span data-ttu-id="21975-391">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-391">Configuration</span></span> <span data-ttu-id="21975-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="21975-392">**IIS**</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="21975-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="21975-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="21975-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21975-394">**IIS Express**</span></span>

* <span data-ttu-id="21975-395">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="21975-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span> <span data-ttu-id="21975-396">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="21975-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>
* <span data-ttu-id="21975-397">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="21975-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="21975-398">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="21975-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

<span data-ttu-id="21975-399">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>

<span data-ttu-id="21975-400">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

* <span data-ttu-id="21975-401">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="21975-401">Supported Windows versions:</span></span>
* <span data-ttu-id="21975-402">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-402">Windows 7 or later</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="21975-403">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="21975-404">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="21975-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="21975-405">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="21975-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="21975-406">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="21975-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

### <a name="process-name"></a><span data-ttu-id="21975-407">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="21975-407">Hosting models</span></span>

<span data-ttu-id="21975-408">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="21975-408">In-process hosting model</span></span>

<span data-ttu-id="21975-409">Um eine App für In-Process-Hosting zu konfigurieren, fügen Sie der Projektdatei der App die Eigenschaft `<AspNetCoreHostingModel>` mit dem Wert `InProcess` hinzu (Out-of-Process-Hosting wird mit `OutOfProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="21975-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span> <span data-ttu-id="21975-410">Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.</span><span class="sxs-lookup"><span data-stu-id="21975-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="21975-411">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="21975-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

* <span data-ttu-id="21975-412">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="21975-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="21975-413">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="21975-413">The following characteristics apply when hosting in-process:</span></span>
* <span data-ttu-id="21975-414">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="21975-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="21975-415">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="21975-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

<span data-ttu-id="21975-416">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="21975-416">Register the `IISHttpServer`.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="21975-417">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>

<span data-ttu-id="21975-418">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="21975-418">Configure the host to capture startup errors.</span></span>

<span data-ttu-id="21975-419">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="21975-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="21975-420">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-420">Sharing an app pool among apps isn't supported.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="21975-421">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="21975-421">Use one app pool per app.</span></span>

<span data-ttu-id="21975-422">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="21975-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="21975-423">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="21975-423">For example, a websocket connection may delay app shut down.</span></span>

<span data-ttu-id="21975-424">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="21975-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="21975-425">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="21975-425">Client disconnects are detected.</span></span>

| <span data-ttu-id="21975-426">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span> | <span data-ttu-id="21975-427">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="21975-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span> | <span data-ttu-id="21975-428">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="21975-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="21975-429">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="21975-429">Call the `SetCurrentDirectory` method.</span></span></p><p><span data-ttu-id="21975-430">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="21975-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="21975-431">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="21975-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span></p><p><span data-ttu-id="21975-432">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="21975-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="21975-433">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="21975-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span></p><p><span data-ttu-id="21975-434">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="21975-434">Out-of-process hosting model</span></span> <span data-ttu-id="21975-435">Um eine App für Out-of-Process-Hosting zu konfigurieren, konfigurieren Sie die Projektdatei auf eine der folgenden Weisen:</span><span class="sxs-lookup"><span data-stu-id="21975-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="21975-436">Geben Sie die `<AspNetCoreHostingModel>`-Eigenschaft nicht an.</span><span class="sxs-lookup"><span data-stu-id="21975-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span></p><p><span data-ttu-id="21975-437">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="21975-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="21975-438">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft auf `OutOfProcess` fest (In-Process-Hosting wird mit `InProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="21975-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span></p><p><span data-ttu-id="21975-439">Beim Wert wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="21975-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span></p><p><span data-ttu-id="21975-440">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="21975-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span></p><p><span data-ttu-id="21975-441">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="21975-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span> <span data-ttu-id="21975-442">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span></p> | <span data-ttu-id="21975-443">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="21975-443">Configure the host to capture startup errors.</span></span><br><span data-ttu-id="21975-444">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="21975-444">Hosting model changes</span></span><br><span data-ttu-id="21975-445">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="21975-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span> |
| `processPath` | <p><span data-ttu-id="21975-446">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="21975-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span></p><p><span data-ttu-id="21975-447">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="21975-447">The next request to the app spawns a new IIS Express process.</span></span> <span data-ttu-id="21975-448">Prozessname</span><span class="sxs-lookup"><span data-stu-id="21975-448">Process name</span></span> <span data-ttu-id="21975-449">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="21975-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="21975-450">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="21975-450">Many native modules, such as Windows Authentication, remain active.</span></span></p><p><span data-ttu-id="21975-451">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="21975-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span> <span data-ttu-id="21975-452">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="21975-452">The ASP.NET Core Module can also:</span></span></p><p><span data-ttu-id="21975-453">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="21975-453">Set environment variables for the worker process.</span></span></p> | <span data-ttu-id="21975-454">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="21975-454">Log stdout output to file storage for troubleshooting startup issues.</span></span><br><span data-ttu-id="21975-455">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="21975-455">Forward Windows authentication tokens.</span></span><br><span data-ttu-id="21975-456">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="21975-456">How to install and use the ASP.NET Core Module</span></span> |
| `requestTimeout` | <p><span data-ttu-id="21975-457">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="21975-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span></p><p><span data-ttu-id="21975-458">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="21975-458">Configuration with web.config</span></span></p><p><span data-ttu-id="21975-459">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="21975-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span></p><p><span data-ttu-id="21975-460">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="21975-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span> <span data-ttu-id="21975-461">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="21975-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span></p><p><span data-ttu-id="21975-462">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="21975-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span> <span data-ttu-id="21975-463">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="21975-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span></p> | <span data-ttu-id="21975-464">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span><br><span data-ttu-id="21975-465">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="21975-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span><br><span data-ttu-id="21975-466">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="21975-466">Attributes of the aspNetCore element</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="21975-467">Attribut</span><span class="sxs-lookup"><span data-stu-id="21975-467">Attribute</span></span></p><p><span data-ttu-id="21975-468">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="21975-468">Description</span></span></p> | <span data-ttu-id="21975-469">Standard</span><span class="sxs-lookup"><span data-stu-id="21975-469">Default</span></span><br><span data-ttu-id="21975-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span><br><span data-ttu-id="21975-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-471">'Blazor'</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="21975-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-472">'Identity'</span></span></p><p><span data-ttu-id="21975-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-473">'Let's Encrypt'</span></span> <span data-ttu-id="21975-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-474">'Razor'</span></span> <span data-ttu-id="21975-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-475">'SignalR' uid:</span></span></p><p><span data-ttu-id="21975-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p> | <span data-ttu-id="21975-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-477">'Blazor'</span></span><br><span data-ttu-id="21975-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-478">'Identity'</span></span><br><span data-ttu-id="21975-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-479">'Let's Encrypt'</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="21975-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-480">'Razor'</span></span></p><p><span data-ttu-id="21975-481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-481">'SignalR' uid:</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="21975-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p><p><span data-ttu-id="21975-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-483">'Blazor'</span></span> <span data-ttu-id="21975-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-484">'Identity'</span></span> <span data-ttu-id="21975-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-485">'Let's Encrypt'</span></span> <span data-ttu-id="21975-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-486">'Razor'</span></span> <span data-ttu-id="21975-487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-487">'SignalR' uid:</span></span> <span data-ttu-id="21975-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="21975-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-489">'Blazor'</span></span>

<span data-ttu-id="21975-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-490">'Identity'</span></span> <span data-ttu-id="21975-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-491">'Let's Encrypt'</span></span> <span data-ttu-id="21975-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-492">'Razor'</span></span>

<span data-ttu-id="21975-493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-493">'SignalR' uid:</span></span> <span data-ttu-id="21975-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="21975-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-495">'Blazor'</span></span> <span data-ttu-id="21975-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-496">'Identity'</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="21975-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-497">'Let's Encrypt'</span></span> <span data-ttu-id="21975-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-498">'Razor'</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="21975-499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-499">'SignalR' uid:</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="21975-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="21975-500">------ | :-----: | | `arguments` | </span></span>

<span data-ttu-id="21975-501">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-501">Optional string attribute.</span></span> <span data-ttu-id="21975-502">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-502">Arguments to the executable specified in **processPath**.</span></span>

<span data-ttu-id="21975-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="21975-503">| | | `disableStartUpErrorPage` | </span></span> <span data-ttu-id="21975-504">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-504">Optional Boolean attribute.</span></span>

<span data-ttu-id="21975-505">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="21975-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span> <span data-ttu-id="21975-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="21975-506">| `false` | | `forwardWindowsAuthToken` | </span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="21975-507">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-507">Optional Boolean attribute.</span></span>

<span data-ttu-id="21975-508">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span>

<span data-ttu-id="21975-509">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="21975-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span>

<span data-ttu-id="21975-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="21975-510">| `true` | | `hostingModel` | </span></span>

<span data-ttu-id="21975-511">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-511">Optional string attribute.</span></span>

<span data-ttu-id="21975-512">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="21975-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span> <span data-ttu-id="21975-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="21975-513">`outofprocess` | | `processesPerApplication` | </span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="21975-514">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-514">Optional integer attribute.</span></span>

<span data-ttu-id="21975-515">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="21975-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span> <span data-ttu-id="21975-516">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="21975-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span> <span data-ttu-id="21975-517">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="21975-517">Setting `processesPerApplication` is discouraged.</span></span>

<span data-ttu-id="21975-518">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="21975-518">This attribute will be removed in a future release.</span></span> <span data-ttu-id="21975-519">| Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-519">| Default: `1`</span></span>

<span data-ttu-id="21975-520">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-520">Min: `1`</span></span>

<span data-ttu-id="21975-521">Max: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="21975-521">Max: `100`&dagger; | | `processPath` | </span></span> <span data-ttu-id="21975-522">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-522">Required string attribute.</span></span> <span data-ttu-id="21975-523">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-523">Path to the executable that launches a process listening for HTTP requests.</span></span>

<span data-ttu-id="21975-524">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-524">Relative paths are supported.</span></span> <span data-ttu-id="21975-525">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="21975-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span> <span data-ttu-id="21975-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="21975-526">| | | `rapidFailsPerMinute` | </span></span>

<span data-ttu-id="21975-527">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-527">Optional integer attribute.</span></span> <span data-ttu-id="21975-528">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="21975-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span>

<span data-ttu-id="21975-529">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="21975-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span> <span data-ttu-id="21975-530">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-530">Not supported with in-process hosting.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="21975-531">| Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="21975-531">| Default: `10`</span></span> <span data-ttu-id="21975-532">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-532">Min: `0`</span></span>

<span data-ttu-id="21975-533">Max: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="21975-533">Max: `100` | | `requestTimeout` | </span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="21975-534">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-534">Optional timespan attribute.</span></span>

<span data-ttu-id="21975-535">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span> <span data-ttu-id="21975-536">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="21975-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="21975-537">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="21975-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="21975-538">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="21975-538">For in-process hosting, the module waits for the app to process the request.</span></span>

<span data-ttu-id="21975-539">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="21975-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span>

<span data-ttu-id="21975-540">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="21975-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span>

* <span data-ttu-id="21975-541">| Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="21975-541">| Default: `00:02:00`</span></span>
* <span data-ttu-id="21975-542">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="21975-542">Min: `00:00:00`</span></span>
* <span data-ttu-id="21975-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="21975-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span>
* <span data-ttu-id="21975-544">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-544">Optional integer attribute.</span></span>

<span data-ttu-id="21975-545">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span>

* <span data-ttu-id="21975-546">| Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="21975-546">| Default: `10`</span></span>
* <span data-ttu-id="21975-547">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-547">Min: `0`</span></span>
* <span data-ttu-id="21975-548">Max: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="21975-548">Max: `600` | | `startupTimeLimit` | </span></span>

<span data-ttu-id="21975-549">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-549">Optional integer attribute.</span></span>

* <span data-ttu-id="21975-550">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="21975-551">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="21975-551">If this time limit is exceeded, the module kills the process.</span></span>
* <span data-ttu-id="21975-552">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="21975-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span>

> [!WARNING]
> <span data-ttu-id="21975-553">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="21975-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span> <span data-ttu-id="21975-554">| Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="21975-554">| Default: `120`</span></span> <span data-ttu-id="21975-555">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-555">Min: `0`</span></span>

<span data-ttu-id="21975-556">Max: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="21975-556">Max: `3600` | | `stdoutLogEnabled` | </span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="21975-557">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-557">Optional Boolean attribute.</span></span>

<span data-ttu-id="21975-558">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span>

<span data-ttu-id="21975-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="21975-559">| `false` | | `stdoutLogFile` | </span></span> <span data-ttu-id="21975-560">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-560">Optional string attribute.</span></span>

<span data-ttu-id="21975-561">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="21975-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="21975-562">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="21975-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="21975-563">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="21975-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="21975-564">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="21975-565">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="21975-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="21975-566">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span> <span data-ttu-id="21975-567">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="21975-567">Setting environment variables</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="21975-568">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="21975-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span>

<span data-ttu-id="21975-569">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="21975-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="21975-570">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="21975-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="21975-571">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-571">The following example sets two environment variables.</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="21975-572">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="21975-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span>

1. <span data-ttu-id="21975-573">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="21975-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span>
1. <span data-ttu-id="21975-574">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="21975-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>
1. <span data-ttu-id="21975-575">Eine Alternative zum direkten Festlegen der Umgebung in *web.config* ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="21975-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span>
1. <span data-ttu-id="21975-576">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="21975-576">This approach sets the environment in *web.config* when the project is published:</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="21975-577">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="21975-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

<span data-ttu-id="21975-578">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="21975-578">app_offline.htm</span></span>

1. <span data-ttu-id="21975-579">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="21975-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span>
1. <span data-ttu-id="21975-580">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="21975-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>
1. <span data-ttu-id="21975-581">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="21975-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span>
1. <span data-ttu-id="21975-582">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="21975-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="21975-583">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="21975-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="21975-584">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="21975-584">For example, a websocket connection may delay app shut down.</span></span>

### <a name="module"></a><span data-ttu-id="21975-585">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="21975-585">Start-up error page</span></span>

<span data-ttu-id="21975-586">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="21975-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

* <span data-ttu-id="21975-587">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

* <span data-ttu-id="21975-588">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

* <span data-ttu-id="21975-589">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21975-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

* <span data-ttu-id="21975-590">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="21975-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span>

<span data-ttu-id="21975-591">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="21975-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

* <span data-ttu-id="21975-592">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="21975-592">Log creation and redirection</span></span>

* <span data-ttu-id="21975-593">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="21975-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span>

* <span data-ttu-id="21975-594">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span>

* <span data-ttu-id="21975-595">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="21975-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

### <a name="schema"></a><span data-ttu-id="21975-596">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="21975-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span>

<span data-ttu-id="21975-597">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="21975-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

* <span data-ttu-id="21975-598">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="21975-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

* <span data-ttu-id="21975-599">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="21975-599">Don't use the stdout log for general app logging purposes.</span></span>

<span data-ttu-id="21975-600">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="21975-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span>

* <span data-ttu-id="21975-601">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="21975-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

* <span data-ttu-id="21975-602">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-602">A timestamp and file extension are added automatically when the log file is created.</span></span>

### <a name="configuration"></a><span data-ttu-id="21975-603">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="21975-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span>

<span data-ttu-id="21975-604">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="21975-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

* <span data-ttu-id="21975-605">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="21975-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span>

<span data-ttu-id="21975-606">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="21975-606">After startup, all additional logs are discarded.</span></span>

* <span data-ttu-id="21975-607">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="21975-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span>

* <span data-ttu-id="21975-608">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="21975-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

<span data-ttu-id="21975-609">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="21975-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="21975-610">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="21975-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="21975-611">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="21975-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

* <span data-ttu-id="21975-612">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="21975-612">Enhanced diagnostic logs</span></span>
* <span data-ttu-id="21975-613">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="21975-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span>

<span data-ttu-id="21975-614">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="21975-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span> <span data-ttu-id="21975-615">Ordner im Pfad, die für den `<handlerSetting>`-Wert (*logs* im vorherigen Beispiel) bereitgestellt werden, werden nicht automatisch vom Modul erstellt und müssen bereits in der Bereitstellung vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="21975-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span>

<span data-ttu-id="21975-616">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="21975-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span> <span data-ttu-id="21975-617">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="21975-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span> <span data-ttu-id="21975-618">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="21975-618">Levels (in order from least to most verbose):</span></span>

<span data-ttu-id="21975-619">ERROR</span><span class="sxs-lookup"><span data-stu-id="21975-619">ERROR</span></span>

![WARNING](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="21975-621">INFO</span><span class="sxs-lookup"><span data-stu-id="21975-621">INFO</span></span> <span data-ttu-id="21975-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="21975-622">TRACE</span></span> <span data-ttu-id="21975-623">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="21975-623">Locations (multiple locations are permitted):</span></span>

<span data-ttu-id="21975-624">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="21975-624">CONSOLE</span></span> <span data-ttu-id="21975-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="21975-625">EVENTLOG</span></span> <span data-ttu-id="21975-626">DATEI</span><span class="sxs-lookup"><span data-stu-id="21975-626">FILE</span></span>

<span data-ttu-id="21975-627">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="21975-627">The handler settings can also be provided via environment variables:</span></span> <span data-ttu-id="21975-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="21975-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="21975-629">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="21975-629">(Default: *aspnetcore-debug.log*)</span></span> <span data-ttu-id="21975-630">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="21975-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

<span data-ttu-id="21975-631">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="21975-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="21975-632">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="21975-632">The size of the log isn't limited.</span></span>

<span data-ttu-id="21975-633">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="21975-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

* <span data-ttu-id="21975-634">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="21975-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>
* <span data-ttu-id="21975-635">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="21975-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>
* <span data-ttu-id="21975-636">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="21975-636">*Only applies to out-of-process hosting.*</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="21975-637">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="21975-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span>

<span data-ttu-id="21975-638">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="21975-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="21975-639">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="21975-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span>

<span data-ttu-id="21975-640">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span>

<span data-ttu-id="21975-641">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="21975-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="21975-642">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="21975-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="21975-643">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="21975-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="21975-644">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="21975-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span>

<span data-ttu-id="21975-645">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="21975-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="21975-646">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

| <span data-ttu-id="21975-647">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="21975-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> | <span data-ttu-id="21975-648">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="21975-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span> | <span data-ttu-id="21975-649">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="21975-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="21975-650">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="21975-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span></p><p><span data-ttu-id="21975-651">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="21975-651">Disable the IIS Shared Configuration.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="21975-652">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="21975-652">Run the installer.</span></span></p><p><span data-ttu-id="21975-653">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="21975-653">Export the updated *applicationHost.config* file to the share.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="21975-654">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="21975-654">Re-enable the IIS Shared Configuration.</span></span></p><p><span data-ttu-id="21975-655">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="21975-655">Module version and Hosting Bundle installer logs</span></span> <span data-ttu-id="21975-656">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="21975-656">To determine the version of the installed ASP.NET Core Module:</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="21975-657">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="21975-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span></p><p><span data-ttu-id="21975-658">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="21975-658">Locate the *aspnetcore.dll* file.</span></span></p><p><span data-ttu-id="21975-659">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="21975-659">Right-click the file and select **Properties** from the contextual menu.</span></span> <span data-ttu-id="21975-660">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="21975-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span></p> | <span data-ttu-id="21975-661">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="21975-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span><br><span data-ttu-id="21975-662">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-662">Module, schema, and configuration file locations</span></span><br><span data-ttu-id="21975-663">Modul</span><span class="sxs-lookup"><span data-stu-id="21975-663">Module</span></span> |
| `processPath` | <p><span data-ttu-id="21975-664">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="21975-664">**IIS (x86/amd64):**</span></span></p><p><span data-ttu-id="21975-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span> <span data-ttu-id="21975-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span> <span data-ttu-id="21975-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="21975-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span></p><p><span data-ttu-id="21975-669">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="21975-669">**IIS Express (x86/amd64):**</span></span> <span data-ttu-id="21975-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span></p> | <span data-ttu-id="21975-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="21975-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span><br><span data-ttu-id="21975-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span><br><span data-ttu-id="21975-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="21975-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span> |
| `requestTimeout` | <p><span data-ttu-id="21975-674">Schema</span><span class="sxs-lookup"><span data-stu-id="21975-674">Schema</span></span></p><p><span data-ttu-id="21975-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="21975-675">**IIS**</span></span></p><p><span data-ttu-id="21975-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="21975-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span></p> | <span data-ttu-id="21975-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="21975-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span><br><span data-ttu-id="21975-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21975-678">**IIS Express**</span></span><br><span data-ttu-id="21975-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="21975-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="21975-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="21975-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span></p><p><span data-ttu-id="21975-681">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="21975-681">Configuration</span></span></p> | <span data-ttu-id="21975-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="21975-682">**IIS**</span></span><br><span data-ttu-id="21975-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="21975-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span><br><span data-ttu-id="21975-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21975-684">**IIS Express**</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="21975-685">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="21975-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span></p><p><span data-ttu-id="21975-686">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="21975-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span> <span data-ttu-id="21975-687">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="21975-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span> <span data-ttu-id="21975-688">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um Webanforderungen an Back-End-ASP.NET Core-Apps weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="21975-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span></p><p><span data-ttu-id="21975-689">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="21975-689">Supported Windows versions:</span></span></p> | <span data-ttu-id="21975-690">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-690">Windows 7 or later</span></span><br><span data-ttu-id="21975-691">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="21975-691">Windows Server 2008 R2 or later</span></span><br><span data-ttu-id="21975-692">Das Modul funktioniert nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="21975-692">The module only works with Kestrel.</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="21975-693">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="21975-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span></p><p><span data-ttu-id="21975-694">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul auch Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="21975-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="21975-695">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie abstürzt.</span><span class="sxs-lookup"><span data-stu-id="21975-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span></p><p><span data-ttu-id="21975-696">Dies ist im Prinzip das gleiche Verhalten wie bei ASP.NET 4.x-Apps, die prozessintern in IIS ausgeführt und durch [Windows Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="21975-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span> <span data-ttu-id="21975-697">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer App:</span><span class="sxs-lookup"><span data-stu-id="21975-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span> <span data-ttu-id="21975-698">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="21975-698">ASP.NET Core Module</span></span> <span data-ttu-id="21975-699">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="21975-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="21975-700">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="21975-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="21975-701">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="21975-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="21975-702">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span>

<span data-ttu-id="21975-703">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="21975-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="21975-704">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="21975-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

> [!WARNING]
> <span data-ttu-id="21975-705">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="21975-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="21975-706">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="21975-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>

<span data-ttu-id="21975-707">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="21975-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="21975-708">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="21975-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span> <span data-ttu-id="21975-709">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="21975-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="21975-710">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="21975-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="21975-711">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="21975-711">The ASP.NET Core Module can also:</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="21975-712">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="21975-712">Set environment variables for the worker process.</span></span>

<span data-ttu-id="21975-713">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="21975-713">Log stdout output to file storage for troubleshooting startup issues.</span></span> <span data-ttu-id="21975-714">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="21975-714">Forward Windows authentication tokens.</span></span>

<span data-ttu-id="21975-715">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="21975-715">How to install and use the ASP.NET Core Module</span></span> <span data-ttu-id="21975-716">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="21975-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="21975-717">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="21975-717">Configuration with web.config</span></span>

<span data-ttu-id="21975-718">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="21975-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span> <span data-ttu-id="21975-719">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="21975-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span> <span data-ttu-id="21975-720">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="21975-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

![Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="21975-722">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="21975-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="21975-723">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="21975-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span> <span data-ttu-id="21975-724">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="21975-724">Attributes of the aspNetCore element</span></span> <span data-ttu-id="21975-725">Attribut</span><span class="sxs-lookup"><span data-stu-id="21975-725">Attribute</span></span>

<span data-ttu-id="21975-726">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="21975-726">Description</span></span> <span data-ttu-id="21975-727">Standard</span><span class="sxs-lookup"><span data-stu-id="21975-727">Default</span></span>

<span data-ttu-id="21975-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="21975-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-729">'Blazor'</span></span> <span data-ttu-id="21975-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-730">'Identity'</span></span> <span data-ttu-id="21975-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-731">'Let's Encrypt'</span></span>

<span data-ttu-id="21975-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-732">'Razor'</span></span> <span data-ttu-id="21975-733">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-733">'SignalR' uid:</span></span> <span data-ttu-id="21975-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="21975-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-735">'Blazor'</span></span> <span data-ttu-id="21975-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-736">'Identity'</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="21975-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-737">'Let's Encrypt'</span></span> <span data-ttu-id="21975-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-738">'Razor'</span></span>

<span data-ttu-id="21975-739">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-739">'SignalR' uid:</span></span>

<span data-ttu-id="21975-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="21975-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-741">'Blazor'</span></span>

<span data-ttu-id="21975-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-742">'Identity'</span></span> <span data-ttu-id="21975-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-743">'Let's Encrypt'</span></span>

<span data-ttu-id="21975-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-744">'Razor'</span></span> <span data-ttu-id="21975-745">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-745">'SignalR' uid:</span></span> <span data-ttu-id="21975-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="21975-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-747">'Blazor'</span></span> <span data-ttu-id="21975-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-748">'Identity'</span></span> <span data-ttu-id="21975-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-749">'Let's Encrypt'</span></span> <span data-ttu-id="21975-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-750">'Razor'</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="21975-751">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-751">'SignalR' uid:</span></span>

<span data-ttu-id="21975-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21975-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="21975-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21975-753">'Blazor'</span></span>

<span data-ttu-id="21975-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21975-754">'Identity'</span></span>

1. <span data-ttu-id="21975-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21975-755">'Let's Encrypt'</span></span>
1. <span data-ttu-id="21975-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21975-756">'Razor'</span></span>
1. <span data-ttu-id="21975-757">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21975-757">'SignalR' uid:</span></span>
1. <span data-ttu-id="21975-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="21975-758">------ | :-----: | | `arguments` | </span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="21975-759">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-759">Optional string attribute.</span></span>

<span data-ttu-id="21975-760">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="21975-760">Arguments to the executable specified in **processPath**.</span></span>

1. <span data-ttu-id="21975-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="21975-761">| | | `disableStartUpErrorPage` | </span></span>
1. <span data-ttu-id="21975-762">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-762">Optional Boolean attribute.</span></span>
1. <span data-ttu-id="21975-763">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="21975-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span>
1. <span data-ttu-id="21975-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="21975-764">| `false` | | `forwardWindowsAuthToken` | </span></span>

<span data-ttu-id="21975-765">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-765">Optional Boolean attribute.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="21975-766">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span>

### <a name="module"></a><span data-ttu-id="21975-767">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="21975-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span>

<span data-ttu-id="21975-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="21975-768">| `true` | | `processesPerApplication` | </span></span>

* <span data-ttu-id="21975-769">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-769">Optional integer attribute.</span></span>

* <span data-ttu-id="21975-770">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="21975-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span>

<span data-ttu-id="21975-771">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="21975-771">Setting `processesPerApplication` is discouraged.</span></span>

* <span data-ttu-id="21975-772">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="21975-772">This attribute will be removed in a future release.</span></span>

* <span data-ttu-id="21975-773">| Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-773">| Default: `1`</span></span>

### <a name="schema"></a><span data-ttu-id="21975-774">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="21975-774">Min: `1`</span></span>

<span data-ttu-id="21975-775">Max: `100` | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="21975-775">Max: `100` | | `processPath` | </span></span>

* <span data-ttu-id="21975-776">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-776">Required string attribute.</span></span>

<span data-ttu-id="21975-777">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="21975-777">Path to the executable that launches a process listening for HTTP requests.</span></span>

* <span data-ttu-id="21975-778">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="21975-778">Relative paths are supported.</span></span>

### <a name="configuration"></a><span data-ttu-id="21975-779">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="21975-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span>

<span data-ttu-id="21975-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="21975-780">| | | `rapidFailsPerMinute` | </span></span>

* <span data-ttu-id="21975-781">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="21975-781">Optional integer attribute.</span></span>

<span data-ttu-id="21975-782">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="21975-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span>

* <span data-ttu-id="21975-783">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="21975-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span>

* <span data-ttu-id="21975-784">| Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="21975-784">| Default: `10`</span></span>

<span data-ttu-id="21975-785">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="21975-785">Min: `0`</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="21975-786">Max: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="21975-786">Max: `100` | | `requestTimeout` | </span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="21975-787">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="21975-787">Optional timespan attribute.</span></span>
* <xref:host-and-deploy/iis/modules>
