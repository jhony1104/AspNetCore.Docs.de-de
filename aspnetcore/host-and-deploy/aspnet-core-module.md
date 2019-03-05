---
title: ASP.NET Core-Modul
author: guardrex
description: Erfahren Sie, wie Sie das ASP.NET Core-Modul so konfigurieren, dass es ASP.NET Core-Apps hosten kann.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 302cfb00127c223aeb5e51e4d0a9ef3cb69b10eb
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899371"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="b2d04-103">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="b2d04-103">ASP.NET Core Module</span></span>

<span data-ttu-id="b2d04-104">Von [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2d04-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-105">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="b2d04-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="b2d04-106">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="b2d04-107">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="b2d04-108">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="b2d04-108">Supported Windows versions:</span></span>

* <span data-ttu-id="b2d04-109">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="b2d04-109">Windows 7 or later</span></span>
* <span data-ttu-id="b2d04-110">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="b2d04-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b2d04-111">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b2d04-112">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b2d04-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="b2d04-113">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="b2d04-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b2d04-114">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="b2d04-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b2d04-115">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="b2d04-115">In-process hosting model</span></span>

<span data-ttu-id="b2d04-116">Um eine App für In-Process-Hosting zu konfigurieren, fügen Sie der Projektdatei der App die Eigenschaft `<AspNetCoreHostingModel>` mit dem Wert `InProcess` hinzu (Out-of-Process-Hosting wird mit `OutOfProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="b2d04-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b2d04-117">Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="b2d04-118">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="b2d04-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="b2d04-119">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="b2d04-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="b2d04-120">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b2d04-121">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="b2d04-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="b2d04-122">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="b2d04-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="b2d04-123">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="b2d04-124">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="b2d04-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="b2d04-125">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="b2d04-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="b2d04-126">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="b2d04-127">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="b2d04-127">Use one app pool per app.</span></span>

* <span data-ttu-id="b2d04-128">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b2d04-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b2d04-129">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="b2d04-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="b2d04-130">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="b2d04-131">Wenn der Host der App manuell mit `WebHostBuilder` eingerichtet wird (auf die Verwendung von [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) verzichtet wird) und die App jemals direkt auf dem Kestrel-Server ausgeführt wird (selbstgehostet), rufen Sie `UseKestrel` auf, bevor Sie `UseIISIntegration` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="b2d04-132">Bei umgekehrter Reihenfolge tritt beim Starten des Hosts ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="b2d04-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="b2d04-133">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-133">Client disconnects are detected.</span></span> <span data-ttu-id="b2d04-134">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="b2d04-135">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="b2d04-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="b2d04-136">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="b2d04-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="b2d04-137">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="b2d04-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="b2d04-138">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="b2d04-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>
  
* <span data-ttu-id="b2d04-139">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="b2d04-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="b2d04-140">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b2d04-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="b2d04-141">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="b2d04-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b2d04-142">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="b2d04-142">Out-of-process hosting model</span></span>

<span data-ttu-id="b2d04-143">Um eine App für Out-of-Process-Hosting zu konfigurieren, konfigurieren Sie die Projektdatei auf eine der folgenden Weisen:</span><span class="sxs-lookup"><span data-stu-id="b2d04-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="b2d04-144">Geben Sie die `<AspNetCoreHostingModel>`-Eigenschaft nicht an.</span><span class="sxs-lookup"><span data-stu-id="b2d04-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="b2d04-145">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="b2d04-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="b2d04-146">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft auf `OutOfProcess` fest (In-Process-Hosting wird mit `InProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="b2d04-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b2d04-147">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b2d04-148">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="b2d04-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="b2d04-149">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="b2d04-150">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="b2d04-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="b2d04-151">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="b2d04-151">Hosting model changes</span></span>

<span data-ttu-id="b2d04-152">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="b2d04-153">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="b2d04-154">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="b2d04-155">Prozessname</span><span class="sxs-lookup"><span data-stu-id="b2d04-155">Process name</span></span>

<span data-ttu-id="b2d04-156">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="b2d04-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b2d04-157">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um Webanforderungen an Back-End-ASP.NET Core-Apps weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="b2d04-158">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="b2d04-158">Supported Windows versions:</span></span>

* <span data-ttu-id="b2d04-159">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="b2d04-159">Windows 7 or later</span></span>
* <span data-ttu-id="b2d04-160">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="b2d04-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b2d04-161">Das Modul funktioniert nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b2d04-161">The module only works with Kestrel.</span></span> <span data-ttu-id="b2d04-162">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="b2d04-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="b2d04-163">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul auch Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="b2d04-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="b2d04-164">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie abstürzt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="b2d04-165">Dies ist im Prinzip das gleiche Verhalten wie bei ASP.NET 4.x-Apps, die prozessintern in IIS ausgeführt und durch [Windows Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b2d04-166">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer App:</span><span class="sxs-lookup"><span data-stu-id="b2d04-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core-Modul](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="b2d04-168">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b2d04-169">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b2d04-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b2d04-170">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b2d04-171">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-171">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="b2d04-172">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b2d04-173">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b2d04-174">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b2d04-175">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="b2d04-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b2d04-176">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b2d04-177">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="b2d04-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="b2d04-178">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="b2d04-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b2d04-179">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="b2d04-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b2d04-180">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="b2d04-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b2d04-181">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="b2d04-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b2d04-182">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="b2d04-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b2d04-183">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="b2d04-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b2d04-184">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="b2d04-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b2d04-185">Anweisungen zum Installieren und Verwenden des ASP.NET Core-Moduls finden Sie unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="b2d04-185">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b2d04-186">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="b2d04-186">Configuration with web.config</span></span>

<span data-ttu-id="b2d04-187">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2d04-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b2d04-188">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="b2d04-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

<span data-ttu-id="b2d04-189">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="b2d04-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b2d04-190">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b2d04-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

<span data-ttu-id="b2d04-191">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b2d04-192">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b2d04-193">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="b2d04-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b2d04-194">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="b2d04-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="b2d04-195">Attribut</span><span class="sxs-lookup"><span data-stu-id="b2d04-195">Attribute</span></span> | <span data-ttu-id="b2d04-196">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b2d04-196">Description</span></span> | <span data-ttu-id="b2d04-197">Standard</span><span class="sxs-lookup"><span data-stu-id="b2d04-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b2d04-198">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-198">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-199">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b2d04-200">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-201">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="b2d04-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b2d04-202">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-203">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b2d04-204">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="b2d04-205">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-205">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-206">Gibt das Hostingmodell als In-Process (`InProcess`) oder Out-of-Process (`OutOfProcess`) an.</span><span class="sxs-lookup"><span data-stu-id="b2d04-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="b2d04-207">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-208">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="b2d04-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b2d04-209">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="b2d04-210">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b2d04-211">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b2d04-212">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-212">Default: `1`</span></span><br><span data-ttu-id="b2d04-213">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-213">Min: `1`</span></span><br><span data-ttu-id="b2d04-214">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="b2d04-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="b2d04-215">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-215">Required string attribute.</span></span></p><p><span data-ttu-id="b2d04-216">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b2d04-217">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-217">Relative paths are supported.</span></span> <span data-ttu-id="b2d04-218">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="b2d04-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b2d04-219">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-220">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="b2d04-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b2d04-221">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="b2d04-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="b2d04-222">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="b2d04-223">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-223">Default: `10`</span></span><br><span data-ttu-id="b2d04-224">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-224">Min: `0`</span></span><br><span data-ttu-id="b2d04-225">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="b2d04-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b2d04-226">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b2d04-227">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b2d04-228">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="b2d04-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="b2d04-229">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="b2d04-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="b2d04-230">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-230">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="b2d04-231">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-231">Default: `00:02:00`</span></span><br><span data-ttu-id="b2d04-232">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-232">Min: `00:00:00`</span></span><br><span data-ttu-id="b2d04-233">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-233">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b2d04-234">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-235">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-235">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b2d04-236">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-236">Default: `10`</span></span><br><span data-ttu-id="b2d04-237">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-237">Min: `0`</span></span><br><span data-ttu-id="b2d04-238">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-238">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b2d04-239">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-240">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-240">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b2d04-241">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="b2d04-241">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="b2d04-242">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-242">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b2d04-243">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-243">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b2d04-244">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="b2d04-244">Default: `120`</span></span><br><span data-ttu-id="b2d04-245">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-245">Min: `0`</span></span><br><span data-ttu-id="b2d04-246">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-246">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b2d04-247">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-247">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-248">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-248">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b2d04-249">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-249">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-250">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-250">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b2d04-251">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="b2d04-251">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b2d04-252">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-252">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b2d04-253">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-253">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="b2d04-254">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-254">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b2d04-255">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-255">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="b2d04-256">Attribut</span><span class="sxs-lookup"><span data-stu-id="b2d04-256">Attribute</span></span> | <span data-ttu-id="b2d04-257">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b2d04-257">Description</span></span> | <span data-ttu-id="b2d04-258">Standard</span><span class="sxs-lookup"><span data-stu-id="b2d04-258">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b2d04-259">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-259">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-260">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-260">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b2d04-261">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-261">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-262">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="b2d04-262">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b2d04-263">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-264">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-264">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b2d04-265">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-265">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="b2d04-266">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-266">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-267">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="b2d04-267">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b2d04-268">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-268">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b2d04-269">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-269">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b2d04-270">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-270">Default: `1`</span></span><br><span data-ttu-id="b2d04-271">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-271">Min: `1`</span></span><br><span data-ttu-id="b2d04-272">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="b2d04-272">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="b2d04-273">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-273">Required string attribute.</span></span></p><p><span data-ttu-id="b2d04-274">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-274">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b2d04-275">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-275">Relative paths are supported.</span></span> <span data-ttu-id="b2d04-276">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="b2d04-276">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b2d04-277">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-277">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-278">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="b2d04-278">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b2d04-279">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="b2d04-279">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="b2d04-280">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-280">Default: `10`</span></span><br><span data-ttu-id="b2d04-281">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-281">Min: `0`</span></span><br><span data-ttu-id="b2d04-282">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="b2d04-282">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b2d04-283">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-283">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b2d04-284">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-284">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b2d04-285">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="b2d04-285">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="b2d04-286">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-286">Default: `00:02:00`</span></span><br><span data-ttu-id="b2d04-287">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-287">Min: `00:00:00`</span></span><br><span data-ttu-id="b2d04-288">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-288">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b2d04-289">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-289">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-290">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-290">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b2d04-291">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-291">Default: `10`</span></span><br><span data-ttu-id="b2d04-292">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-292">Min: `0`</span></span><br><span data-ttu-id="b2d04-293">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-293">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b2d04-294">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-294">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-295">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-295">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b2d04-296">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="b2d04-296">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="b2d04-297">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-297">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b2d04-298">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-298">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b2d04-299">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="b2d04-299">Default: `120`</span></span><br><span data-ttu-id="b2d04-300">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-300">Min: `0`</span></span><br><span data-ttu-id="b2d04-301">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-301">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b2d04-302">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-302">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-303">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-303">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b2d04-304">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-304">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-305">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-305">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b2d04-306">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="b2d04-306">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b2d04-307">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-307">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b2d04-308">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-308">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="b2d04-309">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-309">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b2d04-310">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-310">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="b2d04-311">Attribut</span><span class="sxs-lookup"><span data-stu-id="b2d04-311">Attribute</span></span> | <span data-ttu-id="b2d04-312">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b2d04-312">Description</span></span> | <span data-ttu-id="b2d04-313">Standard</span><span class="sxs-lookup"><span data-stu-id="b2d04-313">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b2d04-314">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-314">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-315">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-315">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b2d04-316">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-316">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-317">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="b2d04-317">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b2d04-318">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-318">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-319">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-319">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b2d04-320">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-320">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="b2d04-321">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-321">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-322">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="b2d04-322">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b2d04-323">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-323">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b2d04-324">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-324">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b2d04-325">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-325">Default: `1`</span></span><br><span data-ttu-id="b2d04-326">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="b2d04-326">Min: `1`</span></span><br><span data-ttu-id="b2d04-327">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="b2d04-327">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="b2d04-328">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-328">Required string attribute.</span></span></p><p><span data-ttu-id="b2d04-329">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-329">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b2d04-330">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-330">Relative paths are supported.</span></span> <span data-ttu-id="b2d04-331">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="b2d04-331">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b2d04-332">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-333">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="b2d04-333">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b2d04-334">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="b2d04-334">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="b2d04-335">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-335">Default: `10`</span></span><br><span data-ttu-id="b2d04-336">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-336">Min: `0`</span></span><br><span data-ttu-id="b2d04-337">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="b2d04-337">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b2d04-338">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-338">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b2d04-339">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-339">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b2d04-340">In den Versionen des ASP.NET Core-Moduls, die im Lieferumfang der Version ASP.NET Core 2.0 oder früher enthalten waren, darf der `requestTimeout` nur in ganzen Minuten angegeben werden. Andernfalls wird standardmäßig der Wert 2 Minuten angenommen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-340">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="b2d04-341">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-341">Default: `00:02:00`</span></span><br><span data-ttu-id="b2d04-342">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-342">Min: `00:00:00`</span></span><br><span data-ttu-id="b2d04-343">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b2d04-343">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b2d04-344">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-344">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-345">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-345">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b2d04-346">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="b2d04-346">Default: `10`</span></span><br><span data-ttu-id="b2d04-347">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-347">Min: `0`</span></span><br><span data-ttu-id="b2d04-348">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-348">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b2d04-349">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-349">Optional integer attribute.</span></span></p><p><span data-ttu-id="b2d04-350">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-350">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b2d04-351">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="b2d04-351">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="b2d04-352">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-352">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="b2d04-353">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="b2d04-353">Default: `120`</span></span><br><span data-ttu-id="b2d04-354">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="b2d04-354">Min: `0`</span></span><br><span data-ttu-id="b2d04-355">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="b2d04-355">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b2d04-356">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-356">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b2d04-357">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-357">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b2d04-358">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-358">Optional string attribute.</span></span></p><p><span data-ttu-id="b2d04-359">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-359">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b2d04-360">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="b2d04-360">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b2d04-361">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-361">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b2d04-362">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-362">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="b2d04-363">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-363">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b2d04-364">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="b2d04-364">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="b2d04-365">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="b2d04-365">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2d04-366">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-366">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b2d04-367">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="b2d04-367">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="b2d04-368">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-368">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2d04-369">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-369">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b2d04-370">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="b2d04-370">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="b2d04-371">In diesem Abschnitt festgelegte Umgebungsvariablen stehen im Konflikt mit Systemumgebungsvariablen gleichen Namens.</span><span class="sxs-lookup"><span data-stu-id="b2d04-371">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="b2d04-372">Wenn eine Umgebungsvariable sowohl in der Datei *web.config* als auch auf Systemebene in Windows festgelegt ist, wird der Wert aus der Datei *web.config* dem Wert der Systemumgebungsvariablen angefügt (z.B. `ASPNETCORE_ENVIRONMENT: Development;Development`), um zu verhindern, dass die App startet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-372">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="b2d04-373">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-373">The following example sets two environment variables.</span></span> <span data-ttu-id="b2d04-374">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="b2d04-374">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b2d04-375">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-375">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b2d04-376">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-376">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="b2d04-377">Eine Alternative zum direkten Festlegen der Umgebung in *web.config* ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="b2d04-377">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="b2d04-378">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="b2d04-378">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="b2d04-379">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="b2d04-379">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="b2d04-380">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="b2d04-380">app_offline.htm</span></span>

<span data-ttu-id="b2d04-381">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-381">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b2d04-382">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="b2d04-382">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b2d04-383">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-383">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="b2d04-384">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-384">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-385">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-385">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b2d04-386">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="b2d04-386">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="b2d04-387">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="b2d04-387">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-388">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="b2d04-388">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="b2d04-389">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-389">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="b2d04-390">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-390">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="b2d04-391">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-391">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="b2d04-392">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="b2d04-392">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b2d04-393">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="b2d04-393">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b2d04-394">Wenn das ASP.NET Core-Modul den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-394">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="b2d04-395">Um diese Seite zu unterdrücken und zur IIS-502-Sandardstatuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="b2d04-395">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b2d04-396">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="b2d04-396">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Statuscodeseite „502.5: Prozessfehler“](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b2d04-398">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="b2d04-398">Log creation and redirection</span></span>

<span data-ttu-id="b2d04-399">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-399">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b2d04-400">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-400">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b2d04-401">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="b2d04-401">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="b2d04-402">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-402">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b2d04-403">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-403">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b2d04-404">Die Verwendung des „stdout“-Protokolls wird zur Problembehandlung bei Startproblemen der App empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-404">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="b2d04-405">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="b2d04-405">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b2d04-406">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="b2d04-406">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b2d04-407">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="b2d04-407">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b2d04-408">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-408">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b2d04-409">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung (*.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-409">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="b2d04-410">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="b2d04-410">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-411">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="b2d04-411">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="b2d04-412">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-412">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="b2d04-413">Das folgende `aspNetCore`-Beispielelement konfiguriert die stdout-Protokollierung für eine in Azure App Service gehostete App.</span><span class="sxs-lookup"><span data-stu-id="b2d04-413">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="b2d04-414">Ein lokaler Pfad oder ein Netzwerkfreigabepfad ist für die lokale Protokollierung zulässig.</span><span class="sxs-lookup"><span data-stu-id="b2d04-414">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="b2d04-415">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-415">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="b2d04-416">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="b2d04-416">Enhanced diagnostic logs</span></span>

<span data-ttu-id="b2d04-417">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-417">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="b2d04-418">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="b2d04-418">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b2d04-419">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-419">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="b2d04-420">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="b2d04-420">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="b2d04-421">ERROR</span><span class="sxs-lookup"><span data-stu-id="b2d04-421">ERROR</span></span>
* <span data-ttu-id="b2d04-422">WARNING</span><span class="sxs-lookup"><span data-stu-id="b2d04-422">WARNING</span></span>
* <span data-ttu-id="b2d04-423">INFO</span><span class="sxs-lookup"><span data-stu-id="b2d04-423">INFO</span></span>
* <span data-ttu-id="b2d04-424">TRACE</span><span class="sxs-lookup"><span data-stu-id="b2d04-424">TRACE</span></span>

<span data-ttu-id="b2d04-425">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="b2d04-425">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="b2d04-426">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="b2d04-426">CONSOLE</span></span>
* <span data-ttu-id="b2d04-427">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="b2d04-427">EVENTLOG</span></span>
* <span data-ttu-id="b2d04-428">DATEI</span><span class="sxs-lookup"><span data-stu-id="b2d04-428">FILE</span></span>

<span data-ttu-id="b2d04-429">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="b2d04-429">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="b2d04-430">`ASPNETCORE_MODULE_DEBUG_FILE`: Der Pfad zur Debugprotokolldatei</span><span class="sxs-lookup"><span data-stu-id="b2d04-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="b2d04-431">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="b2d04-431">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="b2d04-432">`ASPNETCORE_MODULE_DEBUG`: Einstellung der Debugebene</span><span class="sxs-lookup"><span data-stu-id="b2d04-432">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="b2d04-433">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="b2d04-433">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="b2d04-434">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-434">The size of the log isn't limited.</span></span> <span data-ttu-id="b2d04-435">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-435">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="b2d04-436">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="b2d04-436">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b2d04-437">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="b2d04-437">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-438">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="b2d04-438">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="b2d04-439">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="b2d04-439">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b2d04-440">Die Verwendung von HTTP ist eine Leistungsoptimierung, bei der der Datenverkehr zwischen dem Modul und Kestrel in einer Loopbackadresse außerhalb der Netzwerkschnittstelle stattfindet.</span><span class="sxs-lookup"><span data-stu-id="b2d04-440">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="b2d04-441">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="b2d04-441">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b2d04-442">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-442">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b2d04-443">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-443">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b2d04-444">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-444">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b2d04-445">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="b2d04-445">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b2d04-446">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-446">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b2d04-447">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-447">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b2d04-448">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-448">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b2d04-449">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="b2d04-449">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b2d04-450">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b2d04-450">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="b2d04-451">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="b2d04-451">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2d04-452">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="b2d04-452">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="b2d04-453">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="b2d04-453">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="b2d04-454">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="b2d04-454">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b2d04-455">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-455">Run the installer.</span></span>
1. <span data-ttu-id="b2d04-456">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="b2d04-456">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="b2d04-457">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-457">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b2d04-458">Wenn Sie eine IIS-Freigabekonfiguration verwenden, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b2d04-458">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="b2d04-459">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="b2d04-459">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b2d04-460">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-460">Run the installer.</span></span>
1. <span data-ttu-id="b2d04-461">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="b2d04-461">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="b2d04-462">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="b2d04-462">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a><span data-ttu-id="b2d04-463">Anwendungsinitialisierung</span><span class="sxs-lookup"><span data-stu-id="b2d04-463">Application Initialization</span></span>

<span data-ttu-id="b2d04-464">[IIS-Anwendungsinitialisierung](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) ist ein IIS-Feature, das eine HTTP-Anforderung an die App sendet, wenn der App-Pool startet oder wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b2d04-464">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="b2d04-465">Die Anforderung löst den Start der App aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-465">The request triggers the app to start.</span></span> <span data-ttu-id="b2d04-466">Anwendungsinitialisierung kann sowohl vom [In-Process-Hostmodell](xref:fundamentals/servers/index#in-process-hosting-model) als auch vom [Out-of-Process-Hostmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) mit ASP.NET Core-Modul Version 2 verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b2d04-466">Application Initialization can be used by both the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model) and [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) with the ASP.NET Core Module version 2.</span></span>

<span data-ttu-id="b2d04-467">So aktivieren Sie die Anwendungsinitialisierung:</span><span class="sxs-lookup"><span data-stu-id="b2d04-467">To enable Application Initialization:</span></span>

1. <span data-ttu-id="b2d04-468">Vergewissern Sie sich, dass die IIS-Anwendungsinitialisierungs-Rollenfunktion aktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="b2d04-468">Confirm that the IIS Application Initialization role feature in enabled:</span></span>
   * <span data-ttu-id="b2d04-469">In Windows 7 oder höher: Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="b2d04-469">On Windows 7 or later: Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="b2d04-470">Öffnen Sie **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="b2d04-470">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="b2d04-471">Aktivieren Sie das Kontrollkästchen für **Anwendungsinitialisierung**.</span><span class="sxs-lookup"><span data-stu-id="b2d04-471">Select the check box for **Application Initialization**.</span></span>
   * <span data-ttu-id="b2d04-472">Öffnen Sie in Windows Server 2008 R2 oder höher den **Assistenten zum Hinzufügen von Rollen und Features**.</span><span class="sxs-lookup"><span data-stu-id="b2d04-472">On Windows Server 2008 R2 or later, open the **Add Roles and Features Wizard**.</span></span> <span data-ttu-id="b2d04-473">Wenn Sie den Bereich **Rollendienste auswählen** erreichen, öffnen Sie den Knoten **Anwendungsentwicklung**, und aktivieren Sie das **Kontrollkästchen Anwendungsinitialisierung**.</span><span class="sxs-lookup"><span data-stu-id="b2d04-473">When you reach the **Select role services** panel, open the **Application Development** node and select the **Application Initialization** check box.</span></span>
1. <span data-ttu-id="b2d04-474">Wählen Sie im IIS-Manager **Anwendungspools** im Bereich **Verbindungen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-474">In IIS Manager, select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="b2d04-475">Wählen Sie in der Liste den App-Pool der App aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-475">Select the app's app pool in the list.</span></span>
1. <span data-ttu-id="b2d04-476">Wählen Sie im Bereich **Aktionen** unter **Anwendungspool bearbeiten** **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-476">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="b2d04-477">Legen Sie für **Startmodus** **AlwaysRunning** fest.</span><span class="sxs-lookup"><span data-stu-id="b2d04-477">Set **Start Mode** to **AlwaysRunning**.</span></span>
1. <span data-ttu-id="b2d04-478">Öffnen Sie den Knoten **Websites** im Bereich **Verbindungen**.</span><span class="sxs-lookup"><span data-stu-id="b2d04-478">Open the **Sites** node in the **Connections** panel.</span></span>
1. <span data-ttu-id="b2d04-479">Wählen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-479">Select the app.</span></span>
1. <span data-ttu-id="b2d04-480">Wählen Sie im Bereich **Aktionen** unter **Website verwalten** **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-480">Select **Advanced Settings** under **Manage Website** in the **Actions** panel.</span></span>
1. <span data-ttu-id="b2d04-481">Legen Sie für **Vorabladen aktiviert** **True** fest.</span><span class="sxs-lookup"><span data-stu-id="b2d04-481">Set **Preload Enabled** to **True**.</span></span>

<span data-ttu-id="b2d04-482">Weitere Informationen finden Sie unter [IIS 8.0 Anwendungsinitialisierung](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span><span class="sxs-lookup"><span data-stu-id="b2d04-482">For more information, see [IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span></span>

<span data-ttu-id="b2d04-483">Apps, die das [Out-of-Process-Hostmodell](xref:fundamentals/servers/index#out-of-process-hosting-model) verwenden, müssen einen externen Dienst verwenden, um die App in regelmäßigen Abständen zu pingen, um ihre Ausführung aufrechtzuerhalten.</span><span class="sxs-lookup"><span data-stu-id="b2d04-483">Apps that use the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) must use an external service to periodically ping the app in order to keep it running.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b2d04-484">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="b2d04-484">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b2d04-485">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="b2d04-485">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b2d04-486">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="b2d04-486">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="b2d04-487">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="b2d04-487">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="b2d04-488">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="b2d04-488">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b2d04-489">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="b2d04-489">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b2d04-490">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<Zeitstempel>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="b2d04-490">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b2d04-491">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b2d04-491">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b2d04-492">Modul</span><span class="sxs-lookup"><span data-stu-id="b2d04-492">Module</span></span>

<span data-ttu-id="b2d04-493">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b2d04-493">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="b2d04-494">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-494">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="b2d04-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="b2d04-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="b2d04-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="b2d04-498">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b2d04-498">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="b2d04-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="b2d04-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="b2d04-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="b2d04-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="b2d04-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="b2d04-503">Schema</span><span class="sxs-lookup"><span data-stu-id="b2d04-503">Schema</span></span>

<span data-ttu-id="b2d04-504">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b2d04-504">**IIS**</span></span>

   * <span data-ttu-id="b2d04-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b2d04-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="b2d04-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="b2d04-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="b2d04-507">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b2d04-507">**IIS Express**</span></span>

   * <span data-ttu-id="b2d04-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b2d04-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="b2d04-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="b2d04-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="b2d04-510">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b2d04-510">Configuration</span></span>

<span data-ttu-id="b2d04-511">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b2d04-511">**IIS**</span></span>

   * <span data-ttu-id="b2d04-512">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="b2d04-512">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="b2d04-513">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b2d04-513">**IIS Express**</span></span>

   * <span data-ttu-id="b2d04-514">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="b2d04-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>
   
   * <span data-ttu-id="b2d04-515">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="b2d04-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="b2d04-516">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="b2d04-516">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2d04-517">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b2d04-517">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="b2d04-518">GitHub-Repository für ASP.NET Core-Modul (Referenzquelle)</span><span class="sxs-lookup"><span data-stu-id="b2d04-518">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
