---
title: Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich
author: guardrex
description: Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: ab48d80c9cb1c09b5164ed732e76a59687683e97
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034729"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="813ba-103">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="813ba-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="813ba-104">Von [Luke Latham](https://github.com/guardrex) und [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="813ba-104">By [Luke Latham](https://github.com/guardrex) and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="813ba-105">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="813ba-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="813ba-106">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="813ba-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="813ba-107">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="813ba-108">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="813ba-109">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="813ba-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="813ba-110">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="813ba-110">Forwarded headers</span></span>

<span data-ttu-id="813ba-111">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="813ba-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="813ba-112">Header</span><span class="sxs-lookup"><span data-stu-id="813ba-112">Header</span></span> | <span data-ttu-id="813ba-113">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="813ba-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="813ba-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="813ba-114">X-Forwarded-For</span></span> | <span data-ttu-id="813ba-115">Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="813ba-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="813ba-116">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="813ba-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="813ba-117">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="813ba-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="813ba-118">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="813ba-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="813ba-119">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="813ba-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="813ba-120">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="813ba-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="813ba-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="813ba-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="813ba-122">Der Wert des ursprünglichen Schemas (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="813ba-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="813ba-123">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="813ba-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="813ba-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="813ba-124">X-Forwarded-Host</span></span> | <span data-ttu-id="813ba-125">Der ursprüngliche Wert des Felds „Hostheader“.</span><span class="sxs-lookup"><span data-stu-id="813ba-125">The original value of the Host header field.</span></span> <span data-ttu-id="813ba-126">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="813ba-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="813ba-127">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="813ba-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="813ba-128">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="813ba-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="813ba-129">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="813ba-129">The middleware updates:</span></span>

* <span data-ttu-id="813ba-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Auf den Headerwert `X-Forwarded-For` festlegen.</span><span class="sxs-lookup"><span data-stu-id="813ba-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="813ba-131">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="813ba-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="813ba-132">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="813ba-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="813ba-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Auf den Headerwert `X-Forwarded-Proto` festlegen.</span><span class="sxs-lookup"><span data-stu-id="813ba-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="813ba-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Auf den Headerwert `X-Forwarded-Host` festlegen.</span><span class="sxs-lookup"><span data-stu-id="813ba-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="813ba-135">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="813ba-136">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="813ba-136">The default settings are:</span></span>

* <span data-ttu-id="813ba-137">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="813ba-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="813ba-138">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="813ba-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="813ba-139">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="813ba-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="813ba-140">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="813ba-141">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="813ba-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="813ba-142">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="813ba-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="813ba-143">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="813ba-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="813ba-144">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="813ba-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="813ba-145">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="813ba-146">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="813ba-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="813ba-147">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="813ba-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="813ba-148">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="813ba-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="813ba-149">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="813ba-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="813ba-150">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="813ba-151">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="813ba-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="813ba-152">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="813ba-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="813ba-153">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="813ba-154">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="813ba-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="813ba-155">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="813ba-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="813ba-156">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="813ba-157">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="813ba-157">Nginx configuration</span></span>

<span data-ttu-id="813ba-158">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="813ba-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="813ba-159">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="813ba-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="813ba-160">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="813ba-160">Apache configuration</span></span>

<span data-ttu-id="813ba-161">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="813ba-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="813ba-162">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="813ba-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="813ba-163">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="813ba-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="813ba-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="813ba-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="813ba-165">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="813ba-165">The following example changes the default values:</span></span>

* <span data-ttu-id="813ba-166">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="813ba-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="813ba-167">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="813ba-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="813ba-168">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="813ba-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="813ba-169">Option</span><span class="sxs-lookup"><span data-stu-id="813ba-169">Option</span></span> | <span data-ttu-id="813ba-170">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="813ba-170">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="813ba-171">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="813ba-171">AllowedHosts</span></span> | <span data-ttu-id="813ba-172">Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="813ba-172">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="813ba-173">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="813ba-173">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="813ba-174">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-174">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="813ba-175">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="813ba-175">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="813ba-176">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="813ba-176">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="813ba-177">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="813ba-177">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="813ba-178">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="813ba-178">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="813ba-179">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="813ba-179">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="813ba-180">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="813ba-180">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="813ba-181">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="813ba-181">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="813ba-182">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="813ba-182">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="813ba-183">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="813ba-183">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="813ba-184">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-184">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="813ba-185">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-185">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="813ba-186">Die Standardeinstellung ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="813ba-186">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="813ba-187">Gibt an, welche Weiterleitungen verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="813ba-187">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="813ba-188">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="813ba-188">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="813ba-189">Typische Werte, die dieser Eigenschaft zugewiesen werden, sind „ForwardedHeaders.XForwardedFor</span><span class="sxs-lookup"><span data-stu-id="813ba-189">Typical values assigned to this property are \`ForwardedHeaders.XForwardedFor</span></span> | <span data-ttu-id="813ba-190">ForwardedHeaders.XForwardedProto“.</span><span class="sxs-lookup"><span data-stu-id="813ba-190">ForwardedHeaders.XForwardedProto\`.</span></span><br><br><span data-ttu-id="813ba-191">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="813ba-191">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="813ba-192">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-192">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="813ba-193">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-193">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="813ba-194">Die Standardeinstellung ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="813ba-194">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="813ba-195">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="813ba-196">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="813ba-197">Die Standardeinstellung ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="813ba-197">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="813ba-198">Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-198">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="813ba-199">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="813ba-199">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span><br><br><span data-ttu-id="813ba-200">Der Standard ist 1.</span><span class="sxs-lookup"><span data-stu-id="813ba-200">The default is 1.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="813ba-201">Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-201">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="813ba-202">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="813ba-202">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="813ba-203">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="813ba-203">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="813ba-204">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="813ba-204">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="813ba-205">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="813ba-205">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="813ba-206">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="813ba-206">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="813ba-207">Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="813ba-207">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="813ba-208">Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="813ba-208">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="813ba-209">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="813ba-209">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="813ba-210">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="813ba-210">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="813ba-211">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="813ba-211">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="813ba-212">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="813ba-212">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="813ba-213">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="813ba-213">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="813ba-214">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="813ba-214">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="813ba-215">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-215">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="813ba-216">Die Standardeinstellung ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="813ba-216">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="813ba-217">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-217">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="813ba-218">Die Standardeinstellung ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="813ba-218">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="813ba-219">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="813ba-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="813ba-220">Die Standardeinstellung ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="813ba-220">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="813ba-221">Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-221">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="813ba-222">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="813ba-222">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="813ba-223">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="813ba-223">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="813ba-224">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="813ba-224">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="813ba-225">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="813ba-225">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="813ba-226">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-226">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="813ba-227">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="813ba-227">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="813ba-228">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-228">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="813ba-229">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="813ba-229">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="813ba-230">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="813ba-230">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="813ba-231">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="813ba-231">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="813ba-232">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="813ba-232">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="813ba-233">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-233">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="813ba-234">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="813ba-234">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="813ba-235">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="813ba-235">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="813ba-236">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="813ba-236">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="813ba-237">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="813ba-237">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="813ba-238">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="813ba-238">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="813ba-239">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="813ba-239">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="813ba-240">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="813ba-240">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="813ba-241">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="813ba-241">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="813ba-242">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="813ba-242">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="813ba-243">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="813ba-243">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="813ba-244">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="813ba-244">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="813ba-245">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="813ba-245">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="813ba-246">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="813ba-246">Converted prefix length: 104</span></span>

<span data-ttu-id="813ba-247">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="813ba-247">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="813ba-248">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="813ba-248">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="813ba-249">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="813ba-249">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="813ba-250">.NET Core-Vorlagen rufen <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> auf.</span><span class="sxs-lookup"><span data-stu-id="813ba-250">.NET Core templates call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>.</span></span> <span data-ttu-id="813ba-251">Diese Methoden versetzen eine Site in eine Endlosschleife, falls sie in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-251">These methods put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="813ba-252">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="813ba-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="813ba-253">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="813ba-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="813ba-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="813ba-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="813ba-255">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="813ba-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="813ba-256">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="813ba-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

<span data-ttu-id="813ba-257">Bis neuen Container-Images in Azure bereitgestellt werden, müssen Sie eine App-Einstellung (Umgebungsvariable) für `ASPNETCORE_FORWARDEDHEADERS_ENABLED` erstellen, die auf `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="813ba-257">Until new container images are provided in Azure, you must create an app setting (environment variable) for `ASPNETCORE_FORWARDEDHEADERS_ENABLED` set to `true`.</span></span> <span data-ttu-id="813ba-258">Weitere Informationen finden Sie unter [Vorlagen funktionieren nicht in Antares-Linux wegen fehlender Schemaweiterleitungen (Aspnet/AspNetCore #4135)](https://github.com/aspnet/AspNetCore/issues/4135).</span><span class="sxs-lookup"><span data-stu-id="813ba-258">For more information, see [Templates do not work in Antares Linux due to missing scheme forwarders (aspnet/AspNetCore #4135)](https://github.com/aspnet/AspNetCore/issues/4135).</span></span>

::: moniker-end

## <a name="troubleshoot"></a><span data-ttu-id="813ba-259">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="813ba-259">Troubleshoot</span></span>

<span data-ttu-id="813ba-260">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="813ba-260">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="813ba-261">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="813ba-261">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="813ba-262">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="813ba-262">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="813ba-263">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="813ba-263">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="813ba-264">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="813ba-264">You can write to logs instead of the response body.</span></span> <span data-ttu-id="813ba-265">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="813ba-265">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="813ba-266">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="813ba-266">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="813ba-267">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="813ba-267">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="813ba-268">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="813ba-268">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="813ba-269">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="813ba-269">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="813ba-270">Wenn mehrere Werte in einem bestimmten Header vorhanden sind, beachten Sie die Prozessheader der Middleware für weitergeleitete Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="813ba-270">If there are multiple values in a given header, note Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="813ba-271">Der Standardwert von `ForwardLimit` ist 1 (eins), sodass nur der ganz rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="813ba-271">The default `ForwardLimit` is 1 (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="813ba-272">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-272">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="813ba-273">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="813ba-273">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="813ba-274">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="813ba-274">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="813ba-275">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="813ba-275">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="813ba-276">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="813ba-276">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="813ba-277">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="813ba-277">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="813ba-278">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="813ba-278">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="813ba-279">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="813ba-279">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="certificate-forwarding"></a><span data-ttu-id="813ba-280">Zertifikatweiterleitung</span><span class="sxs-lookup"><span data-stu-id="813ba-280">Certificate forwarding</span></span> 

### <a name="on-azure"></a><span data-ttu-id="813ba-281">In Azure</span><span class="sxs-lookup"><span data-stu-id="813ba-281">On Azure</span></span>

<span data-ttu-id="813ba-282">Informationen zum Konfigurieren von Azure-Web-Apps finden Sie in der [Azure-Dokumentation](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="813ba-282">See the [Azure documentation](/azure/app-service/app-service-web-configure-tls-mutual-auth) to configure Azure Web Apps.</span></span> <span data-ttu-id="813ba-283">Fügen Sie in der `Startup.Configure`-Methode Ihrer App den folgenden Code vor dem Aufruf von `app.UseAuthentication();` hinzu:</span><span class="sxs-lookup"><span data-stu-id="813ba-283">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="813ba-284">Sie müssen außerdem die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben, den Azure verwendet.</span><span class="sxs-lookup"><span data-stu-id="813ba-284">You'll also need to configure the Certificate Forwarding middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="813ba-285">Fügen Sie in der `Startup.ConfigureServices`-Methode Ihrer App den folgenden Code hinzu, um den Header zu konfigurieren, aus dem die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="813ba-285">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="with-other-web-proxies"></a><span data-ttu-id="813ba-286">Mit anderen Webproxys</span><span class="sxs-lookup"><span data-stu-id="813ba-286">With other web proxies</span></span>

<span data-ttu-id="813ba-287">Wenn Sie einen Proxy verwenden, der nicht IIS oder Routing von Anwendungsanforderungen von Azure-Web-Apps ist, konfigurieren Sie Ihren Proxy so, dass er das empfangene Zertifikat in einem HTTP-Header weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="813ba-287">If you're using a proxy that isn't IIS or Azure's Web Apps Application Request Routing, configure your proxy to forward the certificate it received in an HTTP header.</span></span> <span data-ttu-id="813ba-288">Fügen Sie in der `Startup.Configure`-Methode Ihrer App den folgenden Code vor dem Aufruf von `app.UseAuthentication();` hinzu:</span><span class="sxs-lookup"><span data-stu-id="813ba-288">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="813ba-289">Sie müssen außerdem die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="813ba-289">You'll also need to configure the Certificate Forwarding middleware to specify the header name.</span></span> <span data-ttu-id="813ba-290">Fügen Sie in der `Startup.ConfigureServices`-Methode Ihrer App den folgenden Code hinzu, um den Header zu konfigurieren, aus dem die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="813ba-290">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="813ba-291">Schließlich, wenn der Proxy etwas anderes macht, als das Zertifikat mit base64 zu codieren (wie bei Nginx), legen Sie die Option `HeaderConverter` fest.</span><span class="sxs-lookup"><span data-stu-id="813ba-291">Finally, if the proxy is doing something other than base64 encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="813ba-292">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="813ba-292">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="813ba-293">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="813ba-293">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="813ba-294">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="813ba-294">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)
