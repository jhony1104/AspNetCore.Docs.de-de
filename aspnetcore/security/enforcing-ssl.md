---
title: Erzwingen von HTTPS in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie HTTPS/TLS in einer ASP.net Core-Web-App anfordern.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 82cd2e52f3bd929682b9eae24611ad04fd9f8682
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317367"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="b8e3b-103">Erzwingen von HTTPS in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b8e3b-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="b8e3b-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b8e3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b8e3b-105">Dieses Dokument zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-105">This document shows how to:</span></span>

* <span data-ttu-id="b8e3b-106">Erfordert HTTPS für alle Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="b8e3b-107">Alle HTTP-Anforderungen auf HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="b8e3b-108">Eine API kann nicht verhindern, dass ein Client bei der ersten Anforderung sensible Daten sendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="b8e3b-109">API-Projekte</span><span class="sxs-lookup"><span data-stu-id="b8e3b-109">API projects</span></span>
>
> <span data-ttu-id="b8e3b-110">Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="b8e3b-111">`RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="b8e3b-112">API-Clients verstehen diese Codes möglicherweise nicht, oder Sie führen keine Weiterleitung von HTTP an HTTPS durch.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="b8e3b-113">Dies kann dazu führen, dass solche Clients Daten unverschlüsselt mittels HTTP versenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="b8e3b-114">Web-APIs sollten daher entweder:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="b8e3b-115">nicht auf HTTP lauschen oder</span><span class="sxs-lookup"><span data-stu-id="b8e3b-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="b8e3b-116">die Verbindung mit dem Statuscode 400 („Ungültige Anforderung“) schließen und die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="b8e3b-117">Hsts-und API-Projekte</span><span class="sxs-lookup"><span data-stu-id="b8e3b-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="b8e3b-118">Die Standard-API-Projekte enthalten keine [hsts](#hsts) , da hsts im Allgemeinen eine reine Browser Anweisung ist.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="b8e3b-119">Andere Aufrufer, z. b. Telefon-oder Desktop-Apps, gehorchen **nicht** der Anweisung.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="b8e3b-120">Auch innerhalb von Browsern hat ein einzelner authentifizierter API-API-Rückruf Risiken für unsichere Netzwerke.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="b8e3b-121">Der sichere Ansatz besteht darin, API-Projekte so zu konfigurieren, dass Sie nur über HTTPS lauschen und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="b8e3b-122">API-Projekte</span><span class="sxs-lookup"><span data-stu-id="b8e3b-122">API projects</span></span>
>
> <span data-ttu-id="b8e3b-123">Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="b8e3b-124">`RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="b8e3b-125">API-Clients verstehen diese Codes möglicherweise nicht, oder Sie führen keine Weiterleitung von HTTP an HTTPS durch.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="b8e3b-126">Dies kann dazu führen, dass solche Clients Daten unverschlüsselt mittels HTTP versenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="b8e3b-127">Web-APIs sollten daher entweder:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="b8e3b-128">nicht auf HTTP lauschen oder</span><span class="sxs-lookup"><span data-stu-id="b8e3b-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="b8e3b-129">die Verbindung mit dem Statuscode 400 („Ungültige Anforderung“) schließen und die Anforderung nicht verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="b8e3b-130">HTTPS erforderlich</span><span class="sxs-lookup"><span data-stu-id="b8e3b-130">Require HTTPS</span></span>

