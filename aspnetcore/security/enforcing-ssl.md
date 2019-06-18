---
title: Erzwingen von HTTPS in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie HTTPS/TLS in einer ASP.NET Core-Web-app benötigen.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 08ce50775d1b5348cb0528a1724cec2e5c72dae2
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152903"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="a38c7-103">Erzwingen von HTTPS in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a38c7-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="a38c7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a38c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a38c7-105">Dieses Dokument zeigt, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="a38c7-105">This document shows how to:</span></span>

* <span data-ttu-id="a38c7-106">HTTPS für alle Anforderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a38c7-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="a38c7-107">Alle HTTP-Anforderungen auf HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="a38c7-108">Keine API kann verhindern, dass einen Client sensible Daten bei der ersten Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="a38c7-109">-API-Projekte</span><span class="sxs-lookup"><span data-stu-id="a38c7-109">API projects</span></span>
>
> <span data-ttu-id="a38c7-110">Führen Sie **nicht** verwenden [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) für Web-APIs, die vertraulichen Informationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="a38c7-111">`RequireHttpsAttribute` leitet Browsers per HTTP-Statuscode von HTTP an HTTPS weiter.</span><span class="sxs-lookup"><span data-stu-id="a38c7-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="a38c7-112">API-Clients verstehen diese Codes möglicherweise nicht, oder Sie führen keine Weiterleitung von HTTP an HTTPS durch.</span><span class="sxs-lookup"><span data-stu-id="a38c7-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="a38c7-113">Dies kann dazu führen, dass solche Clients Daten unverschlüsselt mittels HTTP versenden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="a38c7-114">Web-APIs sollten daher entweder:</span><span class="sxs-lookup"><span data-stu-id="a38c7-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="a38c7-115">nicht auf HTTP lauschen oder</span><span class="sxs-lookup"><span data-stu-id="a38c7-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="a38c7-116">die Verbindung mit dem Statuscode 400 („Ungültige Anforderung“) schließen und die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="a38c7-117">-API-Projekte</span><span class="sxs-lookup"><span data-stu-id="a38c7-117">API projects</span></span>
>
> <span data-ttu-id="a38c7-118">Führen Sie **nicht** verwenden [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) für Web-APIs, die vertraulichen Informationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-118">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="a38c7-119">`RequireHttpsAttribute` leitet Browsers per HTTP-Statuscode von HTTP an HTTPS weiter.</span><span class="sxs-lookup"><span data-stu-id="a38c7-119">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="a38c7-120">API-Clients verstehen diese Codes möglicherweise nicht, oder Sie führen keine Weiterleitung von HTTP an HTTPS durch.</span><span class="sxs-lookup"><span data-stu-id="a38c7-120">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="a38c7-121">Dies kann dazu führen, dass solche Clients Daten unverschlüsselt mittels HTTP versenden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-121">Such clients may send information over HTTP.</span></span> <span data-ttu-id="a38c7-122">Web-APIs sollten daher entweder:</span><span class="sxs-lookup"><span data-stu-id="a38c7-122">Web APIs should either:</span></span>
>
> * <span data-ttu-id="a38c7-123">nicht auf HTTP lauschen oder</span><span class="sxs-lookup"><span data-stu-id="a38c7-123">Not listen on HTTP.</span></span>
> * <span data-ttu-id="a38c7-124">die Verbindung mit dem Statuscode 400 („Ungültige Anforderung“) schließen und die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-124">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="a38c7-125">HSTS und API-Projekte</span><span class="sxs-lookup"><span data-stu-id="a38c7-125">HSTS and API projects</span></span>
>
> <span data-ttu-id="a38c7-126">Die Standard-API-Projekte enthalten keine [HSTS](#hsts) da HSTS in der Regel eine einzige Browser-Anweisung ist.</span><span class="sxs-lookup"><span data-stu-id="a38c7-126">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="a38c7-127">Führen Sie die anderen Abrufe, z. B. Telefon oder desktop-apps **nicht** befolgen die Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-127">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="a38c7-128">Sogar innerhalb von Browsern hat ein einzelner authentifizierter Aufruf einer API über HTTP Risiken auf unsicheren Netzwerken.</span><span class="sxs-lookup"><span data-stu-id="a38c7-128">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="a38c7-129">Der sichere Ansatz ist zum Konfigurieren von API-Projekten nur zum Empfangen und Antworten über HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a38c7-129">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="a38c7-130">Erforderlichkeit von HTTPS</span><span class="sxs-lookup"><span data-stu-id="a38c7-130">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a38c7-131">Es wird empfohlen, Produktion ASP.NET Core Web-apps-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="a38c7-131">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="a38c7-132">HTTPS-Umleitung-Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="a38c7-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="a38c7-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)), HTTP Strict Transport Security Protocol (HSTS)-Header für Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a38c7-134">In einer Reverseproxykonfiguration bereitgestellte Apps ermöglichen die Proxys, für die verbindungssicherheit (HTTPS) behandelt.</span><span class="sxs-lookup"><span data-stu-id="a38c7-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="a38c7-135">Wenn der Proxy auch HTTPS-Umleitung behandelt wird, besteht keine Notwendigkeit zur Verwendung von HTTPS-Umleitung-Middleware.</span><span class="sxs-lookup"><span data-stu-id="a38c7-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a38c7-136">Wenn der Proxyserver verarbeitet auch das Schreiben von HSTS-Header (z. B. [native HSTS zu unterstützen, in IIS 10.0 (1709) oder höher](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware nicht erforderlich, von der app.</span><span class="sxs-lookup"><span data-stu-id="a38c7-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="a38c7-137">Weitere Informationen finden Sie unter [Opt-Out-of HTTPS/HSTS bei projekterstellung](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="a38c7-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="a38c7-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="a38c7-138">UseHttpsRedirection</span></span>

<span data-ttu-id="a38c7-139">Der folgende code ruft `UseHttpsRedirection` in die `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="a38c7-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="a38c7-140">Der oben markierte Code:</span><span class="sxs-lookup"><span data-stu-id="a38c7-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="a38c7-141">Verwendet die standardmäßige [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="a38c7-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="a38c7-142">Verwendet die standardmäßige [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), es sei denn, durch Überschreiben der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="a38c7-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="a38c7-143">Es wird empfohlen, statt temporäre umleitungen dauerhafte umleitungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="a38c7-144">Die Zwischenspeicherung von Ressourcenlinks kann dazu führen, dass instabiles Verhalten in entwicklungsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="a38c7-145">Wenn Sie einen permanente Umleitungsstatuscode senden lieber, wenn die app in einer nicht-Entwicklungsumgebung ist, finden Sie unter der [dauerhafte umleitungen zu konfigurieren, in der Produktion](#configure-permanent-redirects-in-production) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="a38c7-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="a38c7-146">Es wird empfohlen, [HSTS](#http-strict-transport-security-protocol-hsts) , um Clients zu signalisieren, die Ressource nur sichere Anforderungen an die app (nur in der Produktion) gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="a38c7-147">Portkonfiguration</span><span class="sxs-lookup"><span data-stu-id="a38c7-147">Port configuration</span></span>

<span data-ttu-id="a38c7-148">Ein Port muss für die Middleware verfügbar sein, eine unsichere Anforderung an HTTPS umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="a38c7-149">Wenn kein Port verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="a38c7-149">If no port is available:</span></span>

* <span data-ttu-id="a38c7-150">Umleitung zu HTTPS auftreten nicht.</span><span class="sxs-lookup"><span data-stu-id="a38c7-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="a38c7-151">Die Middleware protokolliert die Warnung "Fehler bei den Https-Port für die Umleitung zu bestimmen."</span><span class="sxs-lookup"><span data-stu-id="a38c7-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="a38c7-152">Geben Sie den HTTPS-Port mit einer der folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="a38c7-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="a38c7-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="a38c7-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="a38c7-154">Legen Sie die `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [Https_port Webhost-Konfigurationseinstellung](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="a38c7-154">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="a38c7-155">**Schlüssel**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="a38c7-155">**Key**: `https_port`</span></span>  
  <span data-ttu-id="a38c7-156">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="a38c7-156">**Type**: *string*</span></span>  
  <span data-ttu-id="a38c7-157">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a38c7-157">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="a38c7-158">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a38c7-158">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="a38c7-159">**Umgebungsvariable**: `<PREFIX_>HTTPS_PORT` (Das Präfix ist `ASPNETCORE_` bei Verwendung der [Webhost](xref:fundamentals/host/web-host).)</span><span class="sxs-lookup"><span data-stu-id="a38c7-159">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="a38c7-160">Beim Konfigurieren einer <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span><span class="sxs-lookup"><span data-stu-id="a38c7-160">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="a38c7-161">Geben Sie einen Port mit dem sicheren Schema unter Verwendung der `ASPNETCORE_URLS` -Umgebungsvariablen angegeben.</span><span class="sxs-lookup"><span data-stu-id="a38c7-161">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="a38c7-162">Die Umgebungsvariable konfiguriert den Server.</span><span class="sxs-lookup"><span data-stu-id="a38c7-162">The environment variable configures the server.</span></span> <span data-ttu-id="a38c7-163">Die Middleware indirekt ermittelt die HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="a38c7-163">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="a38c7-164">Dieser Ansatz funktioniert nicht bei reverse-Proxy-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-164">This approach doesn't work in reverse proxy deployments.</span></span>
* <span data-ttu-id="a38c7-165">Legen Sie bei der Entwicklung eine HTTPS-URL in *"launchsettings.JSON"* .</span><span class="sxs-lookup"><span data-stu-id="a38c7-165">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="a38c7-166">Aktivieren Sie HTTPS, bei der IIS Express verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-166">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="a38c7-167">Konfigurieren Sie einen HTTPS-URL-Endpunkt für die edgebereitstellung von öffentlich zugänglichen [Kestrel](xref:fundamentals/servers/kestrel) Server oder [HTTP.sys](xref:fundamentals/servers/httpsys) Server.</span><span class="sxs-lookup"><span data-stu-id="a38c7-167">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="a38c7-168">Nur **eine HTTPS-Port** wird von der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="a38c7-168">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="a38c7-169">Die Middleware ermittelt, den Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="a38c7-169">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="a38c7-170">Beim Ausführen einer app in einer Reverseproxykonfiguration <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ist nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a38c7-170">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="a38c7-171">Legen Sie den Port, der mit einem der anderen in diesem Abschnitt beschriebenen Ansätze.</span><span class="sxs-lookup"><span data-stu-id="a38c7-171">Set the port using one of the other approaches described in this section.</span></span>

<span data-ttu-id="a38c7-172">Wenn Kestrel oder HTTP.sys als öffentlich zugängliche Edgeserver verwendet wird, muss Kestrel oder HTTP.sys zum Lauschen an beide konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a38c7-172">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="a38c7-173">Die, in dem der Client umgeleitet wird, sicherer Port (in der Regel Port 443 in der Produktion und 5001 in der Entwicklung).</span><span class="sxs-lookup"><span data-stu-id="a38c7-173">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="a38c7-174">Der unsichere Port (üblicherweise 80 in der Produktion) und 5000 in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="a38c7-174">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="a38c7-175">Der unsichere Port muss der Client erhält eine unsichere Anforderung und den Client mit dem sicheren Port umleiten, damit die app zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="a38c7-175">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="a38c7-176">Weitere Informationen finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) oder <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="a38c7-176">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="a38c7-177">Bereitstellungsszenarien</span><span class="sxs-lookup"><span data-stu-id="a38c7-177">Deployment scenarios</span></span>

<span data-ttu-id="a38c7-178">Jeder Firewall zwischen dem Client und Server müssen auch Kommunikationsports für den Datenverkehr zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-178">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="a38c7-179">Wenn die Anforderungen in einer Reverseproxykonfiguration weitergeleitet werden, verwenden Sie [Forwardedheadersmiddleware](xref:host-and-deploy/proxy-load-balancer) vor dem Aufruf von HTTPS-Umleitung-Middleware.</span><span class="sxs-lookup"><span data-stu-id="a38c7-179">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a38c7-180">Forwarded-Header-Middleware Updates der `Request.Scheme`unter Verwendung der `X-Forwarded-Proto` Header.</span><span class="sxs-lookup"><span data-stu-id="a38c7-180">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="a38c7-181">Die Middleware ermöglicht umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="a38c7-181">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="a38c7-182">Wenn Forwardedheadersmiddleware nicht verwendet wird, möglicherweise nicht die Back-End-app erhalten das richtige Schema und letztlich in eine Umleitungsschleife entsteht.</span><span class="sxs-lookup"><span data-stu-id="a38c7-182">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="a38c7-183">Eine allgemeine Fehlermeldung für Endbenutzer ist, dass zu viele umleitungen aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="a38c7-183">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="a38c7-184">Wenn Sie in Azure App Service bereitstellen möchten, befolgen Sie die Anweisungen in [Lernprogramm: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="a38c7-184">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="a38c7-185">Optionen</span><span class="sxs-lookup"><span data-stu-id="a38c7-185">Options</span></span>

<span data-ttu-id="a38c7-186">Die folgenden hervorgehobenen Code ruft [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) middlewareoptionen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a38c7-186">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="a38c7-187">Aufrufen von `AddHttpsRedirection` ist nur erforderlich, ändern Sie die Werte der `HttpsPort` oder `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="a38c7-187">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="a38c7-188">Der oben markierte Code:</span><span class="sxs-lookup"><span data-stu-id="a38c7-188">The preceding highlighted code:</span></span>

* <span data-ttu-id="a38c7-189">Legt [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) zu <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, dies ist der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="a38c7-189">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="a38c7-190">Verwenden Sie die Felder von der <xref:Microsoft.AspNetCore.Http.StatusCodes> -Klasse für Zuweisungen zu `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="a38c7-190">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="a38c7-191">Legt den HTTPS-Port in 5001 fest.</span><span class="sxs-lookup"><span data-stu-id="a38c7-191">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="a38c7-192">Der Standardwert ist 443.</span><span class="sxs-lookup"><span data-stu-id="a38c7-192">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="a38c7-193">Konfigurieren Sie dauerhafte umleitungen in der Produktion</span><span class="sxs-lookup"><span data-stu-id="a38c7-193">Configure permanent redirects in production</span></span>

<span data-ttu-id="a38c7-194">Die Middleware standardmäßig zum Senden einer [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) mit alle umleitungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-194">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="a38c7-195">Falls gewünscht, einen permanente Umleitung-Statuscode senden, wenn die app in einer Entwicklungsumgebung, umschließen Sie die Konfiguration der Middleware-Optionen in eine bedingungsüberprüfung für eine nicht-Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="a38c7-195">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="a38c7-196">Beim Konfigurieren einer `IWebHostBuilder` in *"Startup.cs"* :</span><span class="sxs-lookup"><span data-stu-id="a38c7-196">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="a38c7-197">Alternativer Ansatz für HTTPS-Umleitung-Middleware</span><span class="sxs-lookup"><span data-stu-id="a38c7-197">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="a38c7-198">Eine Alternative zur Verwendung von HTTPS-Umleitung-Middleware (`UseHttpsRedirection`) ist die Verwendung von URL-Umschreibenden Middleware (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="a38c7-198">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="a38c7-199">`AddRedirectToHttps` können den Statuscode und den Port auch festlegen, wenn die Umleitung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-199">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="a38c7-200">Weitere Informationen finden Sie unter [URL-Umschreibenden Middleware](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="a38c7-200">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="a38c7-201">Ohne die Notwendigkeit von zusätzlichen umleitungs-Regeln an HTTPS umleiten möchten, empfehlen wir Ihnen unter Verwendung von HTTPS-Umleitung-Middleware (`UseHttpsRedirection`) in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="a38c7-201">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="a38c7-202">Die [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) wird verwendet, um die HTTPS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a38c7-202">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="a38c7-203">`[RequireHttpsAttribute]` ergänzen können, Controllern oder Methoden oder global angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="a38c7-203">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="a38c7-204">Um das Attribut global anzuwenden, fügen Sie den folgenden Code zur `ConfigureServices` in `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a38c7-204">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="a38c7-205">Der oben markierte Code ist erforderlich, alle Anforderungen verwenden `HTTPS`daher HTTP-Anforderungen werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a38c7-205">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="a38c7-206">Der folgende hervorgehobene Code leitet alle HTTP-Anfragen an HTTPS um:</span><span class="sxs-lookup"><span data-stu-id="a38c7-206">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="a38c7-207">Weitere Informationen finden Sie unter [URL-Umschreibenden Middleware](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="a38c7-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="a38c7-208">Die Middleware kann außerdem die app, die den Statuscode oder den Statuscode sowie den Port festgelegt, wenn die Umleitung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-208">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="a38c7-209">Das globale Erzwingen der Verwendung von HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) ist eine bewährte Sicherheitsmethode.</span><span class="sxs-lookup"><span data-stu-id="a38c7-209">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="a38c7-210">Dieser Ansatz gilt im Vergleich zur Anwendung des `[RequireHttps]` -Attributs auf alle Controller und Razor Pages als sicherer.</span><span class="sxs-lookup"><span data-stu-id="a38c7-210">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="a38c7-211">denn Sie können nicht gewährleisten, dass das `[RequireHttps]` -Attribut angewendet wird, wenn neue Controller oder Razor Pages hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-211">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="a38c7-212">HTTP Strict Transport Security-Protokoll (HSTS)</span><span class="sxs-lookup"><span data-stu-id="a38c7-212">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="a38c7-213">Pro [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) ist eine optionale sicherheitserweiterung, die von einer Web-app durch die Verwendung eines Antwortheaders angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-213">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="a38c7-214">Wenn eine [Browser mit Unterstützung von HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) diesen Header empfängt:</span><span class="sxs-lookup"><span data-stu-id="a38c7-214">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="a38c7-215">Der Browser speichert die Konfiguration für die Domäne, die verhindert, dass eine Kommunikation über HTTP senden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-215">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="a38c7-216">Der Browser wird die gesamte Kommunikation über HTTPS erzwungen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-216">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="a38c7-217">Der Browser wird verhindert, dass der Benutzer mithilfe von Zertifikaten für nicht vertrauenswürdig oder ungültig.</span><span class="sxs-lookup"><span data-stu-id="a38c7-217">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="a38c7-218">Der Browser deaktiviert eingabeaufforderungen, die einen solches Zertifikat vorübergehend vertrauen Benutzer zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-218">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="a38c7-219">Da HSTS vom Client erzwungen wird, hat es einige Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="a38c7-219">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="a38c7-220">Der Client muss HSTS unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-220">The client must support HSTS.</span></span>
* <span data-ttu-id="a38c7-221">HSTS muss mindestens eine erfolgreiche HTTPS-Anforderung zu, um die Richtlinie HSTS herzustellen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-221">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="a38c7-222">Die Anwendung muss jede HTTP-Anforderung überprüfen und umleiten oder die HTTP-Anforderung ablehnen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-222">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="a38c7-223">ASP.NET Core 2.1 oder höher implementiert HSTS mit der `UseHsts` -Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a38c7-223">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="a38c7-224">Der folgende code ruft `UseHsts` bei der app nicht im [Entwicklungsmodus](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="a38c7-224">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="a38c7-225">`UseHsts` ist nicht in der Entwicklung empfohlen, da die Einstellungen HSTS hohem Maße zwischenspeicherbar sind von Browsern.</span><span class="sxs-lookup"><span data-stu-id="a38c7-225">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="a38c7-226">In der Standardeinstellung `UseHsts` schließt die lokalen Loopback-Adresse.</span><span class="sxs-lookup"><span data-stu-id="a38c7-226">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="a38c7-227">Für produktionsumgebungen HTTPS zum ersten Mal implementieren legen Sie den ersten [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) auf einen niedrigen Wert eines der <xref:System.TimeSpan> Methoden.</span><span class="sxs-lookup"><span data-stu-id="a38c7-227">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="a38c7-228">Legen Sie den Wert von Stunden keine mehr als ein Tag für den Fall, dass Sie die HTTP-HTTPS-Infrastruktur wiederherstellen müssen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-228">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="a38c7-229">Nachdem Sie sich die Nachhaltigkeit der HTTPS-Konfiguration sicher sind, erhöhen Sie den HSTS-Max-Age-Wert; häufig verwendeter Wert ist ein Jahr.</span><span class="sxs-lookup"><span data-stu-id="a38c7-229">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="a38c7-230">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="a38c7-230">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="a38c7-231">Legt den preload Parameter des Strict-Transport-Security-Headers.</span><span class="sxs-lookup"><span data-stu-id="a38c7-231">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="a38c7-232">Preload ist nicht Teil der [RFC HSTS Spezifikation](https://tools.ietf.org/html/rfc6797), aber vom Webbrowser zum Vorabladen von HSTS Websites Neuinstallation unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-232">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="a38c7-233">Weitere Informationen finden Sie unter [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="a38c7-233">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="a38c7-234">Ermöglicht [IncludeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), denen Unterdomänen der Host die HSTS-Richtlinie gilt.</span><span class="sxs-lookup"><span data-stu-id="a38c7-234">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="a38c7-235">Explizit festlegt den Max-Age-Parameter, der den Strict-Transport-Security-Header auf 60 Tage.</span><span class="sxs-lookup"><span data-stu-id="a38c7-235">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="a38c7-236">Wenn nicht festgelegt, der Standardwert ist 30 Tage.</span><span class="sxs-lookup"><span data-stu-id="a38c7-236">If not set, defaults to 30 days.</span></span> <span data-ttu-id="a38c7-237">Finden Sie unter den [Max-Age-Direktive](https://tools.ietf.org/html/rfc6797#section-6.1.1) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-237">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="a38c7-238">Fügt `example.com` zur Liste der Hosts zu schließen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-238">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="a38c7-239">`UseHsts` Schließt die folgenden Loopback-Hosts an:</span><span class="sxs-lookup"><span data-stu-id="a38c7-239">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="a38c7-240">`localhost` : Die IPv4-Loopback-Adresse.</span><span class="sxs-lookup"><span data-stu-id="a38c7-240">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="a38c7-241">`127.0.0.1` : Die IPv4-Loopback-Adresse.</span><span class="sxs-lookup"><span data-stu-id="a38c7-241">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="a38c7-242">`[::1]` : Die IPv6-Loopback-Adresse.</span><span class="sxs-lookup"><span data-stu-id="a38c7-242">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="a38c7-243">Deaktivieren von HTTPS/HSTS bei projekterstellung</span><span class="sxs-lookup"><span data-stu-id="a38c7-243">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="a38c7-244">In einigen Back-End-Dienst-Szenarien, in denen verbindungssicherheit am Rand des Netzwerks öffentlich zugänglichen behandelt wird, ist nicht Konfigurieren der Sicherheit der Serververbindung über jeden Knoten erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a38c7-244">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="a38c7-245">Web-apps, die aus den Vorlagen in Visual Studio oder über generiert die [Dotnet neue](/dotnet/core/tools/dotnet-new) Befehls aktivieren [HTTPS-Umleitung](#require-https) und [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="a38c7-245">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="a38c7-246">Für Bereitstellungen, die diese Szenarien erfordern nicht, Sie können HTTPS/HSTS deaktivieren, wenn die app aus der Vorlage erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a38c7-246">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="a38c7-247">Zum Deaktivieren von HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="a38c7-247">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a38c7-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38c7-248">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a38c7-249">Deaktivieren Sie die **für HTTPS konfigurieren** Kontrollkästchen.</span><span class="sxs-lookup"><span data-stu-id="a38c7-249">Uncheck the **Configure for HTTPS** check box.</span></span>

![Dialogfeld "neues ASP.NET Core-Webanwendung" mit der konfigurieren für HTTPS-Kontrollkästchen deaktiviert.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a38c7-251">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="a38c7-251">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="a38c7-252">Verwenden Sie die `--no-https`-Option.</span><span class="sxs-lookup"><span data-stu-id="a38c7-252">Use the `--no-https` option.</span></span> <span data-ttu-id="a38c7-253">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a38c7-253">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="a38c7-254">Vertrauen Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat unter Windows und macOS</span><span class="sxs-lookup"><span data-stu-id="a38c7-254">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="a38c7-255">.NET Core SDK umfasst ein Entwicklungszertifikat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a38c7-255">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="a38c7-256">Das Zertifikat wird als Teil der ersten Ausführung-Umgebung installiert.</span><span class="sxs-lookup"><span data-stu-id="a38c7-256">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="a38c7-257">Z. B. `dotnet --info` erzeugt eine Ausgabe ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="a38c7-257">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="a38c7-258">Durch das Installieren des ASP.NET Core SDK wird das ASP.NET Core-HTTPS-Entwicklungszertifikat im lokalen Benutzerzertifikatspeicher installiert.</span><span class="sxs-lookup"><span data-stu-id="a38c7-258">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="a38c7-259">Das Zertifikat wurde installiert, aber es ist nicht als vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="a38c7-259">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="a38c7-260">Führen Sie den einmaligen Schritt zum Ausführen der Dotnet Vertrauen des Zertifikats `dev-certs` Tool:</span><span class="sxs-lookup"><span data-stu-id="a38c7-260">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="a38c7-261">Der folgende Befehl stellt Hilfe zum `dev-certs`-Tool bereit:</span><span class="sxs-lookup"><span data-stu-id="a38c7-261">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="a38c7-262">So richten Sie ein entwicklerzertifikat für Docker</span><span class="sxs-lookup"><span data-stu-id="a38c7-262">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="a38c7-263">Finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="a38c7-263">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="a38c7-264">Vertrauen Sie HTTPS-Zertifikat von Windows-Subsystem für Linux</span><span class="sxs-lookup"><span data-stu-id="a38c7-264">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="a38c7-265">Das Windows-Subsystem für Linux (WSL) generiert ein selbstsigniertes Zertifikat für HTTPS. So konfigurieren Sie den Windows-Zertifikatspeicher, um die WSL-Zertifikat als vertrauenswürdig einstufen:</span><span class="sxs-lookup"><span data-stu-id="a38c7-265">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="a38c7-266">Führen Sie den folgenden Befehl zum Exportieren des Zertifikats WSL generiert: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="a38c7-266">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="a38c7-267">Führen Sie in einem WSL-Fenster den folgenden Befehl ein: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="a38c7-267">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="a38c7-268">Der vorherige Befehl legt die Umgebungsvariablen fest, damit die Linux das vertrauenswürdige Windows-Zertifikat verwendet.</span><span class="sxs-lookup"><span data-stu-id="a38c7-268">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="a38c7-269">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="a38c7-269">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="a38c7-270">Hosten von ASP.NET Core unter Linux mit Apache: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a38c7-270">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="a38c7-271">Hosten von ASP.NET Core unter Linux mit Nginx: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a38c7-271">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="a38c7-272">Gewusst wie: Einrichten von SSL auf IIS</span><span class="sxs-lookup"><span data-stu-id="a38c7-272">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="a38c7-273">OWASP HSTS-Browserunterstützung</span><span class="sxs-lookup"><span data-stu-id="a38c7-273">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
