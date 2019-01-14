---
title: Webserverimplementierungen in ASP.NET Core
author: guardrex
description: Ermitteln Sie die Webserver Kestrel und HTTP.sys für ASP.NET Core. Erfahren Sie mehr über das Auswählen eines Servers und darüber, wann ein Reverseproxyserver zu verwenden ist.
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/11/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 4210d67397c85a1608f79fc4ed9d283521356226
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249489"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="d7002-104">Webserverimplementierungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7002-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="d7002-105">Von [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) und [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d7002-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="d7002-106">Eine ASP.NET Core-App wird über eine In-Process-Implementierung eines HTTP-Servers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7002-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="d7002-107">Die Serverimplementierung lauscht auf HTTP-Anforderungen und leitet diese als [Anforderungsfunktionen](xref:fundamentals/request-features), die in einer <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse zusammengefasst werden, an die App weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="d7002-108">Windows</span><span class="sxs-lookup"><span data-stu-id="d7002-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="d7002-109">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="d7002-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="d7002-110">Ein [Kestrel-Server](xref:fundamentals/servers/kestrel) stellt die plattformübergreifende Standardimplementierung von HTTP-Servern dar.</span><span class="sxs-lookup"><span data-stu-id="d7002-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="d7002-111">IIS-HTTP-Server ist ein [In-Process-Server](#in-process-hosting-model) für IIS.</span><span class="sxs-lookup"><span data-stu-id="d7002-111">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="d7002-112">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="d7002-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="d7002-113">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App auf zwei unterschiedliche Arten ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="d7002-113">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="d7002-114">Im gleichen Prozess wie im IIS-Workerprozess (das [In-Process-Hostingmodell](#in-process-hosting-model)) mit dem [IIS-HTTP-Server](#iis-http-server).</span><span class="sxs-lookup"><span data-stu-id="d7002-114">In the same process as the IIS worker process (the [in-process hosting model](#in-process-hosting-model)) with the [IIS HTTP Server](#iis-http-server).</span></span> <span data-ttu-id="d7002-115">*In-Process* ist die empfohlene Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="d7002-115">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="d7002-116">In einem anderen Prozess als im IIS-Workerprozess (das [Out-of-Process-Hostingmodell](#out-of-process-hosting-model)) mit dem [Kestrel-Server](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="d7002-116">In a process separate from the IIS worker process (the [out-of-process hosting model](#out-of-process-hosting-model)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="d7002-117">Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ist ein natives IIS-Modul, das native IIS-Anforderungen zwischen IIS und dem In-Process-IIS-HTTP-Server oder Kestrel verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d7002-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="d7002-118">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="d7002-118">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="d7002-119">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="d7002-119">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="d7002-120">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="d7002-120">In-process hosting model</span></span>

<span data-ttu-id="d7002-121">Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Arbeitsprozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7002-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="d7002-122">Dadurch werden die Out-of-Process-Leistungseinbußen durch den Proxyvorgang für Anforderungen über den Loopbackadapter entfernt. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zurück zum selben Computer leitet.</span><span class="sxs-lookup"><span data-stu-id="d7002-122">This removes the out-of-process performance penalty of proxying requests over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="d7002-123">IIS erledigt das Prozessmanagement mit dem [Windows-Prozessaktivierungsdienst (Process Activation Service, WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="d7002-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d7002-124">Das ASP.NET Core-Modul:</span><span class="sxs-lookup"><span data-stu-id="d7002-124">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="d7002-125">Führt die Initialisierung von Apps aus.</span><span class="sxs-lookup"><span data-stu-id="d7002-125">Performs app initialization.</span></span>
  * <span data-ttu-id="d7002-126">Lädt die [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="d7002-126">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="d7002-127">Ruft `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="d7002-127">Calls `Program.Main`.</span></span>
* <span data-ttu-id="d7002-128">Behandelt die Lebensdauer der nativen IIS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="d7002-128">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="d7002-129">Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.</span><span class="sxs-lookup"><span data-stu-id="d7002-129">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="d7002-130">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer In-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="d7002-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core-Modul](_static/ancm-inprocess.png)

<span data-ttu-id="d7002-132">Eine Anforderung geht aus dem Web beim HTTP.sys-Treiber im Kernelmodus ein.</span><span class="sxs-lookup"><span data-stu-id="d7002-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d7002-133">Der Treiber leitet die native Anforderung an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d7002-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d7002-134">Das Modul empfängt die native Anforderung und übergibt sie an den IIS-HTTP-Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="d7002-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="d7002-135">Der IIS-HTTP-Server ist eine prozessinterne Serverimplementierung für IIS, die die Anforderung vom nativen Modus in den verwalteten Modus konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d7002-135">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="d7002-136">Nachdem der IIS-HTTP-Server die Anforderung verarbeitet hat, wird die Anforderung per Push an die Middlewarepipeline von ASP.NET Core übertragen.</span><span class="sxs-lookup"><span data-stu-id="d7002-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d7002-137">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d7002-138">Die Antwort der App wird an IIS übergeben, von wo sie per Push an den Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="d7002-138">The app's response is passed back to IIS, which pushes it back out to the client that initiated the request.</span></span>

<span data-ttu-id="d7002-139">In-Process-Hosting ist eine wählbare Option für vorhandene Apps. Die [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen sehen das In-Process-Hostingmodell aber als Standard für alle IIS- und IIS Express-Szenarios vor.</span><span class="sxs-lookup"><span data-stu-id="d7002-139">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d7002-140">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="d7002-140">Out-of-process hosting model</span></span>

<span data-ttu-id="d7002-141">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="d7002-141">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="d7002-142">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="d7002-142">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d7002-143">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-143">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d7002-144">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="d7002-144">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

<span data-ttu-id="d7002-146">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d7002-146">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d7002-147">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d7002-147">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d7002-148">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="d7002-148">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d7002-149">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{PORT}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="d7002-149">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="d7002-150">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="d7002-150">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d7002-151">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="d7002-151">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d7002-152">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="d7002-152">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d7002-153">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-153">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d7002-154">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="d7002-154">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d7002-155">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="d7002-155">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d7002-156">In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:</span><span class="sxs-lookup"><span data-stu-id="d7002-156">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="d7002-157">macOS</span><span class="sxs-lookup"><span data-stu-id="d7002-157">macOS</span></span>](#tab/macos)

<span data-ttu-id="d7002-158">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-158">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d7002-159">Linux</span><span class="sxs-lookup"><span data-stu-id="d7002-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="d7002-160">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-160">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="d7002-161">Windows</span><span class="sxs-lookup"><span data-stu-id="d7002-161">Windows</span></span>](#tab/windows)

<span data-ttu-id="d7002-162">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="d7002-162">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="d7002-163">[Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.</span><span class="sxs-lookup"><span data-stu-id="d7002-163">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="d7002-164">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="d7002-164">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="d7002-165">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App in einem anderen Prozess als im IIS-Workerprozess (*Out-of-Process*) mit dem [Kestrel-Server](#kestrel) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7002-165">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="d7002-166">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="d7002-166">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="d7002-167">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="d7002-167">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d7002-168">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-168">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d7002-169">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="d7002-169">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

<span data-ttu-id="d7002-171">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d7002-171">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d7002-172">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d7002-172">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d7002-173">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="d7002-173">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d7002-174">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="d7002-174">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="d7002-175">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="d7002-175">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d7002-176">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="d7002-176">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d7002-177">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="d7002-177">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d7002-178">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-178">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d7002-179">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="d7002-179">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d7002-180">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="d7002-180">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d7002-181">In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:</span><span class="sxs-lookup"><span data-stu-id="d7002-181">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="d7002-182">macOS</span><span class="sxs-lookup"><span data-stu-id="d7002-182">macOS</span></span>](#tab/macos)

<span data-ttu-id="d7002-183">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d7002-184">Linux</span><span class="sxs-lookup"><span data-stu-id="d7002-184">Linux</span></span>](#tab/linux)

<span data-ttu-id="d7002-185">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-185">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="d7002-186">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d7002-186">Kestrel</span></span>

<span data-ttu-id="d7002-187">Kestrel ist der Standardwebserver, der in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-187">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d7002-188">Kestrel eignet sich für folgende Zwecke:</span><span class="sxs-lookup"><span data-stu-id="d7002-188">Kestrel can be used:</span></span>

* <span data-ttu-id="d7002-189">Eigenständig als Edge-Server zur Verarbeitung von Anforderungen, die direkt aus einem Netzwerk, auch über das Internet, kommen.</span><span class="sxs-lookup"><span data-stu-id="d7002-189">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="d7002-191">Mit einem *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d7002-191">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d7002-192">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-192">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d7002-194">Jede der beiden Hostingkonfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; wird für ASP.NET Core 2.1 oder neuere Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d7002-194">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="d7002-195">Wenn die App nur Anforderungen aus einem internen Netzwerk akzeptiert, kann Kestrel eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-195">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![Kestrel kommuniziert direkt mit dem internen Netzwerk.](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="d7002-197">Wenn die App für das Internet verfügbar gemacht ist, muss Kestrel einen *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7002-197">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d7002-198">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="d7002-198">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d7002-200">Der wichtigste Grund für die Verwendung eines Reverseproxys für öffentlich zugängliche Edge-Server-Bereitstellungen, die direkt im Internet verfügbar gemacht werden, ist die Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="d7002-200">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="d7002-201">Die 1.x-Versionen von Kestrel enthalten keine wesentlichen Sicherheitsfeatures zum Schutz vor Angriffen aus dem Internet.</span><span class="sxs-lookup"><span data-stu-id="d7002-201">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="d7002-202">So sind z. B. keine geeigneten Timeouts, Größenlimits für Anforderungen sowie Beschränkungen der Anzahl gleichzeitiger Verbindungen vorhanden.</span><span class="sxs-lookup"><span data-stu-id="d7002-202">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

::: moniker-end

<span data-ttu-id="d7002-203">Leitfäden zur Kestrel-Konfiguration und Informationen darüber, wann Kestrel in einer Reverseproxykonfiguration verwendet wird, finden Sie unter <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="d7002-203">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

### <a name="nginx-with-kestrel"></a><span data-ttu-id="d7002-204">Nginx und Kestrel</span><span class="sxs-lookup"><span data-stu-id="d7002-204">Nginx with Kestrel</span></span>

<span data-ttu-id="d7002-205">Informationen dazu, wie Nginx unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="d7002-205">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="d7002-206">Apache und Kestrel</span><span class="sxs-lookup"><span data-stu-id="d7002-206">Apache with Kestrel</span></span>

<span data-ttu-id="d7002-207">Informationen dazu, wie Apache unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="d7002-207">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a><span data-ttu-id="d7002-208">IIS-HTTP-Server</span><span class="sxs-lookup"><span data-stu-id="d7002-208">IIS HTTP Server</span></span>

<span data-ttu-id="d7002-209">IIS-HTTP-Server ist ein [In-Process-Server](#in-process-hosting-model) für IIS, der für In-Process-Bereitstellungen benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="d7002-209">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS and required for in-process deployments.</span></span> <span data-ttu-id="d7002-210">Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) verarbeitet native IIS-Anforderungen zwischen IIS und dem IIS-HTTP-Server.</span><span class="sxs-lookup"><span data-stu-id="d7002-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) handles native IIS requests between IIS and IIS HTTP Server.</span></span> <span data-ttu-id="d7002-211">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="d7002-211">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="d7002-212">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d7002-212">HTTP.sys</span></span>

<span data-ttu-id="d7002-213">Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist HTTP.sys eine Alternative zu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7002-213">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="d7002-214">Grundsätzlich empfiehlt sich Kestrel, um die beste Leistung zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="d7002-214">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="d7002-215">HTTP.sys kann in Szenarien verwendet werden, in denen die App mit dem Internet verbunden ist und erforderliche Funktionen von HTTP.sys, aber nicht von Kestrel unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-215">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="d7002-216">Weitere Informationen finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="d7002-216">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="d7002-218">Außerdem kann HTTP.sys auch bei Apss zum Einsatz kommen, die nur in einem internen Netzwerk verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-218">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d7002-220">Den Leitfaden für die HTTP.sys-Konfiguration finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="d7002-220">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="d7002-221">ASP.NET Core-Serverinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="d7002-221">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="d7002-222">Die [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder)-Schnittstelle, sie in der `Startup.Configure`-Methode verfügbar ist, macht die [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures)-Eigenschaft des Typs [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d7002-222">The [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup.Configure` method exposes the [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) property of type [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="d7002-223">Kestrel und HTTP.sys machen nur jeweils eine einzelne Funktion verfügbar, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature). Andere Serverimplementierungen machen jedoch möglicherweise weitere Funktionalitäten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d7002-223">Kestrel and HTTP.sys only expose a single feature each, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="d7002-224">Mit `IServerAddressesFeature` kann ermittelt werden, an welchen Port die Serverimplementierung zur Laufzeit gebunden ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-224">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="d7002-225">Benutzerdefinierte Server</span><span class="sxs-lookup"><span data-stu-id="d7002-225">Custom servers</span></span>

<span data-ttu-id="d7002-226">Wenn die integrierten Server nicht den Anforderungen der App entsprechen, kann eine benutzerdefinierte Serverimplementierung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="d7002-226">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="d7002-227">In der [Anleitung zu Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) wird erläutert, wie eine auf [Nowin](https://github.com/Bobris/Nowin) basierende [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver)-Implementierung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d7002-227">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="d7002-228">Nur die Feature-Schnittstellen, die von der App verwendet werden, erfordern Implementierung , wobei mindestens [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) und [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) unterstützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="d7002-228">Only the feature interfaces that the app uses require implementation, though at a minimum [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="d7002-229">Serverstart</span><span class="sxs-lookup"><span data-stu-id="d7002-229">Server startup</span></span>

<span data-ttu-id="d7002-230">Der Server wird gestartet, wenn die integrierte Entwicklungsumgebung (IDE) oder der Editor die App startet:</span><span class="sxs-lookup"><span data-stu-id="d7002-230">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="d7002-231">[Visual Studio:](https://www.visualstudio.com/vs/) Die Startprofile können verwendet werden, um die App und den Server mit [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) oder mit der Konsole zu starten.</span><span class="sxs-lookup"><span data-stu-id="d7002-231">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="d7002-232">[Visual Studio Code:](https://code.visualstudio.com/) Die App und der Server werden durch [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) gestartet, das den CoreCLR-Debugger aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d7002-232">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="d7002-233">[Visual Studio für Mac:](https://www.visualstudio.com/vs/mac/) Die App und der Server werden durch den [Mono Soft-Mode Debugger](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) gestartet.</span><span class="sxs-lookup"><span data-stu-id="d7002-233">[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="d7002-234">Wird die App über eine Eingabeaufforderung im Ordner des Projekts gestartet, startet [dotnet run](/dotnet/core/tools/dotnet-run) die App und den Server (nur Kestrel und HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="d7002-234">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="d7002-235">Die Konfiguration wird durch die Option `-c|--configuration` angegeben, die entweder auf `Debug` (Standardwert) oder `Release` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-235">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="d7002-236">Sind in der Datei *launchSettings.json* Startprofile vorhanden, verwenden Sie die Option `--launch-profile <NAME>`, um das Startprofil festzulegen (z. B. `Development` oder `Production`).</span><span class="sxs-lookup"><span data-stu-id="d7002-236">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="d7002-237">Weitere Informationen hierzu finden Sie unter [dotnet run](/dotnet/core/tools/dotnet-run) und unter [Verpacken einer Verteilung von .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="d7002-237">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="d7002-238">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="d7002-238">HTTP/2 support</span></span>

<span data-ttu-id="d7002-239">[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden Bereitstellungsszenarien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d7002-239">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="d7002-240">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d7002-240">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="d7002-241">Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="d7002-241">Operating system</span></span>
    * <span data-ttu-id="d7002-242">Windows Server 2016/Windows 10 oder höher&dagger;</span><span class="sxs-lookup"><span data-stu-id="d7002-242">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="d7002-243">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="d7002-243">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="d7002-244">HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d7002-244">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="d7002-245">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-245">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="d7002-246">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d7002-246">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="d7002-247">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-247">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="d7002-248">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="d7002-248">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="d7002-249">IIS (In-Process)</span><span class="sxs-lookup"><span data-stu-id="d7002-249">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d7002-250">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-250">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d7002-251">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-251">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="d7002-252">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="d7002-252">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d7002-253">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-253">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d7002-254">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d7002-254">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d7002-255">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="d7002-255">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="d7002-256">&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="d7002-256">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d7002-257">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="d7002-257">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d7002-258">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d7002-258">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="d7002-259">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d7002-259">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="d7002-260">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-260">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="d7002-261">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="d7002-261">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="d7002-262">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="d7002-262">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d7002-263">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="d7002-263">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d7002-264">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d7002-264">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d7002-265">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="d7002-265">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="d7002-266">Eine HTTP/2-Verbindung muss [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) und TLS 1.2 oder höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7002-266">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="d7002-267">Weitere Informationen finden Sie in den Themen, die Ihre Serverbereitstellungsszenarien betreffen.</span><span class="sxs-lookup"><span data-stu-id="d7002-267">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7002-268">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d7002-268">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
