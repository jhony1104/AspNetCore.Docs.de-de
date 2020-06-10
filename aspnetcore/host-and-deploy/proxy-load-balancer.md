---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 9299117b45a71b7aaf761fc3a0a4e541373dd970
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106296"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="03fa1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-103">'Identity'</span></span>

<span data-ttu-id="03fa1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03fa1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-105">'Razor'</span></span> <span data-ttu-id="03fa1-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-106">'SignalR' uid:</span></span>

* <span data-ttu-id="03fa1-107">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="03fa1-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>
* <span data-ttu-id="03fa1-108">Von [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="03fa1-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="03fa1-109">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="03fa1-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="03fa1-110">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="03fa1-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

<span data-ttu-id="03fa1-111">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>

| <span data-ttu-id="03fa1-112">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span> | <span data-ttu-id="03fa1-113">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="03fa1-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span> |
| ------ | ----------- |
| <span data-ttu-id="03fa1-114">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="03fa1-114">Forwarded headers</span></span> | <span data-ttu-id="03fa1-115">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="03fa1-115">By convention, proxies forward information in HTTP headers.</span></span> <span data-ttu-id="03fa1-116">Header</span><span class="sxs-lookup"><span data-stu-id="03fa1-116">Header</span></span> <span data-ttu-id="03fa1-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="03fa1-117">Description</span></span> <span data-ttu-id="03fa1-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-119">'Blazor'</span></span> <span data-ttu-id="03fa1-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-120">'Identity'</span></span> |
| <span data-ttu-id="03fa1-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-121">'Let's Encrypt'</span></span> | <span data-ttu-id="03fa1-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-122">'Razor'</span></span> <span data-ttu-id="03fa1-123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-123">'SignalR' uid:</span></span> |
| <span data-ttu-id="03fa1-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="03fa1-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-125">'Blazor'</span></span> <span data-ttu-id="03fa1-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-126">'Identity'</span></span> <span data-ttu-id="03fa1-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-127">'Let's Encrypt'</span></span> |

<span data-ttu-id="03fa1-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-128">'Razor'</span></span>

<span data-ttu-id="03fa1-129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-129">'SignalR' uid:</span></span>

* <span data-ttu-id="03fa1-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-131">'Blazor'</span></span> <span data-ttu-id="03fa1-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-132">'Identity'</span></span>
* <span data-ttu-id="03fa1-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-133">'Let's Encrypt'</span></span>
* <span data-ttu-id="03fa1-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-134">'Razor'</span></span>

<span data-ttu-id="03fa1-135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-135">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="03fa1-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-137">'Blazor'</span></span>
* <span data-ttu-id="03fa1-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-138">'Identity'</span></span>
* <span data-ttu-id="03fa1-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-139">'Let's Encrypt'</span></span>

<span data-ttu-id="03fa1-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-140">'Razor'</span></span> <span data-ttu-id="03fa1-141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-141">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-142">------ | | X-Forwarded-For | Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="03fa1-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="03fa1-143">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="03fa1-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="03fa1-144">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="03fa1-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span>

<span data-ttu-id="03fa1-145">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="03fa1-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="03fa1-146">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="03fa1-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="03fa1-147">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="03fa1-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="03fa1-148">| | X-Forwarded-Proto | Der Wert des ursprünglichen Schemas (HTTP/HTTPS)</span><span class="sxs-lookup"><span data-stu-id="03fa1-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="03fa1-149">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="03fa1-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span>

<span data-ttu-id="03fa1-150">| | X-Forwarded-Host | Der ursprüngliche Wert des Hostheaderfelds</span><span class="sxs-lookup"><span data-stu-id="03fa1-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="03fa1-151">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="03fa1-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="03fa1-152">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="03fa1-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span>

<span data-ttu-id="03fa1-153">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="03fa1-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="03fa1-154">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="03fa1-154">The middleware updates:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="03fa1-155">[HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="03fa1-156">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="03fa1-157">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="03fa1-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

<span data-ttu-id="03fa1-158">[HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span> <span data-ttu-id="03fa1-159">[HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="03fa1-160">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span>

<span data-ttu-id="03fa1-161">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="03fa1-161">The default settings are:</span></span> <span data-ttu-id="03fa1-162">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-162">There is only *one proxy* between the app and the source of the requests.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="03fa1-163">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="03fa1-163">Only loopback addresses are configured for known proxies and known networks.</span></span>

<span data-ttu-id="03fa1-164">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span> <span data-ttu-id="03fa1-165">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span>

* <span data-ttu-id="03fa1-166">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span>
* <span data-ttu-id="03fa1-167">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="03fa1-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span>
* <span data-ttu-id="03fa1-168">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="03fa1-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="03fa1-169">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="03fa1-169">IIS/IIS Express and ASP.NET Core Module</span></span> | <span data-ttu-id="03fa1-170">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="03fa1-171">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="03fa1-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span><ul><li><span data-ttu-id="03fa1-172">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span></li><li><span data-ttu-id="03fa1-173">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="03fa1-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span></li><li><span data-ttu-id="03fa1-174">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="03fa1-174">Other proxy server and load balancer scenarios</span></span></li><li><span data-ttu-id="03fa1-175">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span></li><li><span data-ttu-id="03fa1-176">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="03fa1-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="03fa1-177">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span></li><li><span data-ttu-id="03fa1-178">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span></li><li><span data-ttu-id="03fa1-179">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="03fa1-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span> <span data-ttu-id="03fa1-180">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span></li><li><span data-ttu-id="03fa1-181">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span></li></ul><span data-ttu-id="03fa1-182">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="03fa1-182">Nginx configuration</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="03fa1-183">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="03fa1-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="03fa1-184">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="03fa1-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span><br><br><span data-ttu-id="03fa1-185">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="03fa1-185">Apache configuration</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="03fa1-186">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="03fa1-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="03fa1-187">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="03fa1-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span> <span data-ttu-id="03fa1-188">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="03fa1-188">Forwarded Headers Middleware options</span></span><br><br><span data-ttu-id="03fa1-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="03fa1-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="03fa1-190">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="03fa1-190">The following example changes the default values:</span></span> <span data-ttu-id="03fa1-191">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-191">Limit the number of entries in the forwarded headers to `2`.</span></span><br><br><span data-ttu-id="03fa1-192">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="03fa1-192">Add a known proxy address of `127.0.10.1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="03fa1-193">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span> <span data-ttu-id="03fa1-194">Option</span><span class="sxs-lookup"><span data-stu-id="03fa1-194">Option</span></span><br><br><span data-ttu-id="03fa1-195">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="03fa1-195">Description</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="03fa1-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-197">'Blazor'</span></span> <span data-ttu-id="03fa1-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-198">'Identity'</span></span><br><br><span data-ttu-id="03fa1-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-199">'Let's Encrypt'</span></span> <span data-ttu-id="03fa1-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-200">'Razor'</span></span><br><br><span data-ttu-id="03fa1-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-201">'SignalR' uid:</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="03fa1-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-203">'Blazor'</span></span><br><br><span data-ttu-id="03fa1-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-204">'Identity'</span></span> <span data-ttu-id="03fa1-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-205">'Let's Encrypt'</span></span> <span data-ttu-id="03fa1-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-206">'Razor'</span></span> <span data-ttu-id="03fa1-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-207">'SignalR' uid:</span></span><br><br><span data-ttu-id="03fa1-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="03fa1-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-209">'Blazor'</span></span> <span data-ttu-id="03fa1-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-210">'Identity'</span></span><br><br><span data-ttu-id="03fa1-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-211">'Let's Encrypt'</span></span> <span data-ttu-id="03fa1-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-212">'Razor'</span></span> <span data-ttu-id="03fa1-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-213">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span><br><br><span data-ttu-id="03fa1-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-215">'Blazor'</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="03fa1-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-216">'Identity'</span></span><br><br><span data-ttu-id="03fa1-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-217">'Let's Encrypt'</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="03fa1-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-218">'Razor'</span></span><br><br><span data-ttu-id="03fa1-219">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-219">'SignalR' uid:</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="03fa1-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte</span><span class="sxs-lookup"><span data-stu-id="03fa1-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><br><br><span data-ttu-id="03fa1-221">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-221">Values are compared using ordinal-ignore-case.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="03fa1-222">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-222">Port numbers must be excluded.</span></span><br><br><span data-ttu-id="03fa1-223">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="03fa1-223">If the list is empty, all hosts are allowed.</span></span> <span data-ttu-id="03fa1-224">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="03fa1-224">A top-level wildcard `*` allows all non-empty hosts.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="03fa1-225">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="03fa1-225">Subdomain wildcards are permitted but don't match the root domain.</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="03fa1-226">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span>

<span data-ttu-id="03fa1-227">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="03fa1-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span> <span data-ttu-id="03fa1-228">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="03fa1-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="03fa1-229">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="03fa1-230">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span>

<span data-ttu-id="03fa1-231">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="03fa1-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span>

<span data-ttu-id="03fa1-233">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="03fa1-234">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="03fa1-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Gibt an, welche Weiterleitungen verarbeitet werden sollen</span><span class="sxs-lookup"><span data-stu-id="03fa1-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span>

<span data-ttu-id="03fa1-236">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="03fa1-237">Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="03fa1-238">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="03fa1-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="03fa1-240">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span>

<span data-ttu-id="03fa1-241">Der Standardwert ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="03fa1-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="03fa1-243">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span>

<span data-ttu-id="03fa1-244">Der Standardwert ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-244">The default is `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="03fa1-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="03fa1-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span>

<span data-ttu-id="03fa1-246">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="03fa1-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span>  
<span data-ttu-id="03fa1-247">Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="03fa1-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span>

<span data-ttu-id="03fa1-248">Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="03fa1-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="03fa1-249">Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="03fa1-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="03fa1-250">Der Standardwert ist `1`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-250">The default is `1`.</span></span>

<span data-ttu-id="03fa1-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Adressbereiche bekannter Netzwerke, von denen weitergeleitete Header akzeptiert werden müssen</span><span class="sxs-lookup"><span data-stu-id="03fa1-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="03fa1-252">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="03fa1-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span> <span data-ttu-id="03fa1-253">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="03fa1-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="03fa1-254">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="03fa1-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span>

<span data-ttu-id="03fa1-255">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="03fa1-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="03fa1-256">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="03fa1-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="03fa1-257">Der Standard ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> 

### <a name="azure"></a><span data-ttu-id="03fa1-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen</span><span class="sxs-lookup"><span data-stu-id="03fa1-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span>

<span data-ttu-id="03fa1-259">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-259">Use `KnownProxies` to specify exact IP address matches.</span></span> <span data-ttu-id="03fa1-260">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="03fa1-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span>

<span data-ttu-id="03fa1-261">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="03fa1-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="03fa1-262">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="03fa1-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="03fa1-263">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="03fa1-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="03fa1-264">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span>

<span data-ttu-id="03fa1-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span> <span data-ttu-id="03fa1-266">Der Standardwert ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-266">The default is `X-Original-For`.</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="03fa1-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span> <span data-ttu-id="03fa1-268">Der Standardwert ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-268">The default is `X-Original-Host`.</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="03fa1-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span> <span data-ttu-id="03fa1-270">Der Standardwert ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-270">The default is `X-Original-Proto`.</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="03fa1-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span>

<span data-ttu-id="03fa1-272">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="03fa1-273">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> <span data-ttu-id="03fa1-274">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="03fa1-274">Scenarios and use cases</span></span> 

<span data-ttu-id="03fa1-275">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="03fa1-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="03fa1-276">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="03fa1-277">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03fa1-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

<span data-ttu-id="03fa1-278">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

* <span data-ttu-id="03fa1-279">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="03fa1-279">Deal with path base and proxies that change the request path</span></span>
* <span data-ttu-id="03fa1-280">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="03fa1-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="03fa1-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="03fa1-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span> <span data-ttu-id="03fa1-282">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="03fa1-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span> <span data-ttu-id="03fa1-283">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span>

<span data-ttu-id="03fa1-284">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="03fa1-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span> <span data-ttu-id="03fa1-285">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="03fa1-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span> <span data-ttu-id="03fa1-286">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="03fa1-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="03fa1-287">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="03fa1-287">Configuration for a proxy that uses different header names</span></span> <span data-ttu-id="03fa1-288">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="03fa1-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span> <span data-ttu-id="03fa1-289">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="03fa1-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="03fa1-290">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="03fa1-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="03fa1-291">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="03fa1-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03fa1-292">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="03fa1-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="03fa1-293">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03fa1-294">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="03fa1-294">IPv4 address: `10.11.12.1/8`</span></span> <span data-ttu-id="03fa1-295">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="03fa1-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>

* <span data-ttu-id="03fa1-296">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="03fa1-296">Converted prefix length: 104</span></span>
* <span data-ttu-id="03fa1-297">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="03fa1-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span>

<span data-ttu-id="03fa1-298">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="03fa1-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="03fa1-299">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="03fa1-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="03fa1-300">Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span>

| <span data-ttu-id="03fa1-301">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="03fa1-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> | <span data-ttu-id="03fa1-302">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="03fa1-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> |
| ------ | ----------- |
| <span data-ttu-id="03fa1-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="03fa1-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span> | <span data-ttu-id="03fa1-304">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="03fa1-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="03fa1-305">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="03fa1-305">In `Startup.ConfigureServices`, use the following code:</span></span> <span data-ttu-id="03fa1-306">Zertifikatweiterleitung</span><span class="sxs-lookup"><span data-stu-id="03fa1-306">Certificate forwarding</span></span> <span data-ttu-id="03fa1-307">Azure</span><span class="sxs-lookup"><span data-stu-id="03fa1-307">Azure</span></span> <span data-ttu-id="03fa1-308">Informationen zum Konfigurieren von Azure App Service für die Zertifikatsweiterleitung finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="03fa1-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="03fa1-309">Die folgende Anleitung bezieht sich auf die Konfiguration der ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="03fa1-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span> |
| <span data-ttu-id="03fa1-310">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="03fa1-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span> | <span data-ttu-id="03fa1-311">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung, um den von Azure verwendeten Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="03fa1-312">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="03fa1-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span> |
| <span data-ttu-id="03fa1-313">Andere Webproxys</span><span class="sxs-lookup"><span data-stu-id="03fa1-313">Other web proxies</span></span> | <span data-ttu-id="03fa1-314">Wenn ein Proxy verwendet wird, der nicht IIS oder Routing von Anwendungsanforderungen von Azure App Service entspricht, konfigurieren Sie den Proxy so, dass das empfangene Zertifikat in einem HTTP-Header weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="03fa1-315">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="03fa1-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span> <span data-ttu-id="03fa1-316">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> |

<span data-ttu-id="03fa1-317">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="03fa1-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

<span data-ttu-id="03fa1-318">Wenn der Proxy das Zertifikat mit nicht mit base64 codiert (wie bei Nginx), legen Sie die Option `HeaderConverter` fest.</span><span class="sxs-lookup"><span data-stu-id="03fa1-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span>

* <span data-ttu-id="03fa1-319">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="03fa1-319">Consider the following example in `Startup.ConfigureServices`:</span></span> <span data-ttu-id="03fa1-320">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="03fa1-320">Troubleshoot</span></span> <span data-ttu-id="03fa1-321">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="03fa1-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span>
* <span data-ttu-id="03fa1-322">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="03fa1-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span>
* <span data-ttu-id="03fa1-323">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="03fa1-323">Use inline middleware to write request headers to an app response or log the headers.</span></span>

<span data-ttu-id="03fa1-324">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="03fa1-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span> <span data-ttu-id="03fa1-325">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-325">You can write to logs instead of the response body.</span></span>

* <span data-ttu-id="03fa1-326">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="03fa1-326">Writing to logs allows the site to function normally while debugging.</span></span>
* <span data-ttu-id="03fa1-327">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="03fa1-327">To write logs rather than to the response body:</span></span>
* <span data-ttu-id="03fa1-328">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>

<span data-ttu-id="03fa1-329">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span> <span data-ttu-id="03fa1-330">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="03fa1-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="03fa1-331">Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="03fa1-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="03fa1-332">Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="03fa1-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="03fa1-333">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span>

<span data-ttu-id="03fa1-334">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="03fa1-335">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="03fa1-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span> <span data-ttu-id="03fa1-336">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="03fa1-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="03fa1-337">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="03fa1-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="03fa1-338">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="03fa1-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

<span data-ttu-id="03fa1-339">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="03fa1-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="03fa1-340">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="03fa1-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span> <span data-ttu-id="03fa1-341">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="03fa1-341">Additional resources</span></span>

[<span data-ttu-id="03fa1-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="03fa1-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295) <span data-ttu-id="03fa1-343">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="03fa1-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="03fa1-344">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="03fa1-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span> <span data-ttu-id="03fa1-345">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="03fa1-346">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="03fa1-347">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="03fa1-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span> <span data-ttu-id="03fa1-348">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="03fa1-348">Forwarded headers</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="03fa1-349">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="03fa1-349">By convention, proxies forward information in HTTP headers.</span></span>

<span data-ttu-id="03fa1-350">Header</span><span class="sxs-lookup"><span data-stu-id="03fa1-350">Header</span></span> <span data-ttu-id="03fa1-351">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="03fa1-351">Description</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="03fa1-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="03fa1-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-353">'Blazor'</span></span> <span data-ttu-id="03fa1-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-354">'Identity'</span></span>

* <span data-ttu-id="03fa1-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-355">'Let's Encrypt'</span></span>
* <span data-ttu-id="03fa1-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-356">'Razor'</span></span>
* <span data-ttu-id="03fa1-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-357">'SignalR' uid:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="03fa1-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="03fa1-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-359">'Blazor'</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="03fa1-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-360">'Identity'</span></span><ul><li><span data-ttu-id="03fa1-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-361">'Let's Encrypt'</span></span></li><li><span data-ttu-id="03fa1-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-362">'Razor'</span></span></li><li><span data-ttu-id="03fa1-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-363">'SignalR' uid:</span></span></li><li><span data-ttu-id="03fa1-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></li><li><span data-ttu-id="03fa1-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-365">'Blazor'</span></span> <span data-ttu-id="03fa1-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-366">'Identity'</span></span></li><li><span data-ttu-id="03fa1-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-367">'Let's Encrypt'</span></span></li><li><span data-ttu-id="03fa1-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-368">'Razor'</span></span> <span data-ttu-id="03fa1-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-369">'SignalR' uid:</span></span></li><li><span data-ttu-id="03fa1-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span></li></ul><span data-ttu-id="03fa1-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-371">'Blazor'</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="03fa1-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-372">'Identity'</span></span> <span data-ttu-id="03fa1-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-373">'Let's Encrypt'</span></span><br><br><span data-ttu-id="03fa1-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-374">'Razor'</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="03fa1-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-375">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-376">------ | | X-Forwarded-For | Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="03fa1-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="03fa1-377">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="03fa1-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span><br><br><span data-ttu-id="03fa1-378">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="03fa1-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="03fa1-379">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="03fa1-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="03fa1-380">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="03fa1-380">The last proxy in the chain isn't in the list of parameters.</span></span><br><br><span data-ttu-id="03fa1-381">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="03fa1-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="03fa1-382">| | X-Forwarded-Proto | Der Wert des ursprünglichen Schemas (HTTP/HTTPS)</span><span class="sxs-lookup"><span data-stu-id="03fa1-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="03fa1-383">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="03fa1-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span><br><br><span data-ttu-id="03fa1-384">| | X-Forwarded-Host | Der ursprüngliche Wert des Hostheaderfelds</span><span class="sxs-lookup"><span data-stu-id="03fa1-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="03fa1-385">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="03fa1-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="03fa1-386">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="03fa1-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> <span data-ttu-id="03fa1-387">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="03fa1-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span><br><br><span data-ttu-id="03fa1-388">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="03fa1-388">The middleware updates:</span></span> <span data-ttu-id="03fa1-389">[HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span><br><br><span data-ttu-id="03fa1-390">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="03fa1-391">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="03fa1-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span> <span data-ttu-id="03fa1-392">[HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span><br><br><span data-ttu-id="03fa1-393">[HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span> <span data-ttu-id="03fa1-394">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="03fa1-395">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="03fa1-395">The default settings are:</span></span> <span data-ttu-id="03fa1-396">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-396">There is only *one proxy* between the app and the source of the requests.</span></span><br><br><span data-ttu-id="03fa1-397">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="03fa1-397">Only loopback addresses are configured for known proxies and known networks.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="03fa1-398">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span> <span data-ttu-id="03fa1-399">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span><br><br><span data-ttu-id="03fa1-400">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="03fa1-401">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="03fa1-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="03fa1-402">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="03fa1-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span> <span data-ttu-id="03fa1-403">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="03fa1-403">IIS/IIS Express and ASP.NET Core Module</span></span><br><br><span data-ttu-id="03fa1-404">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="03fa1-405">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="03fa1-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span><br><br><span data-ttu-id="03fa1-406">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="03fa1-407">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="03fa1-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span><br><br><span data-ttu-id="03fa1-408">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="03fa1-408">Other proxy server and load balancer scenarios</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="03fa1-409">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span><br><br><span data-ttu-id="03fa1-410">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="03fa1-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="03fa1-411">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span><br><br><span data-ttu-id="03fa1-412">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03fa1-413">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="03fa1-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="03fa1-414">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="03fa1-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="03fa1-415">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

<span data-ttu-id="03fa1-416">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="03fa1-416">Nginx configuration</span></span> <span data-ttu-id="03fa1-417">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="03fa1-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="03fa1-418">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="03fa1-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="03fa1-419">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="03fa1-419">Apache configuration</span></span>

<span data-ttu-id="03fa1-420">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="03fa1-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="03fa1-421">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="03fa1-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

<span data-ttu-id="03fa1-422">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="03fa1-422">Forwarded Headers Middleware options</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="03fa1-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="03fa1-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="03fa1-424">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="03fa1-424">The following example changes the default values:</span></span>

<span data-ttu-id="03fa1-425">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-425">Limit the number of entries in the forwarded headers to `2`.</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="03fa1-426">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="03fa1-426">Add a known proxy address of `127.0.10.1`.</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="03fa1-427">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

<span data-ttu-id="03fa1-428">Option</span><span class="sxs-lookup"><span data-stu-id="03fa1-428">Option</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="03fa1-429">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="03fa1-429">Description</span></span>

<span data-ttu-id="03fa1-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-431">'Blazor'</span></span> <span data-ttu-id="03fa1-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-432">'Identity'</span></span>

<span data-ttu-id="03fa1-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-433">'Let's Encrypt'</span></span>

<span data-ttu-id="03fa1-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-434">'Razor'</span></span>

<span data-ttu-id="03fa1-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-435">'SignalR' uid:</span></span>  
<span data-ttu-id="03fa1-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="03fa1-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-437">'Blazor'</span></span> <span data-ttu-id="03fa1-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-438">'Identity'</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="03fa1-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-439">'Let's Encrypt'</span></span>

<span data-ttu-id="03fa1-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-440">'Razor'</span></span> <span data-ttu-id="03fa1-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-441">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-443">'Blazor'</span></span>

<span data-ttu-id="03fa1-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-444">'Identity'</span></span> <span data-ttu-id="03fa1-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-445">'Let's Encrypt'</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="03fa1-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-446">'Razor'</span></span>

<span data-ttu-id="03fa1-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-447">'SignalR' uid:</span></span> <span data-ttu-id="03fa1-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03fa1-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="03fa1-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-449">'Blazor'</span></span> 

<span data-ttu-id="03fa1-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03fa1-450">'Identity'</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="03fa1-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03fa1-451">'Let's Encrypt'</span></span> <span data-ttu-id="03fa1-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03fa1-452">'Razor'</span></span>

<span data-ttu-id="03fa1-453">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03fa1-453">'SignalR' uid:</span></span>

* <span data-ttu-id="03fa1-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte</span><span class="sxs-lookup"><span data-stu-id="03fa1-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span>
* <span data-ttu-id="03fa1-455">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-455">Values are compared using ordinal-ignore-case.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="03fa1-456">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="03fa1-456">Port numbers must be excluded.</span></span> <span data-ttu-id="03fa1-457">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="03fa1-457">If the list is empty, all hosts are allowed.</span></span> <span data-ttu-id="03fa1-458">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="03fa1-458">A top-level wildcard `*` allows all non-empty hosts.</span></span>

<span data-ttu-id="03fa1-459">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="03fa1-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="03fa1-460">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span> <span data-ttu-id="03fa1-461">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="03fa1-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="03fa1-462">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="03fa1-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="03fa1-463">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="03fa1-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span> <span data-ttu-id="03fa1-464">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="03fa1-465">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="03fa1-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="03fa1-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03fa1-467">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="03fa1-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="03fa1-468">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="03fa1-468">The default is `X-Forwarded-For`.</span></span>

::: moniker-end