<span data-ttu-id="b8e3b-131">Es wird empfohlen, dass die Web-Apps für die Produktion ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="b8e3b-132">Die HTTPS-Umleitungs Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) zum Umleiten von HTTP-Anforderungen an HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="b8e3b-133">Hsts-Middleware ([usehsts](#http-strict-transport-security-protocol-hsts)) zum Senden von http-hsts-Headern (Strict Transport Security Protocol) an Clients.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="b8e3b-134">Apps, die in einer Reverseproxykonfiguration bereitgestellt werden, ermöglichen dem Proxy das Verarbeiten der Verbindungssicherheit (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="b8e3b-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="b8e3b-135">Wenn der Proxy auch die HTTPS-Umleitung verarbeitet, muss keine HTTPS-Umleitungs Middleware verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b8e3b-136">Wenn der Proxy Server auch das Schreiben von hsts-Headern verarbeitet (z. b. System [eigene hsts-Unterstützung in IIS 10,0 (1709) oder](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)höher), ist hsts-Middleware für die APP nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="b8e3b-137">Weitere Informationen finden Sie unter [Ablehnen von HTTPS/hsts bei der Projekt Erstellung](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="b8e3b-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="b8e3b-138">UseHttpsRedirection</span></span>

<span data-ttu-id="b8e3b-139">Der folgende Code ruft `UseHttpsRedirection` in der `Startup`-Klasse auf:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="b8e3b-140">Der vorangehende markierte Code:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="b8e3b-141">Verwendet den Standardwert [httpsredirectionoptions. redirectstatus Code](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="b8e3b-142">Verwendet den Standardwert [httpsredirectionoptions. httpsport](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), es sei denn, er wurde durch die `ASPNETCORE_HTTPS_PORT`-Umgebungsvariable oder [iserveraddressesfeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)überschrieben.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="b8e3b-143">Es wird empfohlen, temporäre Umleitungen anstelle von permanenten Umleitungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="b8e3b-144">Das Zwischenspeichern von Links kann in Entwicklungsumgebungen zu instabiles Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="b8e3b-145">Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, finden Sie weitere Informationen im Abschnitt [configure permanent Umleitungen in Production](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="b8e3b-146">Wir empfehlen die Verwendung von [hsts](#http-strict-transport-security-protocol-hsts) , um Clients zu signalisieren, dass nur sichere Ressourcenanforderungen an die APP gesendet werden sollen (nur in der Produktion).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="b8e3b-147">Port Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b8e3b-147">Port configuration</span></span>

<span data-ttu-id="b8e3b-148">Ein Port muss verfügbar sein, damit die Middleware eine unsichere Anforderung an HTTPS umleiten muss.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="b8e3b-149">Wenn kein Port verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-149">If no port is available:</span></span>

* <span data-ttu-id="b8e3b-150">Umleitung zu HTTPS erfolgt nicht.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="b8e3b-151">Die Middleware protokolliert die Warnung "der HTTPS-Port für die Umleitung konnte nicht bestimmt werden".</span><span class="sxs-lookup"><span data-stu-id="b8e3b-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="b8e3b-152">Geben Sie den HTTPS-Port an, indem Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="b8e3b-153">Legen Sie [httpsredirectionoptions. httpsport](#options)fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b8e3b-154">Legen Sie die [Einstellung](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)für den `https_port` Host fest:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="b8e3b-155">In der Host Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-155">In host configuration.</span></span>
  * <span data-ttu-id="b8e3b-156">Durch Festlegen der `ASPNETCORE_HTTPS_PORT`-Umgebungsvariable.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="b8e3b-157">Durch Hinzufügen eines Eintrags auf oberster Ebene in *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="b8e3b-158">Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="b8e3b-159">Die Umgebungsvariable konfiguriert den Server.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-159">The environment variable configures the server.</span></span> <span data-ttu-id="b8e3b-160">Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="b8e3b-161">Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="b8e3b-162">Legen Sie die [Einstellung](xref:fundamentals/host/web-host#https-port)für den `https_port` Host fest:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="b8e3b-163">In der Host Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-163">In host configuration.</span></span>
  * <span data-ttu-id="b8e3b-164">Durch Festlegen der `ASPNETCORE_HTTPS_PORT`-Umgebungsvariable.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="b8e3b-165">Durch Hinzufügen eines Eintrags auf oberster Ebene in *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="b8e3b-166">Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](xref:fundamentals/host/web-host#server-urls)verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="b8e3b-167">Die Umgebungsvariable konfiguriert den Server.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-167">The environment variable configures the server.</span></span> <span data-ttu-id="b8e3b-168">Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="b8e3b-169">Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="b8e3b-170">Legen Sie in der Entwicklung eine HTTPS-URL in " *launchsettings. JSON*" fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="b8e3b-171">Aktivieren Sie HTTPS, wenn IIS Express verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="b8e3b-172">Konfigurieren Sie einen HTTPS-URL-Endpunkt für eine Public-Edge-Bereitstellung von [Kestrel](xref:fundamentals/servers/kestrel) -Server oder [http. sys](xref:fundamentals/servers/httpsys) -Server.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="b8e3b-173">Nur **ein HTTPS-Port** wird von der APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="b8e3b-174">Die Middleware ermittelt den Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="b8e3b-175">Wenn eine app in einer Reverseproxykonfiguration ausgeführt wird, ist <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="b8e3b-176">Legen Sie den Port mithilfe eines der anderen in diesem Abschnitt beschriebenen Ansätze fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="b8e3b-177">Edge-bereit Stellungen</span><span class="sxs-lookup"><span data-stu-id="b8e3b-177">Edge deployments</span></span> 

<span data-ttu-id="b8e3b-178">Wenn Kestrel oder http. sys als Public-Edge-Server verwendet wird, müssen Kestrel oder http. sys so konfiguriert werden, dass Sie auf beiden Servern lauschen:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="b8e3b-179">Der sichere Port, an den der Client umgeleitet wird (in der Regel 443 in der Produktionsumgebung und 5001 in der Entwicklung).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="b8e3b-180">Der unsichere Port (in der Regel 80 in der Produktionsumgebung und 5000 in der Entwicklung).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="b8e3b-181">Der Client muss auf den unsicheren Port zugreifen können, damit die APP eine unsichere Anforderung empfängt und den Client an den sicheren Port umleitet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="b8e3b-182">Weitere Informationen finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) oder <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="b8e3b-183">Bereitstellungsszenarien</span><span class="sxs-lookup"><span data-stu-id="b8e3b-183">Deployment scenarios</span></span>

<span data-ttu-id="b8e3b-184">Für jede Firewall zwischen dem Client und dem Server müssen auch Kommunikationsports für den Datenverkehr geöffnet sein.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="b8e3b-185">Wenn Anforderungen in einer Reverseproxykonfiguration weitergeleitet werden, verwenden Sie die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) , bevor Sie die HTTPS-Umleitungs</span><span class="sxs-lookup"><span data-stu-id="b8e3b-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b8e3b-186">Die Middleware für weitergeleitete Header aktualisiert die `Request.Scheme`mithilfe des `X-Forwarded-Proto`-Headers.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="b8e3b-187">Die Middleware ermöglicht das ordnungsgemäße Funktionieren von Umleitungs-URIs und anderen Sicherheitsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="b8e3b-188">Wenn die Middleware für weitergeleitete Header nicht verwendet wird, empfängt die Back-End-App möglicherweise nicht das richtige Schema und befindet sich in einer Umleitungs Schleife.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="b8e3b-189">Eine gängige Fehlermeldung für den Endbenutzer besteht darin, dass zu viele Umleitungen aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="b8e3b-190">Wenn Sie Azure App Service bereitstellen, befolgen Sie die Anweisungen im [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="b8e3b-191">Optionen</span><span class="sxs-lookup"><span data-stu-id="b8e3b-191">Options</span></span>

<span data-ttu-id="b8e3b-192">Der folgende markierte Code ruft [addhttpsredirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) auf, um Middleware-Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="b8e3b-193">Das Aufrufen von `AddHttpsRedirection` ist nur erforderlich, um die Werte von `HttpsPort` oder `RedirectStatusCode`zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="b8e3b-194">Der vorangehende markierte Code:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="b8e3b-195">Legt [httpsredirectionoptions. redirectstatus Code](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) auf <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>fest. Dies ist der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="b8e3b-196">Verwenden Sie die Felder der <xref:Microsoft.AspNetCore.Http.StatusCodes>-Klasse, um Zuweisungen zu `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="b8e3b-197">Legt den HTTPS-Port auf 5001 fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-197">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="b8e3b-198">Der Standardwert ist 443.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-198">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="b8e3b-199">Konfigurieren dauerhafter Umleitungen in der Produktion</span><span class="sxs-lookup"><span data-stu-id="b8e3b-199">Configure permanent redirects in production</span></span>

<span data-ttu-id="b8e3b-200">Die Middleware sendet standardmäßig ein [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) -Wert mit allen Umleitungen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-200">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="b8e3b-201">Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, packen Sie die middlewareoptions-Konfiguration in eine bedingte Prüfung für eine nicht Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-201">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8e3b-202">Beim Konfigurieren von Diensten in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
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

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b8e3b-203">Beim Konfigurieren von Diensten in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-203">When configuring services in *Startup.cs*:</span></span>

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

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="b8e3b-204">Alternativer Ansatz der HTTPS-Umleitungs Middleware</span><span class="sxs-lookup"><span data-stu-id="b8e3b-204">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="b8e3b-205">Eine Alternative zur Verwendung der HTTPS-Umleitungs Middleware (`UseHttpsRedirection`) ist die Verwendung der URL-Umschreib enden Middleware (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-205">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="b8e3b-206">`AddRedirectToHttps` können auch den Statuscode und den Port festlegen, wenn die Umleitung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-206">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="b8e3b-207">Weitere Informationen finden Sie unter [URL-Umschreib Ende Middleware](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="b8e3b-208">Beim Umleiten an HTTPS ohne die Anforderung zusätzlicher Umleitungs Regeln empfiehlt sich die Verwendung von HTTPS-Umleitungs Middleware (`UseHttpsRedirection`), die in diesem Thema beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-208">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="b8e3b-209">HTTP Strict Transport Security Protocol (hsts)</span><span class="sxs-lookup"><span data-stu-id="b8e3b-209">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="b8e3b-210">Pro [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)ist die [http-strikte Transport Sicherheit (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) eine Opt-in-Sicherheits Erweiterung, die durch eine Web-App durch die Verwendung eines Antwort Headers angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-210">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="b8e3b-211">Wenn ein [Browser, der hsts unterstützt,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) diesen Header empfängt:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-211">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="b8e3b-212">Der Browser speichert die Konfiguration für die Domäne, die das Senden jeglicher Kommunikation über HTTP verhindert.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-212">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="b8e3b-213">Der Browser erzwingt die gesamte Kommunikation über HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-213">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="b8e3b-214">Der Browser hindert den Benutzer daran, nicht vertrauenswürdige oder ungültige Zertifikate zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-214">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="b8e3b-215">Der Browser deaktiviert Eingabe Aufforderungen, mit denen ein Benutzer ein solches Zertifikat vorübergehend als vertrauenswürdig einstufen kann.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-215">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="b8e3b-216">Da hsts vom Client erzwungen wird, gelten einige Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-216">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="b8e3b-217">Der Client muss hsts unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-217">The client must support HSTS.</span></span>
* <span data-ttu-id="b8e3b-218">Hsts erfordert mindestens eine erfolgreiche HTTPS-Anforderung zum Einrichten der hsts-Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-218">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="b8e3b-219">Die Anwendung muss jede HTTP-Anforderung überprüfen und die HTTP-Anforderung umleiten oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-219">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="b8e3b-220">ASP.net Core 2,1 und höher implementiert hsts mit der `UseHsts`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-220">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="b8e3b-221">Der folgende Code ruft `UseHsts` auf, wenn sich die APP nicht im [Entwicklungsmodus befindet](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="b8e3b-221">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="b8e3b-222">`UseHsts` wird in der Entwicklung nicht empfohlen, da die hsts-Einstellungen durch Browser stark zwischen speicherbar sind.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-222">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="b8e3b-223">Standardmäßig schließt `UseHsts` die lokale Loopback Adresse aus.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-223">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="b8e3b-224">Legen Sie für Produktionsumgebungen, die HTTPS zum ersten Mal implementieren, die anfängliche [hstsoptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) -Einstellung auf einen kleinen Wert fest, indem Sie eine der <xref:System.TimeSpan> Methoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-224">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="b8e3b-225">Legen Sie den Wert von Stunden auf nicht mehr als einen Tag fest, falls Sie die HTTPS-Infrastruktur auf http zurücksetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-225">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="b8e3b-226">Nachdem Sie sicher sind, dass die HTTPS-Konfiguration in der Lage ist, erhöhen Sie den Wert für hsts max-age. ein häufig verwendeter Wert ist ein Jahr.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-226">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="b8e3b-227">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="b8e3b-227">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="b8e3b-228">Legt den vorab laden-Parameter des Strict-Transport-Security-Headers fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-228">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="b8e3b-229">Vorab laden ist nicht Teil der [RFC hsts-Spezifikation](https://tools.ietf.org/html/rfc6797), wird aber von Webbrowsern unterstützt, um hsts-Websites bei einer Neuinstallation vorab zu laden.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-229">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="b8e3b-230">Weitere Informationen finden Sie unter [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-230">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="b8e3b-231">Aktiviert [includesubdomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), die die hsts-Richtlinie auf Host Unterdomänen anwendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-231">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="b8e3b-232">Legt den max-age-Parameter des Strict-Transport-Security-Headers explizit auf 60 Tage fest.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-232">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="b8e3b-233">Wenn nicht festgelegt, wird standardmäßig 30 Tage verwendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-233">If not set, defaults to 30 days.</span></span> <span data-ttu-id="b8e3b-234">Weitere Informationen finden Sie in der [max-age-Direktive](https://tools.ietf.org/html/rfc6797#section-6.1.1) .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-234">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="b8e3b-235">Fügt der Liste der auszuschließenden Hosts `example.com` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-235">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="b8e3b-236">`UseHsts` schließt die folgenden Loopback Hosts aus:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-236">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="b8e3b-237">`localhost`: die IPv4-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-237">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="b8e3b-238">`127.0.0.1`: die IPv4-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-238">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="b8e3b-239">`[::1]`: die IPv6-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-239">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="b8e3b-240">Ausschließen von HTTPS/hsts bei der Projekt Erstellung</span><span class="sxs-lookup"><span data-stu-id="b8e3b-240">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="b8e3b-241">In einigen Back-End-Dienst Szenarios, in denen die Verbindungssicherheit an der öffentlichen Kante des Netzwerks behandelt wird, ist die Konfiguration der Verbindungssicherheit an den einzelnen Knoten nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-241">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="b8e3b-242">Web-Apps, die aus den Vorlagen in Visual Studio oder dem [dotnet New](/dotnet/core/tools/dotnet-new) -Befehl generiert werden, ermöglichen die [HTTPS-Umleitung](#require-https) und [hsts](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-242">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="b8e3b-243">Bei bereit Stellungen, für die diese Szenarios nicht erforderlich sind, können Sie HTTPS/hsts bei der Erstellung der APP aus der Vorlage ablehnen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-243">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="b8e3b-244">So beenden Sie HTTPS/hsts:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-244">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b8e3b-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8e3b-245">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b8e3b-246">Deaktivieren Sie das Kontrollkästchen **für HTTPS konfigurieren** .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-246">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b8e3b-249">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b8e3b-249">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="b8e3b-250">Verwenden Sie die `--no-https`-Option.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-250">Use the `--no-https` option.</span></span> <span data-ttu-id="b8e3b-251">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-251">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="b8e3b-252">Vertrauen des ASP.net Core HTTPS-Entwicklungs Zertifikats unter Windows und macOS</span><span class="sxs-lookup"><span data-stu-id="b8e3b-252">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="b8e3b-253">Die .net Core SDK enthält ein HTTPS-Entwicklungs Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-253">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="b8e3b-254">Das Zertifikat wird im Rahmen der erstmaligen Durchführung installiert.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-254">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="b8e3b-255">Beispielsweise erzeugt `dotnet --info` eine Ausgabe ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-255">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="b8e3b-256">Durch das Installieren des ASP.NET Core SDK wird das ASP.NET Core-HTTPS-Entwicklungszertifikat im lokalen Benutzerzertifikatspeicher installiert.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-256">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="b8e3b-257">Das Zertifikat wurde installiert, aber es ist nicht vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-257">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="b8e3b-258">Um dem Zertifikat zu vertrauen, führen Sie den einmaligen Schritt aus, um das dotnet `dev-certs`-Tool auszuführen:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-258">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="b8e3b-259">Der folgende Befehl stellt Hilfe zum `dev-certs`-Tool bereit:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-259">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="b8e3b-260">Einrichten eines Entwickler Zertifikats für docker</span><span class="sxs-lookup"><span data-stu-id="b8e3b-260">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="b8e3b-261">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="b8e3b-261">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="b8e3b-262">Vertrauen des HTTPS-Zertifikats aus dem Windows-Subsystem für Linux</span><span class="sxs-lookup"><span data-stu-id="b8e3b-262">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="b8e3b-263">Das Windows-Subsystem für Linux (WSL) generiert ein selbst signiertes HTTPS-Zertifikat. So konfigurieren Sie den Windows-Zertifikat Speicher für die Vertrauenswürdigkeit des WSL-Zertifikats:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-263">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="b8e3b-264">Führen Sie den folgenden Befehl aus, um das von WSL generierte Zertifikat zu exportieren: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="b8e3b-264">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="b8e3b-265">Führen Sie in einem WSL-Fenster den folgenden Befehl aus: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="b8e3b-265">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="b8e3b-266">Mit dem vorangehenden Befehl werden die Umgebungsvariablen festgelegt, sodass Linux das vertrauenswürdige Windows-Zertifikat verwendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-266">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="b8e3b-267">Beheben von Zertifikats Problemen</span><span class="sxs-lookup"><span data-stu-id="b8e3b-267">Troubleshoot certificate problems</span></span>

<span data-ttu-id="b8e3b-268">Dieser Abschnitt enthält Hilfe, wenn das ASP.net Core HTTPS-Entwicklungs Zertifikat [installiert und vertrauenswürdig](#trust)ist, Sie jedoch weiterhin Browser Warnungen haben, dass das Zertifikat nicht vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-268">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="b8e3b-269">Das ASP.net Core HTTPS-Entwicklungs Zertifikat wird von [Kestrel](xref:fundamentals/servers/kestrel)verwendet.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-269">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="b8e3b-270">Alle Plattformen-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="b8e3b-270">All platforms - certificate not trusted</span></span>

<span data-ttu-id="b8e3b-271">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-271">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b8e3b-272">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-272">Close any browser instances open.</span></span> <span data-ttu-id="b8e3b-273">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-273">Open a new browser window to app.</span></span> <span data-ttu-id="b8e3b-274">Die Zertifikats Vertrauensstellung wird von Browsern zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-274">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="b8e3b-275">Mit den obigen Befehlen werden die meisten Browser Vertrauensstellungs Probleme gelöst.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-275">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="b8e3b-276">Wenn der Browser dem Zertifikat immer noch nicht vertraut, befolgen Sie die folgenden plattformspezifischen Vorschläge.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-276">If the browser is still not trusting the certificate, follow the platform specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="b8e3b-277">Docker-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="b8e3b-277">Docker - certificate not trusted</span></span>

* <span data-ttu-id="b8e3b-278">Löschen Sie den Ordner *c:\Users\{User} \appdata\roaming\asp.net\https* .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-278">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="b8e3b-279">Bereinigen Sie die Projekt Mappe.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-279">Clean the solution.</span></span> <span data-ttu-id="b8e3b-280">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-280">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="b8e3b-281">Starten Sie das Entwicklungs Tool neu.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-281">Restart the development tool.</span></span> <span data-ttu-id="b8e3b-282">Beispielsweise Visual Studio, Visual Studio Code oder Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-282">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="b8e3b-283">Windows-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="b8e3b-283">Windows - certificate not trusted</span></span>

* <span data-ttu-id="b8e3b-284">Überprüfen Sie die Zertifikate im Zertifikat Speicher.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-284">Check the certificates in the certificate store.</span></span> <span data-ttu-id="b8e3b-285">Es sollte ein `localhost` Zertifikat mit dem `ASP.NET Core HTTPS development certificate` anzeigen Amen unter `Current User > Personal > Certificates` und `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="b8e3b-285">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="b8e3b-286">Entfernen Sie alle gefundenen Zertifikate von persönlichen und vertrauenswürdigen Stamm Zertifizierungsstellen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-286">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="b8e3b-287">Entfernen Sie das IIS Express localhost-Zertifikat **nicht** .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-287">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="b8e3b-288">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-288">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b8e3b-289">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-289">Close any browser instances open.</span></span> <span data-ttu-id="b8e3b-290">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-290">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="b8e3b-291">OS X-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="b8e3b-291">OS X - certificate not trusted</span></span>

* <span data-ttu-id="b8e3b-292">Öffnen Sie den Keychain-Zugriff.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-292">Open KeyChain Access.</span></span>
* <span data-ttu-id="b8e3b-293">Wählen Sie die System Schlüsselkette aus.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-293">Select the System keychain.</span></span>
* <span data-ttu-id="b8e3b-294">Überprüfen Sie, ob ein localhost-Zertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-294">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="b8e3b-295">Überprüfen Sie, ob es ein `+` Symbol auf dem Symbol enthält, um dessen vertrauenswürdig für alle Benutzer anzugeben.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-295">Check that it contains a `+` symbol on the icon to indicate its trusted for all users.</span></span>
* <span data-ttu-id="b8e3b-296">Entfernen Sie das Zertifikat aus der Keychain des Systems.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-296">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="b8e3b-297">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b8e3b-297">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b8e3b-298">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-298">Close any browser instances open.</span></span> <span data-ttu-id="b8e3b-299">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-299">Open a new browser window to app.</span></span>

<span data-ttu-id="b8e3b-300">Informationen zur Behandlung von Zertifikat Problemen mit Visual Studio finden [Sie unter https-Fehler mit IIS Express (ASPNET/aspnetcore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) .</span><span class="sxs-lookup"><span data-stu-id="b8e3b-300">See [HTTPS Error using IIS Express (aspnet/AspNetCore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="b8e3b-301">IIS Express SSL-Zertifikat, das mit Visual Studio verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-301">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="b8e3b-302">Um Probleme mit dem IIS Express Zertifikat zu beheben, wählen Sie im Visual Studio-Installer **Reparieren** aus.</span><span class="sxs-lookup"><span data-stu-id="b8e3b-302">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span>

## <a name="additional-information"></a><span data-ttu-id="b8e3b-303">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="b8e3b-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="b8e3b-304">Host ASP.net Core unter Linux mit Apache: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b8e3b-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="b8e3b-305">Host ASP.net Core unter Linux mit Nginx: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b8e3b-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="b8e3b-306">Einrichten von SSL unter IIS</span><span class="sxs-lookup"><span data-stu-id="b8e3b-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="b8e3b-307">OWASP hsts-Browserunterstützung</span><span class="sxs-lookup"><span data-stu-id="b8e3b-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
