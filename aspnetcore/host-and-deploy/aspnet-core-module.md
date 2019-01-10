---
title: ASP.NET Core-Modul
author: guardrex
description: Erfahren Sie, wie Sie das ASP.NET Core-Modul so konfigurieren, dass es ASP.NET Core-Apps hosten kann.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: dee4fe7a498d211cb8ef6a3c49017c3cc8a56847
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637858"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="518e0-103">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="518e0-103">ASP.NET Core Module</span></span>

<span data-ttu-id="518e0-104">Von [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="518e0-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="518e0-105">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="518e0-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="518e0-106">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="518e0-107">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="518e0-108">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="518e0-108">Supported Windows versions:</span></span>

* <span data-ttu-id="518e0-109">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="518e0-109">Windows 7 or later</span></span>
* <span data-ttu-id="518e0-110">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="518e0-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="518e0-111">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="518e0-112">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="518e0-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="518e0-113">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="518e0-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="518e0-114">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="518e0-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="518e0-115">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="518e0-115">In-process hosting model</span></span>

<span data-ttu-id="518e0-116">Um eine App für In-Process-Hosting zu konfigurieren, fügen Sie der Projektdatei der App die Eigenschaft `<AspNetCoreHostingModel>` mit dem Wert `InProcess` hinzu (Out-of-Process-Hosting wird mit `OutOfProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="518e0-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="518e0-117">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="518e0-117">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="518e0-118">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="518e0-118">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="518e0-119">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="518e0-119">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

* <span data-ttu-id="518e0-120">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="518e0-120">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="518e0-121">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="518e0-121">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="518e0-122">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="518e0-122">Use one app pool per app.</span></span>

* <span data-ttu-id="518e0-123">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="518e0-123">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="518e0-124">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="518e0-124">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="518e0-125">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="518e0-125">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="518e0-126">Wenn der Host der App manuell mit `WebHostBuilder` eingerichtet wird (auf die Verwendung von [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) verzichtet wird) und die App jemals direkt auf dem Kestrel-Server ausgeführt wird (selbstgehostet), rufen Sie `UseKestrel` auf, bevor Sie `UseIISIntegration` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="518e0-126">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="518e0-127">Bei umgekehrter Reihenfolge tritt beim Starten des Hosts ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="518e0-127">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="518e0-128">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="518e0-128">Client disconnects are detected.</span></span> <span data-ttu-id="518e0-129">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-129">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="518e0-130"><xref:System.IO.Directory.GetCurrentDirectory*> gibt anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="518e0-130"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="518e0-131">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="518e0-131">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="518e0-132">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="518e0-132">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="518e0-133">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="518e0-133">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="518e0-134">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="518e0-134">Out-of-process hosting model</span></span>

<span data-ttu-id="518e0-135">Um eine App für Out-of-Process-Hosting zu konfigurieren, konfigurieren Sie die Projektdatei auf eine der folgenden Weisen:</span><span class="sxs-lookup"><span data-stu-id="518e0-135">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="518e0-136">Geben Sie die `<AspNetCoreHostingModel>`-Eigenschaft nicht an.</span><span class="sxs-lookup"><span data-stu-id="518e0-136">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="518e0-137">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="518e0-137">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="518e0-138">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft auf `OutOfProcess` fest (In-Process-Hosting wird mit `InProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="518e0-138">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="518e0-139">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="518e0-139">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="518e0-140">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="518e0-140">Hosting model changes</span></span>

<span data-ttu-id="518e0-141">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="518e0-141">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="518e0-142">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="518e0-142">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="518e0-143">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="518e0-143">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="518e0-144">Prozessname</span><span class="sxs-lookup"><span data-stu-id="518e0-144">Process name</span></span>

<span data-ttu-id="518e0-145">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="518e0-145">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="518e0-146">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um Webanforderungen an Back-End-ASP.NET Core-Apps weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="518e0-146">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="518e0-147">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="518e0-147">Supported Windows versions:</span></span>

* <span data-ttu-id="518e0-148">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="518e0-148">Windows 7 or later</span></span>
* <span data-ttu-id="518e0-149">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="518e0-149">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="518e0-150">Das Modul funktioniert nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="518e0-150">The module only works with Kestrel.</span></span> <span data-ttu-id="518e0-151">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="518e0-151">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="518e0-152">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul auch Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="518e0-152">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="518e0-153">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie abstürzt.</span><span class="sxs-lookup"><span data-stu-id="518e0-153">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="518e0-154">Dies ist im Prinzip das gleiche Verhalten wie bei ASP.NET 4.x-Apps, die prozessintern in IIS ausgeführt und durch [Windows Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="518e0-154">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="518e0-155">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer App:</span><span class="sxs-lookup"><span data-stu-id="518e0-155">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core-Modul](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="518e0-157">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-157">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="518e0-158">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="518e0-158">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="518e0-159">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="518e0-159">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="518e0-160">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-160">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="518e0-161">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="518e0-161">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="518e0-162">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="518e0-162">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="518e0-163">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-163">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="518e0-164">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="518e0-164">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="518e0-165">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="518e0-165">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="518e0-166">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="518e0-166">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="518e0-167">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="518e0-167">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="518e0-168">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="518e0-168">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="518e0-169">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="518e0-169">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="518e0-170">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="518e0-170">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="518e0-171">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="518e0-171">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="518e0-172">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="518e0-172">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="518e0-173">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="518e0-173">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="518e0-174">Anweisungen zum Installieren und Verwenden des ASP.NET Core-Moduls finden Sie unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="518e0-174">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="518e0-175">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="518e0-175">Configuration with web.config</span></span>

<span data-ttu-id="518e0-176">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="518e0-176">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="518e0-177">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="518e0-177">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="518e0-178">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="518e0-178">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="518e0-179">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="518e0-179">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="518e0-180">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="518e0-180">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="518e0-181">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-181">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="518e0-182">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="518e0-182">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="518e0-183">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="518e0-183">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="518e0-184">Attribut</span><span class="sxs-lookup"><span data-stu-id="518e0-184">Attribute</span></span> | <span data-ttu-id="518e0-185">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="518e0-185">Description</span></span> | <span data-ttu-id="518e0-186">Standard</span><span class="sxs-lookup"><span data-stu-id="518e0-186">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="518e0-187">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-187">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-188">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-188">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="518e0-189">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-189">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-190">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="518e0-190">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="518e0-191">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-192">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-192">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="518e0-193">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="518e0-193">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="518e0-194">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-194">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-195">Gibt das Hostingmodell als In-Process (`InProcess`) oder Out-of-Process (`OutOfProcess`) an.</span><span class="sxs-lookup"><span data-stu-id="518e0-195">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="518e0-196">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-196">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-197">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="518e0-197">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="518e0-198">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="518e0-198">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="518e0-199">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-199">Default: `1`</span></span><br><span data-ttu-id="518e0-200">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-200">Min: `1`</span></span><br><span data-ttu-id="518e0-201">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="518e0-201">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="518e0-202">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-202">Required string attribute.</span></span></p><p><span data-ttu-id="518e0-203">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-203">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="518e0-204">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="518e0-204">Relative paths are supported.</span></span> <span data-ttu-id="518e0-205">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="518e0-205">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="518e0-206">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-206">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-207">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="518e0-207">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="518e0-208">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="518e0-208">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="518e0-209">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="518e0-209">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="518e0-210">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-210">Default: `10`</span></span><br><span data-ttu-id="518e0-211">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-211">Min: `0`</span></span><br><span data-ttu-id="518e0-212">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="518e0-212">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="518e0-213">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-213">Optional timespan attribute.</span></span></p><p><span data-ttu-id="518e0-214">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-214">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="518e0-215">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="518e0-215">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="518e0-216">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="518e0-216">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="518e0-217">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="518e0-217">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="518e0-218">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-218">Default: `00:02:00`</span></span><br><span data-ttu-id="518e0-219">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-219">Min: `00:00:00`</span></span><br><span data-ttu-id="518e0-220">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-220">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="518e0-221">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-222">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-222">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="518e0-223">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-223">Default: `10`</span></span><br><span data-ttu-id="518e0-224">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-224">Min: `0`</span></span><br><span data-ttu-id="518e0-225">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="518e0-225">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="518e0-226">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-226">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-227">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-227">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="518e0-228">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="518e0-228">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="518e0-229">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="518e0-229">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="518e0-230">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="518e0-230">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="518e0-231">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="518e0-231">Default: `120`</span></span><br><span data-ttu-id="518e0-232">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-232">Min: `0`</span></span><br><span data-ttu-id="518e0-233">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="518e0-233">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="518e0-234">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-234">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-235">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-235">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="518e0-236">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-236">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-237">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="518e0-237">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="518e0-238">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="518e0-238">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="518e0-239">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="518e0-239">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="518e0-240">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-240">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="518e0-241">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="518e0-241">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="518e0-242">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-242">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="518e0-243">Attribut</span><span class="sxs-lookup"><span data-stu-id="518e0-243">Attribute</span></span> | <span data-ttu-id="518e0-244">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="518e0-244">Description</span></span> | <span data-ttu-id="518e0-245">Standard</span><span class="sxs-lookup"><span data-stu-id="518e0-245">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="518e0-246">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-246">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-247">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-247">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="518e0-248">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-248">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-249">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="518e0-249">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="518e0-250">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-250">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-251">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-251">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="518e0-252">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="518e0-252">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="518e0-253">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-253">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-254">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="518e0-254">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="518e0-255">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-255">Default: `1`</span></span><br><span data-ttu-id="518e0-256">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-256">Min: `1`</span></span><br><span data-ttu-id="518e0-257">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="518e0-257">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="518e0-258">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-258">Required string attribute.</span></span></p><p><span data-ttu-id="518e0-259">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-259">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="518e0-260">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="518e0-260">Relative paths are supported.</span></span> <span data-ttu-id="518e0-261">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="518e0-261">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="518e0-262">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-262">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-263">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="518e0-263">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="518e0-264">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="518e0-264">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="518e0-265">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-265">Default: `10`</span></span><br><span data-ttu-id="518e0-266">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-266">Min: `0`</span></span><br><span data-ttu-id="518e0-267">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="518e0-267">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="518e0-268">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-268">Optional timespan attribute.</span></span></p><p><span data-ttu-id="518e0-269">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-269">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="518e0-270">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="518e0-270">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="518e0-271">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-271">Default: `00:02:00`</span></span><br><span data-ttu-id="518e0-272">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-272">Min: `00:00:00`</span></span><br><span data-ttu-id="518e0-273">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-273">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="518e0-274">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-274">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-275">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-275">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="518e0-276">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-276">Default: `10`</span></span><br><span data-ttu-id="518e0-277">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-277">Min: `0`</span></span><br><span data-ttu-id="518e0-278">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="518e0-278">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="518e0-279">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-279">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-280">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-280">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="518e0-281">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="518e0-281">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="518e0-282">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="518e0-282">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="518e0-283">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="518e0-283">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="518e0-284">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="518e0-284">Default: `120`</span></span><br><span data-ttu-id="518e0-285">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-285">Min: `0`</span></span><br><span data-ttu-id="518e0-286">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="518e0-286">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="518e0-287">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-287">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-288">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-288">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="518e0-289">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-289">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-290">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="518e0-290">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="518e0-291">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="518e0-291">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="518e0-292">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="518e0-292">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="518e0-293">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-293">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="518e0-294">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="518e0-294">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="518e0-295">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-295">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="518e0-296">Attribut</span><span class="sxs-lookup"><span data-stu-id="518e0-296">Attribute</span></span> | <span data-ttu-id="518e0-297">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="518e0-297">Description</span></span> | <span data-ttu-id="518e0-298">Standard</span><span class="sxs-lookup"><span data-stu-id="518e0-298">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="518e0-299">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-299">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-300">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-300">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="518e0-301">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-301">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-302">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="518e0-302">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="518e0-303">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-303">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-304">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-304">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="518e0-305">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="518e0-305">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="518e0-306">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-306">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-307">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="518e0-307">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="518e0-308">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-308">Default: `1`</span></span><br><span data-ttu-id="518e0-309">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="518e0-309">Min: `1`</span></span><br><span data-ttu-id="518e0-310">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="518e0-310">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="518e0-311">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-311">Required string attribute.</span></span></p><p><span data-ttu-id="518e0-312">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-312">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="518e0-313">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="518e0-313">Relative paths are supported.</span></span> <span data-ttu-id="518e0-314">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="518e0-314">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="518e0-315">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-315">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-316">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="518e0-316">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="518e0-317">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="518e0-317">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="518e0-318">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-318">Default: `10`</span></span><br><span data-ttu-id="518e0-319">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-319">Min: `0`</span></span><br><span data-ttu-id="518e0-320">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="518e0-320">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="518e0-321">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-321">Optional timespan attribute.</span></span></p><p><span data-ttu-id="518e0-322">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-322">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="518e0-323">In den Versionen des ASP.NET Core-Moduls, die im Lieferumfang der Version ASP.NET Core 2.0 oder früher enthalten waren, darf der `requestTimeout` nur in ganzen Minuten angegeben werden. Andernfalls wird standardmäßig der Wert 2 Minuten angenommen.</span><span class="sxs-lookup"><span data-stu-id="518e0-323">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="518e0-324">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-324">Default: `00:02:00`</span></span><br><span data-ttu-id="518e0-325">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-325">Min: `00:00:00`</span></span><br><span data-ttu-id="518e0-326">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="518e0-326">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="518e0-327">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-327">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-328">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-328">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="518e0-329">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="518e0-329">Default: `10`</span></span><br><span data-ttu-id="518e0-330">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-330">Min: `0`</span></span><br><span data-ttu-id="518e0-331">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="518e0-331">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="518e0-332">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="518e0-333">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="518e0-333">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="518e0-334">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="518e0-334">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="518e0-335">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="518e0-335">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="518e0-336">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="518e0-336">Default: `120`</span></span><br><span data-ttu-id="518e0-337">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="518e0-337">Min: `0`</span></span><br><span data-ttu-id="518e0-338">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="518e0-338">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="518e0-339">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-339">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="518e0-340">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-340">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="518e0-341">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="518e0-341">Optional string attribute.</span></span></p><p><span data-ttu-id="518e0-342">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="518e0-342">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="518e0-343">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="518e0-343">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="518e0-344">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="518e0-344">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="518e0-345">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-345">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="518e0-346">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung (*.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="518e0-346">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="518e0-347">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="518e0-347">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="518e0-348">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="518e0-348">Setting environment variables</span></span>

<span data-ttu-id="518e0-349">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="518e0-349">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="518e0-350">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `environmentVariable` eines `environmentVariables`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="518e0-350">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="518e0-351">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="518e0-351">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="518e0-352">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="518e0-352">The following example sets two environment variables.</span></span> <span data-ttu-id="518e0-353">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="518e0-353">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="518e0-354">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="518e0-354">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="518e0-355">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="518e0-355">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!WARNING]
> <span data-ttu-id="518e0-356">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="518e0-356">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="518e0-357">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="518e0-357">app_offline.htm</span></span>

<span data-ttu-id="518e0-358">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="518e0-358">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="518e0-359">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="518e0-359">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="518e0-360">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="518e0-360">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="518e0-361">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="518e0-361">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="518e0-362">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="518e0-362">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="518e0-363">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="518e0-363">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="518e0-364">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="518e0-364">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="518e0-365">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="518e0-365">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="518e0-366">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="518e0-366">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="518e0-367">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="518e0-367">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="518e0-368">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="518e0-368">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="518e0-369">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="518e0-369">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="518e0-370">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="518e0-370">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="518e0-371">Wenn das ASP.NET Core-Modul den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="518e0-371">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="518e0-372">Um diese Seite zu unterdrücken und zur IIS-502-Sandardstatuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="518e0-372">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="518e0-373">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="518e0-373">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Statuscodeseite „502.5: Prozessfehler“](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="518e0-375">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="518e0-375">Log creation and redirection</span></span>

<span data-ttu-id="518e0-376">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="518e0-376">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="518e0-377">Alle im `stdoutLogFile`-Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-377">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="518e0-378">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="518e0-378">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="518e0-379">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="518e0-379">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="518e0-380">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="518e0-380">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="518e0-381">Die Verwendung des „stdout“-Protokolls wird zur Problembehandlung bei Startproblemen der App empfohlen.</span><span class="sxs-lookup"><span data-stu-id="518e0-381">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="518e0-382">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="518e0-382">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="518e0-383">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="518e0-383">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="518e0-384">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="518e0-384">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="518e0-385">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="518e0-385">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="518e0-386">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung (*.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="518e0-386">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="518e0-387">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="518e0-387">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="518e0-388">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="518e0-388">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="518e0-389">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="518e0-389">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="518e0-390">Das folgende `aspNetCore`-Beispielelement konfiguriert die stdout-Protokollierung für eine in Azure App Service gehostete App.</span><span class="sxs-lookup"><span data-stu-id="518e0-390">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="518e0-391">Ein lokaler Pfad oder ein Netzwerkfreigabepfad ist für die lokale Protokollierung zulässig.</span><span class="sxs-lookup"><span data-stu-id="518e0-391">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="518e0-392">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="518e0-392">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="518e0-393">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="518e0-393">Enhanced diagnostic logs</span></span>

<span data-ttu-id="518e0-394">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="518e0-394">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="518e0-395">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="518e0-395">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="518e0-396">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="518e0-396">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="518e0-397">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="518e0-397">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="518e0-398">ERROR</span><span class="sxs-lookup"><span data-stu-id="518e0-398">ERROR</span></span>
* <span data-ttu-id="518e0-399">WARNING</span><span class="sxs-lookup"><span data-stu-id="518e0-399">WARNING</span></span>
* <span data-ttu-id="518e0-400">INFO</span><span class="sxs-lookup"><span data-stu-id="518e0-400">INFO</span></span>
* <span data-ttu-id="518e0-401">TRACE</span><span class="sxs-lookup"><span data-stu-id="518e0-401">TRACE</span></span>

<span data-ttu-id="518e0-402">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="518e0-402">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="518e0-403">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="518e0-403">CONSOLE</span></span>
* <span data-ttu-id="518e0-404">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="518e0-404">EVENTLOG</span></span>
* <span data-ttu-id="518e0-405">DATEI</span><span class="sxs-lookup"><span data-stu-id="518e0-405">FILE</span></span>

<span data-ttu-id="518e0-406">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="518e0-406">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="518e0-407">`ASPNETCORE_MODULE_DEBUG_FILE`: Der Pfad zur Debugprotokolldatei</span><span class="sxs-lookup"><span data-stu-id="518e0-407">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="518e0-408">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="518e0-408">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="518e0-409">`ASPNETCORE_MODULE_DEBUG`: Einstellung der Debugebene</span><span class="sxs-lookup"><span data-stu-id="518e0-409">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="518e0-410">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="518e0-410">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="518e0-411">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="518e0-411">The size of the log isn't limited.</span></span> <span data-ttu-id="518e0-412">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="518e0-412">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="518e0-413">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="518e0-413">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="518e0-414">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="518e0-414">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="518e0-415">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="518e0-415">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="518e0-416">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="518e0-416">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="518e0-417">Die Verwendung von HTTP ist eine Leistungsoptimierung, bei der der Datenverkehr zwischen dem Modul und Kestrel in einer Loopbackadresse außerhalb der Netzwerkschnittstelle stattfindet.</span><span class="sxs-lookup"><span data-stu-id="518e0-417">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="518e0-418">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="518e0-418">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="518e0-419">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="518e0-419">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="518e0-420">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="518e0-420">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="518e0-421">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="518e0-421">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="518e0-422">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="518e0-422">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="518e0-423">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="518e0-423">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="518e0-424">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="518e0-424">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="518e0-425">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="518e0-425">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="518e0-426">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="518e0-426">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="518e0-427">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **SYSTEM**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="518e0-427">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="518e0-428">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in *applicationHost.config* auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="518e0-428">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="518e0-429">Wenn Sie eine IIS-Freigabekonfiguration verwenden, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="518e0-429">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="518e0-430">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="518e0-430">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="518e0-431">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="518e0-431">Run the installer.</span></span>
1. <span data-ttu-id="518e0-432">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="518e0-432">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="518e0-433">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="518e0-433">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="518e0-434">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="518e0-434">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="518e0-435">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="518e0-435">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="518e0-436">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="518e0-436">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="518e0-437">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="518e0-437">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="518e0-438">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="518e0-438">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="518e0-439">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="518e0-439">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="518e0-440">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<Zeitstempel>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="518e0-440">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="518e0-441">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="518e0-441">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="518e0-442">Modul</span><span class="sxs-lookup"><span data-stu-id="518e0-442">Module</span></span>

<span data-ttu-id="518e0-443">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="518e0-443">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="518e0-444">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-444">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="518e0-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="518e0-446">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-446">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="518e0-447">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-447">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="518e0-448">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="518e0-448">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="518e0-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="518e0-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="518e0-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="518e0-452">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="518e0-452">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="518e0-453">Schema</span><span class="sxs-lookup"><span data-stu-id="518e0-453">Schema</span></span>

<span data-ttu-id="518e0-454">**IIS**</span><span class="sxs-lookup"><span data-stu-id="518e0-454">**IIS**</span></span>

   * <span data-ttu-id="518e0-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="518e0-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="518e0-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="518e0-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="518e0-457">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="518e0-457">**IIS Express**</span></span>

   * <span data-ttu-id="518e0-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="518e0-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="518e0-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="518e0-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="518e0-460">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="518e0-460">Configuration</span></span>

<span data-ttu-id="518e0-461">**IIS**</span><span class="sxs-lookup"><span data-stu-id="518e0-461">**IIS**</span></span>

   * <span data-ttu-id="518e0-462">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="518e0-462">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="518e0-463">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="518e0-463">**IIS Express**</span></span>

   * <span data-ttu-id="518e0-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="518e0-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span></span>

<span data-ttu-id="518e0-465">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="518e0-465">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="518e0-466">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="518e0-466">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="518e0-467">GitHub-Repository für ASP.NET Core-Modul (Referenzquelle)</span><span class="sxs-lookup"><span data-stu-id="518e0-467">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>