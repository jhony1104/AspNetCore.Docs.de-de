---
title: Hosten und Bereitstellen von ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor Server-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 8c06d3a4d0d75a3e2fd9f699af38a23833fa8bce
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419943"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="8ff99-103">Hosten und Bereitstellen von Blazor Server</span><span class="sxs-lookup"><span data-stu-id="8ff99-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="8ff99-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8ff99-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8ff99-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="8ff99-105">Host configuration values</span></span>

<span data-ttu-id="8ff99-106">[Blazor Server-Apps](xref:blazor/hosting-models#blazor-server) können [generische Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="8ff99-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="8ff99-107">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="8ff99-107">Deployment</span></span>

<span data-ttu-id="8ff99-108">Mit dem [Blazor Server-Hostingmodell](xref:blazor/hosting-models#blazor-server) wird Blazor in einer ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8ff99-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8ff99-109">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="8ff99-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8ff99-110">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="8ff99-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8ff99-111">Visual Studio enthält die **Blazor Server-App**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="8ff99-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="8ff99-112">Skalierbarkeit</span><span class="sxs-lookup"><span data-stu-id="8ff99-112">Scalability</span></span>

<span data-ttu-id="8ff99-113">Planen Sie eine Bereitstellung, um die verfügbare Infrastruktur für eine Blazor Server-App optimal zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="8ff99-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="8ff99-114">Sehen Sie sich die folgenden Ressourcen an, um die Skalierbarkeit von Blazor Server-Apps zu behandeln:</span><span class="sxs-lookup"><span data-stu-id="8ff99-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="8ff99-115">[Grundlagen von Blazor Server-Apps](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="8ff99-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="8ff99-116">Bereitstellungsserver</span><span class="sxs-lookup"><span data-stu-id="8ff99-116">Deployment server</span></span>

<span data-ttu-id="8ff99-117">Wenn Sie die Skalierbarkeit eines einzelnen Servers in Erwägung ziehen (zentrales Hochskalieren), ist der für eine App verfügbare Arbeitsspeicher wahrscheinlich die erste Ressource, die die App erschöpft, wenn sich die Benutzeranforderungen erhöhen.</span><span class="sxs-lookup"><span data-stu-id="8ff99-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="8ff99-118">Der verfügbare Arbeitsspeicher auf dem Server wirkt sich auf Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="8ff99-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="8ff99-119">Anzahl der aktiven Verbindungen, die ein Server unterstützen kann.</span><span class="sxs-lookup"><span data-stu-id="8ff99-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="8ff99-120">Benutzeroberflächenlatenz auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="8ff99-120">UI latency on the client.</span></span>

<span data-ttu-id="8ff99-121">Anleitungen zum Erstellen sicherer und skalierbarer Blazor Server-Apps finden Sie unter <xref:security/blazor/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="8ff99-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="8ff99-122">Jede Verbindung verwendet ungefähr 250 KB Arbeitsspeicher für eine minimale App im *Hello World*-Stil.</span><span class="sxs-lookup"><span data-stu-id="8ff99-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="8ff99-123">Die Größe einer Verbindung hängt vom App-Code und den Zustandsverwaltungsanforderungen der einzelnen Komponenten ab.</span><span class="sxs-lookup"><span data-stu-id="8ff99-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="8ff99-124">Sie sollten die Ressourcenanforderungen während der Entwicklung für Ihre App und die Infrastruktur messen, aber die folgende Baseline kann ein Ausgangspunkt zur Planung des Bereitstellungsziels sein: Wenn Sie davon ausgehen, dass Ihre App 5.000 gleichzeitige Benutzer unterstützt, sollten Sie erwägen, mindestens 1,3 GB Serverarbeitsspeicher (oder ~273 KB pro Benutzer) für die App einzukalkulieren.</span><span class="sxs-lookup"><span data-stu-id="8ff99-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="8ff99-125">-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8ff99-125"> configuration</span></span>

Blazor<span data-ttu-id="8ff99-126"> Server-Apps verwenden ASP.NET Core SignalR, um mit dem Browser zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="8ff99-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="8ff99-127">[Die Hosting- und Skalierungsbedingungen von SignalR](xref:signalr/publish-to-azure-web-app) gelten für Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="8ff99-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="8ff99-128"> funktioniert am besten, wenn WebSockets aufgrund geringerer Latenz und wegen Zuverlässigkeit und [Sicherheit](xref:signalr/security) zum SignalR-Transport verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8ff99-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="8ff99-129">SignalR verwendet Long Polling, wenn WebSockets nicht verfügbar oder die App explizit für die Verwendung von Long Polling konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="8ff99-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="8ff99-130">Konfigurieren Sie die App bei Bereitstellung für Azure App Service in den Einstellungen für den Dienst im Azure-Portal für die Verwendung von WebSockets.</span><span class="sxs-lookup"><span data-stu-id="8ff99-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="8ff99-131">Weitere Informationen zum Konfigurieren der App für Azure App Service finden Sie in den [Richtlinien für die SignalR-Veröffentlichung](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="8ff99-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="8ff99-132">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="8ff99-132">Azure SignalR Service</span></span>

<span data-ttu-id="8ff99-133">Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ff99-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="8ff99-134">Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="8ff99-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="8ff99-135">Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren von SignalR Service erheblich zur Verringerung der geografiebedingten Latenz bei.</span><span class="sxs-lookup"><span data-stu-id="8ff99-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="8ff99-136">So konfigurieren Sie eine App (und stellen optional Azure SignalR Service bereit)</span><span class="sxs-lookup"><span data-stu-id="8ff99-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="8ff99-137">Aktivieren Sie die Unterstützung von *persistenten Sitzungen* des Diensts, damit Clients [beim Vorabrendering an denselben Server umgeleitet werden](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="8ff99-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="8ff99-138">Legen Sie die `ServerStickyMode`-Option oder den Konfigurationswert auf `Required` fest.</span><span class="sxs-lookup"><span data-stu-id="8ff99-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="8ff99-139">In der Regel erstellt eine App die Konfiguration mithilfe von **einem** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="8ff99-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="8ff99-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8ff99-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="8ff99-141">Konfiguration (verwenden Sie **einen** der folgenden Ansätze):</span><span class="sxs-lookup"><span data-stu-id="8ff99-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="8ff99-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8ff99-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="8ff99-143">**Konfiguration** > **Anwendungseinstellungen** des App-Diensts im Azure-Portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Wert**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="8ff99-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="8ff99-144">Erstellen Sie ein Veröffentlichungsprofil für Azure-Apps in Visual Studio für die Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="8ff99-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="8ff99-145">Fügen Sie dem Profil die **Azure SignalR Service**-Abhängigkeit hinzu.</span><span class="sxs-lookup"><span data-stu-id="8ff99-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="8ff99-146">Wenn das Azure-Abonnement nicht über eine bereits vorhandene Azure SignalR Service-Instanz verfügt, die der App zugewiesen werden soll, wählen Sie **Neue Azure SignalR Service-Instanz erstellen** aus, um eine neue Dienstinstanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="8ff99-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="8ff99-147">Veröffentlichen Sie die App in Azure.</span><span class="sxs-lookup"><span data-stu-id="8ff99-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="8ff99-148">IIS</span><span class="sxs-lookup"><span data-stu-id="8ff99-148">IIS</span></span>

<span data-ttu-id="8ff99-149">Aktivieren Sie bei Verwendung von IIS Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8ff99-149">When using IIS, enable:</span></span>

* <span data-ttu-id="8ff99-150">[WebSockets in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="8ff99-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="8ff99-151">[Persistente Sitzungen mit Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="8ff99-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="8ff99-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="8ff99-152">Kubernetes</span></span>

<span data-ttu-id="8ff99-153">Erstellen Sie eine Eingangsdefinition mithilfe der folgenden [Kubernetes-Anmerkungen für persistente Sitzungen:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)</span><span class="sxs-lookup"><span data-stu-id="8ff99-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="8ff99-154">Linux mit Nginx</span><span class="sxs-lookup"><span data-stu-id="8ff99-154">Linux with Nginx</span></span>

<span data-ttu-id="8ff99-155">Damit SignalR WebSockets ordnungsgemäß funktioniert, sollten Sie sich vergewissern, dass die Header `Upgrade` und `Connection` des Proxys auf die folgenden Werte festgelegt sind und dass `$connection_upgrade` einem der folgenden Werte zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="8ff99-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="8ff99-156">Der Standardwert für den Upgradeheader</span><span class="sxs-lookup"><span data-stu-id="8ff99-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="8ff99-157">`close`, wenn der Upgradeheader fehlt oder leer ist</span><span class="sxs-lookup"><span data-stu-id="8ff99-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="8ff99-158">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="8ff99-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="8ff99-159">NGINX als WebSocket-Proxy</span><span class="sxs-lookup"><span data-stu-id="8ff99-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="8ff99-160">WebSocket-Proxyfunktion</span><span class="sxs-lookup"><span data-stu-id="8ff99-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="8ff99-161">Linux mit Apache</span><span class="sxs-lookup"><span data-stu-id="8ff99-161">Linux with Apache</span></span>

<span data-ttu-id="8ff99-162">Konfigurieren Sie `ProxyPass` für den HTTP- und WebSockets-Datenverkehr, um eine Blazor-App hinter Apache unter Linux zu hosten.</span><span class="sxs-lookup"><span data-stu-id="8ff99-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="8ff99-163">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8ff99-163">In the following example:</span></span>

* <span data-ttu-id="8ff99-164">Der Kestrel-Server wird auf dem Hostcomputer ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8ff99-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="8ff99-165">Die App lauscht an Port 5000 auf Datenverkehr.</span><span class="sxs-lookup"><span data-stu-id="8ff99-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="8ff99-166">Aktivieren Sie die folgenden Module:</span><span class="sxs-lookup"><span data-stu-id="8ff99-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="8ff99-167">Überprüfen Sie die Browserkonsole auf WebSockets-Fehler.</span><span class="sxs-lookup"><span data-stu-id="8ff99-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="8ff99-168">Fehlerbeispiele:</span><span class="sxs-lookup"><span data-stu-id="8ff99-168">Example errors:</span></span>

* <span data-ttu-id="8ff99-169">Firefox kann unter „ws://the-domain-name.tld/_blazor?id=XXX“ keine Verbindung mit dem Server herstellen.</span><span class="sxs-lookup"><span data-stu-id="8ff99-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="8ff99-170">Fehler: "Failed to start the transport 'WebSockets'" (Beim Starten des Transports „Websockets“ ist ein Fehler aufgetreten): Fehler: "There was an error with the transport." (Beim Transport ist ein Fehler aufgetreten.)</span><span class="sxs-lookup"><span data-stu-id="8ff99-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="8ff99-171">Fehler: "Failed to start the transport 'LongPolling'" (Beim Starten des Transports „LongPolling“ ist ein Fehler aufgetreten): "TypeError: this.transport is undefined" (TypeError: Dieser Transport ist nicht definiert)</span><span class="sxs-lookup"><span data-stu-id="8ff99-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="8ff99-172">Fehler: "Unable to connect to the server with any of the available transports." (Es kann mit keinem der verfügbaren Transporte eine Verbindung mit dem Server hergestellt werden.)</span><span class="sxs-lookup"><span data-stu-id="8ff99-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="8ff99-173">"WebSockets failed" (WebSockets-Fehler)</span><span class="sxs-lookup"><span data-stu-id="8ff99-173">WebSockets failed</span></span>
* <span data-ttu-id="8ff99-174">Fehler: "Cannot send data if the connection is not in the 'Connected' State." (Es können keine Daten gesendet werden, wenn die Verbindung nicht den Zustand „Verbunden“ aufweist.)</span><span class="sxs-lookup"><span data-stu-id="8ff99-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="8ff99-175">Weitere Informationen finden Sie in der [Apache-Dokumentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="8ff99-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="8ff99-176">Messen der Netzwerklatenz</span><span class="sxs-lookup"><span data-stu-id="8ff99-176">Measure network latency</span></span>

<span data-ttu-id="8ff99-177">Mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) kann die Netzwerklatenz gemessen werden, wie im folgenden Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="8ff99-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="8ff99-178">Für eine sinnvolle Benutzeroberfläche empfehlen wir eine dauerhafte Latenz von höchstens 250 ms.</span><span class="sxs-lookup"><span data-stu-id="8ff99-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
