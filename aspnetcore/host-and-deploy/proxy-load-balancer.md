---
title: Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich
author: guardrex
description: Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 9db9ef386b84907f10e0393aca125edc9f32424a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172450"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="5ecd8-103">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="5ecd8-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="5ecd8-104">Von [Luke Latham](https://github.com/guardrex) und [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="5ecd8-104">By [Luke Latham](https://github.com/guardrex) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ecd8-105">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="5ecd8-106">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="5ecd8-107">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="5ecd8-108">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="5ecd8-109">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="5ecd8-110">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="5ecd8-110">Forwarded headers</span></span>

<span data-ttu-id="5ecd8-111">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="5ecd8-112">Header</span><span class="sxs-lookup"><span data-stu-id="5ecd8-112">Header</span></span> | <span data-ttu-id="5ecd8-113">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="5ecd8-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="5ecd8-114">X-Forwarded-For</span></span> | <span data-ttu-id="5ecd8-115">Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="5ecd8-116">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="5ecd8-117">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="5ecd8-118">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="5ecd8-119">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="5ecd8-120">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="5ecd8-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="5ecd8-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="5ecd8-122">Der Wert des ursprünglichen Schemas (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="5ecd8-123">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="5ecd8-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="5ecd8-124">X-Forwarded-Host</span></span> | <span data-ttu-id="5ecd8-125">Der ursprüngliche Wert des Felds „Hostheader“.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-125">The original value of the Host header field.</span></span> <span data-ttu-id="5ecd8-126">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="5ecd8-127">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="5ecd8-128">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="5ecd8-129">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-129">The middleware updates:</span></span>

* <span data-ttu-id="5ecd8-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Auf den Headerwert `X-Forwarded-For` festlegen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="5ecd8-131">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="5ecd8-132">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="5ecd8-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Auf den Headerwert `X-Forwarded-Proto` festlegen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="5ecd8-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Auf den Headerwert `X-Forwarded-Host` festlegen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="5ecd8-135">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="5ecd8-136">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-136">The default settings are:</span></span>

* <span data-ttu-id="5ecd8-137">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="5ecd8-138">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="5ecd8-139">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="5ecd8-140">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="5ecd8-141">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="5ecd8-142">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="5ecd8-143">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="5ecd8-144">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="5ecd8-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="5ecd8-145">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="5ecd8-146">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="5ecd8-147">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="5ecd8-148">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5ecd8-149">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="5ecd8-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5ecd8-150">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="5ecd8-151">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="5ecd8-152">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="5ecd8-153">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ecd8-154">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="5ecd8-155">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5ecd8-156">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="5ecd8-157">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5ecd8-157">Nginx configuration</span></span>

<span data-ttu-id="5ecd8-158">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="5ecd8-159">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="5ecd8-160">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5ecd8-160">Apache configuration</span></span>

<span data-ttu-id="5ecd8-161">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="5ecd8-162">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="5ecd8-163">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="5ecd8-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="5ecd8-165">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-165">The following example changes the default values:</span></span>

* <span data-ttu-id="5ecd8-166">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="5ecd8-167">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="5ecd8-168">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="5ecd8-169">Option</span><span class="sxs-lookup"><span data-stu-id="5ecd8-169">Option</span></span> | <span data-ttu-id="5ecd8-170">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="5ecd8-171">Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="5ecd8-172">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="5ecd8-173">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="5ecd8-174">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="5ecd8-175">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="5ecd8-176">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="5ecd8-177">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="5ecd8-178">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="5ecd8-179">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="5ecd8-180">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="5ecd8-181">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="5ecd8-182">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="5ecd8-183">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="5ecd8-184">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-185">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="5ecd8-186">Gibt an, welche Weiterleitungen verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="5ecd8-187">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="5ecd8-188">Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="5ecd8-189">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="5ecd8-190">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="5ecd8-191">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-192">Der Standardwert ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="5ecd8-193">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="5ecd8-194">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-195">Der Standardwert ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="5ecd8-196">Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="5ecd8-197">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="5ecd8-198">Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="5ecd8-199">Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5ecd8-200">Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="5ecd8-201">Der Standardwert ist `1`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="5ecd8-202">Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="5ecd8-203">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="5ecd8-204">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5ecd8-205">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-206">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5ecd8-207">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5ecd8-208">Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="5ecd8-209">Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="5ecd8-210">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="5ecd8-211">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5ecd8-212">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-213">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5ecd8-214">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5ecd8-215">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="5ecd8-216">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-217">Der Standardwert ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="5ecd8-218">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-219">Der Standardwert ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="5ecd8-220">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-221">Der Standardwert ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="5ecd8-222">Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="5ecd8-223">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="5ecd8-224">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="5ecd8-225">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="5ecd8-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="5ecd8-226">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="5ecd8-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="5ecd8-227">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="5ecd8-228">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="5ecd8-229">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="5ecd8-230">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="5ecd8-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="5ecd8-231">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="5ecd8-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="5ecd8-233">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="5ecd8-234">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="5ecd8-235">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5ecd8-236">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="5ecd8-237">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="5ecd8-238">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="5ecd8-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="5ecd8-239">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="5ecd8-240">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="5ecd8-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="5ecd8-241">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="5ecd8-242">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-243">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="5ecd8-244">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="5ecd8-245">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="5ecd8-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="5ecd8-246">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="5ecd8-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="5ecd8-247">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="5ecd8-247">Converted prefix length: 104</span></span>

<span data-ttu-id="5ecd8-248">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="5ecd8-249">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="5ecd8-250">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="5ecd8-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="5ecd8-251">Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="5ecd8-252">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="5ecd8-253">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="5ecd8-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="5ecd8-255">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="5ecd8-256">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="5ecd8-257">Zertifikatweiterleitung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="5ecd8-258">Azure</span><span class="sxs-lookup"><span data-stu-id="5ecd8-258">Azure</span></span>

<span data-ttu-id="5ecd8-259">Informationen zum Konfigurieren von Azure App Service für die Zertifikatsweiterleitung finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="5ecd8-260">Die folgende Anleitung bezieht sich auf die Konfiguration der ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="5ecd8-261">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="5ecd8-262">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung, um den von Azure verwendeten Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="5ecd8-263">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="5ecd8-264">Andere Webproxys</span><span class="sxs-lookup"><span data-stu-id="5ecd8-264">Other web proxies</span></span>

<span data-ttu-id="5ecd8-265">Wenn ein Proxy verwendet wird, der nicht IIS oder Routing von Anwendungsanforderungen von Azure App Service entspricht, konfigurieren Sie den Proxy so, dass das empfangene Zertifikat in einem HTTP-Header weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="5ecd8-266">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="5ecd8-267">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="5ecd8-268">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="5ecd8-269">Wenn der Proxy das Zertifikat mit nicht mit base64 codiert (wie bei Nginx), legen Sie die Option `HeaderConverter` fest.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="5ecd8-270">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="5ecd8-271">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-271">Troubleshoot</span></span>

<span data-ttu-id="5ecd8-272">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5ecd8-273">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="5ecd8-274">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="5ecd8-275">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="5ecd8-276">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="5ecd8-277">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="5ecd8-278">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="5ecd8-279">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="5ecd8-280">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5ecd8-281">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="5ecd8-282">Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5ecd8-283">Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="5ecd8-284">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="5ecd8-285">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="5ecd8-286">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="5ecd8-287">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="5ecd8-288">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ecd8-289">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="5ecd8-290">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="5ecd8-291">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ecd8-292">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="5ecd8-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="5ecd8-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5ecd8-294">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="5ecd8-295">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="5ecd8-296">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="5ecd8-297">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="5ecd8-298">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="5ecd8-299">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="5ecd8-299">Forwarded headers</span></span>

<span data-ttu-id="5ecd8-300">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="5ecd8-301">Header</span><span class="sxs-lookup"><span data-stu-id="5ecd8-301">Header</span></span> | <span data-ttu-id="5ecd8-302">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="5ecd8-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="5ecd8-303">X-Forwarded-For</span></span> | <span data-ttu-id="5ecd8-304">Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="5ecd8-305">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="5ecd8-306">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="5ecd8-307">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="5ecd8-308">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="5ecd8-309">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="5ecd8-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="5ecd8-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="5ecd8-311">Der Wert des ursprünglichen Schemas (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="5ecd8-312">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="5ecd8-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="5ecd8-313">X-Forwarded-Host</span></span> | <span data-ttu-id="5ecd8-314">Der ursprüngliche Wert des Felds „Hostheader“.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-314">The original value of the Host header field.</span></span> <span data-ttu-id="5ecd8-315">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="5ecd8-316">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="5ecd8-317">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="5ecd8-318">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-318">The middleware updates:</span></span>

* <span data-ttu-id="5ecd8-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Auf den Headerwert `X-Forwarded-For` festlegen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="5ecd8-320">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="5ecd8-321">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="5ecd8-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Auf den Headerwert `X-Forwarded-Proto` festlegen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="5ecd8-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Auf den Headerwert `X-Forwarded-Host` festlegen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="5ecd8-324">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="5ecd8-325">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-325">The default settings are:</span></span>

* <span data-ttu-id="5ecd8-326">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="5ecd8-327">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="5ecd8-328">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="5ecd8-329">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="5ecd8-330">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="5ecd8-331">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="5ecd8-332">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="5ecd8-333">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="5ecd8-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="5ecd8-334">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="5ecd8-335">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="5ecd8-336">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="5ecd8-337">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5ecd8-338">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="5ecd8-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5ecd8-339">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="5ecd8-340">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="5ecd8-341">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="5ecd8-342">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ecd8-343">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="5ecd8-344">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5ecd8-345">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="5ecd8-346">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5ecd8-346">Nginx configuration</span></span>

<span data-ttu-id="5ecd8-347">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="5ecd8-348">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="5ecd8-349">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5ecd8-349">Apache configuration</span></span>

<span data-ttu-id="5ecd8-350">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="5ecd8-351">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="5ecd8-352">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="5ecd8-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="5ecd8-354">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-354">The following example changes the default values:</span></span>

* <span data-ttu-id="5ecd8-355">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="5ecd8-356">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="5ecd8-357">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="5ecd8-358">Option</span><span class="sxs-lookup"><span data-stu-id="5ecd8-358">Option</span></span> | <span data-ttu-id="5ecd8-359">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="5ecd8-360">Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="5ecd8-361">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="5ecd8-362">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="5ecd8-363">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="5ecd8-364">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="5ecd8-365">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="5ecd8-366">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="5ecd8-367">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="5ecd8-368">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="5ecd8-369">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="5ecd8-370">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="5ecd8-371">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="5ecd8-372">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="5ecd8-373">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-374">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="5ecd8-375">Gibt an, welche Weiterleitungen verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="5ecd8-376">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="5ecd8-377">Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="5ecd8-378">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="5ecd8-379">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="5ecd8-380">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-381">Der Standardwert ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="5ecd8-382">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="5ecd8-383">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5ecd8-384">Der Standardwert ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="5ecd8-385">Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="5ecd8-386">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="5ecd8-387">Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="5ecd8-388">Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5ecd8-389">Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="5ecd8-390">Der Standardwert ist `1`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="5ecd8-391">Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="5ecd8-392">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="5ecd8-393">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5ecd8-394">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-395">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5ecd8-396">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5ecd8-397">Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="5ecd8-398">Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="5ecd8-399">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="5ecd8-400">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5ecd8-401">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-402">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5ecd8-403">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5ecd8-404">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="5ecd8-405">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-406">Der Standardwert ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="5ecd8-407">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-408">Der Standardwert ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="5ecd8-409">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="5ecd8-410">Der Standardwert ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="5ecd8-411">Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="5ecd8-412">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="5ecd8-413">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="5ecd8-414">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="5ecd8-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="5ecd8-415">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="5ecd8-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="5ecd8-416">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="5ecd8-417">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="5ecd8-418">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="5ecd8-419">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="5ecd8-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="5ecd8-420">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="5ecd8-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="5ecd8-422">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="5ecd8-423">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="5ecd8-424">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5ecd8-425">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="5ecd8-426">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="5ecd8-427">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="5ecd8-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="5ecd8-428">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="5ecd8-429">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="5ecd8-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="5ecd8-430">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="5ecd8-431">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5ecd8-432">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="5ecd8-433">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="5ecd8-434">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="5ecd8-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="5ecd8-435">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="5ecd8-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="5ecd8-436">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="5ecd8-436">Converted prefix length: 104</span></span>

<span data-ttu-id="5ecd8-437">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="5ecd8-438">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="5ecd8-439">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="5ecd8-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="5ecd8-440">Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="5ecd8-441">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="5ecd8-442">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="5ecd8-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="5ecd8-444">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="5ecd8-445">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="5ecd8-446">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5ecd8-446">Troubleshoot</span></span>

<span data-ttu-id="5ecd8-447">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5ecd8-448">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="5ecd8-449">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="5ecd8-450">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="5ecd8-451">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="5ecd8-452">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="5ecd8-453">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="5ecd8-454">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="5ecd8-455">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5ecd8-456">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="5ecd8-457">Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5ecd8-458">Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="5ecd8-459">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="5ecd8-460">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="5ecd8-461">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="5ecd8-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="5ecd8-462">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="5ecd8-463">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ecd8-464">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="5ecd8-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="5ecd8-465">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="5ecd8-466">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="5ecd8-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ecd8-467">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5ecd8-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="5ecd8-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="5ecd8-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
