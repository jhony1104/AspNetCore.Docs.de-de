---
title: Unterschiede zwischen signalr und ASP.net Core signalr
author: bradygaster
description: Unterschiede zwischen signalr und ASP.net Core signalr
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746539"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="a13c2-103">Unterschiede zwischen ASP.net signalr und ASP.net Core signalr</span><span class="sxs-lookup"><span data-stu-id="a13c2-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="a13c2-104">ASP.net Core signalr ist nicht mit Clients oder Servern für ASP.net signalr kompatibel.</span><span class="sxs-lookup"><span data-stu-id="a13c2-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="a13c2-105">In diesem Artikel werden die Features erläutert, die in ASP.net Core signalr entfernt oder geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="a13c2-106">Identifizieren der signalr-Version</span><span class="sxs-lookup"><span data-stu-id="a13c2-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="a13c2-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="a13c2-107">ASP.NET SignalR</span></span> | <span data-ttu-id="a13c2-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a13c2-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="a13c2-109">Server-nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="a13c2-109">Server NuGet Package</span></span> | [<span data-ttu-id="a13c2-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="a13c2-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="a13c2-111">[Microsoft. aspnetcore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="a13c2-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="a13c2-112">[Microsoft. aspnetcore. signalr](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="a13c2-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="a13c2-113">Client-nuget-Pakete</span><span class="sxs-lookup"><span data-stu-id="a13c2-113">Client NuGet Packages</span></span> | [<span data-ttu-id="a13c2-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="a13c2-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="a13c2-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="a13c2-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="a13c2-117">NPM-Client Paket</span><span class="sxs-lookup"><span data-stu-id="a13c2-117">Client npm Package</span></span> | [<span data-ttu-id="a13c2-118">signalr</span><span class="sxs-lookup"><span data-stu-id="a13c2-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="a13c2-119">Java-Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-119">Java Client</span></span> | <span data-ttu-id="a13c2-120">[GitHub-Repository](https://github.com/SignalR/java-client) veraltet</span><span class="sxs-lookup"><span data-stu-id="a13c2-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="a13c2-121">Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="a13c2-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="a13c2-122">Server-App-Typ</span><span class="sxs-lookup"><span data-stu-id="a13c2-122">Server App Type</span></span> | <span data-ttu-id="a13c2-123">ASP.net (System. Web) oder owin-Self-Host</span><span class="sxs-lookup"><span data-stu-id="a13c2-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="a13c2-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a13c2-124">ASP.NET Core</span></span> |
| <span data-ttu-id="a13c2-125">Unterstützte Server Plattformen</span><span class="sxs-lookup"><span data-stu-id="a13c2-125">Supported Server Platforms</span></span> | <span data-ttu-id="a13c2-126">.NET Framework 4,5 oder höher</span><span class="sxs-lookup"><span data-stu-id="a13c2-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="a13c2-127">.NET Framework 4.6.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="a13c2-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="a13c2-128">.Net Core 2,1 oder höher</span><span class="sxs-lookup"><span data-stu-id="a13c2-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="a13c2-129">Funktions Unterschiede</span><span class="sxs-lookup"><span data-stu-id="a13c2-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="a13c2-130">Automatische erneute Verbindungen</span><span class="sxs-lookup"><span data-stu-id="a13c2-130">Automatic reconnects</span></span>

<span data-ttu-id="a13c2-131">Automatische erneute Verbindungen werden in ASP.net Core signalr nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a13c2-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="a13c2-132">Wenn der Client getrennt wird, muss der Benutzer explizit eine neue Verbindung starten, wenn er erneut eine Verbindung herstellen möchte.</span><span class="sxs-lookup"><span data-stu-id="a13c2-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="a13c2-133">In ASP.net signalr versucht signalr, erneut eine Verbindung mit dem Server herzustellen, wenn die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="a13c2-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="a13c2-134">Protokoll Unterstützung</span><span class="sxs-lookup"><span data-stu-id="a13c2-134">Protocol support</span></span>

<span data-ttu-id="a13c2-135">ASP.net Core signalr unterstützt JSON und ein neues binäres Protokoll, das auf [messagepack](xref:signalr/messagepackhubprotocol)basiert.</span><span class="sxs-lookup"><span data-stu-id="a13c2-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="a13c2-136">Außerdem können benutzerdefinierte Protokolle erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="a13c2-137">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="a13c2-137">Transports</span></span>

<span data-ttu-id="a13c2-138">Der unveränderte Frame-Transport wird in ASP.net Core signalr nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a13c2-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="a13c2-139">Unterschiede auf dem Server</span><span class="sxs-lookup"><span data-stu-id="a13c2-139">Differences on the server</span></span>

<span data-ttu-id="a13c2-140">Die serverseitigen ASP.net Core signalr-Bibliotheken sind im [metapaketpaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) " enthalten, das Teil der **ASP.net Core Webanwendungs** Vorlage für Razor-und MVC-Projekte ist.</span><span class="sxs-lookup"><span data-stu-id="a13c2-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="a13c2-141">ASP.net Core signalr eine ASP.net Core Middleware ist, muss Sie durch Aufrufen von [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a13c2-142">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) -Methoden `Startup.Configure` Aufrufes in der-Methode zu.</span><span class="sxs-lookup"><span data-stu-id="a13c2-142">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a13c2-143">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [usesignalr](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) -Methoden `Startup.Configure` Aufrufes in der-Methode zu.</span><span class="sxs-lookup"><span data-stu-id="a13c2-143">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="a13c2-144">Persistente Sitzungen</span><span class="sxs-lookup"><span data-stu-id="a13c2-144">Sticky sessions</span></span>

<span data-ttu-id="a13c2-145">Mit dem Modell für horizontales Skalieren für ASP.net signalr können Clients die Verbindung wiederherstellen und Nachrichten an einen beliebigen Server in der Farm senden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-145">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="a13c2-146">In ASP.net Core signalr muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren.</span><span class="sxs-lookup"><span data-stu-id="a13c2-146">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="a13c2-147">Für horizontales Skalieren mit redis bedeutet das, dass persistente Sitzungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a13c2-147">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="a13c2-148">Für horizontales Skalieren mit dem [Azure signalr-Dienst](/azure/azure-signalr/)sind keine persistenten Sitzungen erforderlich, da der Dienst Verbindungen mit Clients verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a13c2-148">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="a13c2-149">Einzelner Hub pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="a13c2-149">Single hub per connection</span></span>

<span data-ttu-id="a13c2-150">In ASP.net Core signalr wurde das Verbindungs Modell vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="a13c2-150">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="a13c2-151">Verbindungen werden direkt mit einem einzelnen Hub hergestellt, anstatt mit einer einzelnen Verbindung, die für die gemeinsame Nutzung des Zugriffs auf mehrere Hubs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a13c2-151">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="a13c2-152">Streaming</span><span class="sxs-lookup"><span data-stu-id="a13c2-152">Streaming</span></span>

<span data-ttu-id="a13c2-153">ASP.net Core signalr unterstützt jetzt das [Streamen von Daten](xref:signalr/streaming) vom Hub an den Client.</span><span class="sxs-lookup"><span data-stu-id="a13c2-153">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="a13c2-154">Status</span><span class="sxs-lookup"><span data-stu-id="a13c2-154">State</span></span>

<span data-ttu-id="a13c2-155">Die Möglichkeit, den beliebigen Zustand zwischen Clients und dem Hub (häufig "hubstate" genannt) zu übergeben, wurde entfernt sowie Unterstützung für Fortschrittsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="a13c2-155">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="a13c2-156">Zurzeit gibt es keine Entsprechung von Hub-Proxys.</span><span class="sxs-lookup"><span data-stu-id="a13c2-156">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="a13c2-157">Entfernen von persistentconnection</span><span class="sxs-lookup"><span data-stu-id="a13c2-157">PersistentConnection removal</span></span>

<span data-ttu-id="a13c2-158">In ASP.net Core signalr wurde die [persistentconnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) -Klasse entfernt.</span><span class="sxs-lookup"><span data-stu-id="a13c2-158">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="a13c2-159">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="a13c2-159">GlobalHost</span></span>

<span data-ttu-id="a13c2-160">ASP.net Core ist eine Abhängigkeitsinjektion (di) in das Framework integriert.</span><span class="sxs-lookup"><span data-stu-id="a13c2-160">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="a13c2-161">Dienste können mit di auf den [hubcontext](xref:signalr/hubcontext)zugreifen.</span><span class="sxs-lookup"><span data-stu-id="a13c2-161">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="a13c2-162">Das `GlobalHost` -Objekt, das in ASP.net signalr verwendet wird, `HubContext` um ein-Objekt zu erhalten, ist nicht in ASP.net Core signalr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-162">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="a13c2-163">Hubpipeline</span><span class="sxs-lookup"><span data-stu-id="a13c2-163">HubPipeline</span></span>

<span data-ttu-id="a13c2-164">ASP.net Core signalr bietet keine Unterstützung `HubPipeline` für Module.</span><span class="sxs-lookup"><span data-stu-id="a13c2-164">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="a13c2-165">Unterschiede auf dem Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-165">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="a13c2-166">TypeScript</span><span class="sxs-lookup"><span data-stu-id="a13c2-166">TypeScript</span></span>

<span data-ttu-id="a13c2-167">Der ASP.net Core signalr-Client wird in [typescript](https://www.typescriptlang.org/)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a13c2-167">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="a13c2-168">Sie können in JavaScript oder typescript schreiben, wenn Sie den [JavaScript-Client](xref:signalr/javascript-client)verwenden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-168">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="a13c2-169">Der JavaScript-Client wird auf [NPM](https://www.npmjs.com/) gehostet.</span><span class="sxs-lookup"><span data-stu-id="a13c2-169">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="a13c2-170">In früheren Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen.</span><span class="sxs-lookup"><span data-stu-id="a13c2-170">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="a13c2-171">Bei den Kern Versionen enthält das [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) NPM-Paket die JavaScript-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="a13c2-171">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="a13c2-172">Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten.</span><span class="sxs-lookup"><span data-stu-id="a13c2-172">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="a13c2-173">Verwenden Sie NPM, um das `@aspnet/signalr` NPM-Paket abzurufen und zu installieren.</span><span class="sxs-lookup"><span data-stu-id="a13c2-173">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="a13c2-174">jQuery</span><span class="sxs-lookup"><span data-stu-id="a13c2-174">jQuery</span></span>

<span data-ttu-id="a13c2-175">Die Abhängigkeit von jQuery wurde entfernt, aber Projekte können weiterhin jQuery verwenden.</span><span class="sxs-lookup"><span data-stu-id="a13c2-175">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="a13c2-176">Internet Explorer-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="a13c2-176">Internet Explorer support</span></span>

<span data-ttu-id="a13c2-177">ASP.net Core signalr erfordert Microsoft Internet Explorer 11 oder höher (ASP.net signalr supported Microsoft Internet Explorer 8 und höher).</span><span class="sxs-lookup"><span data-stu-id="a13c2-177">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="a13c2-178">Syntax der JavaScript-Client Methode</span><span class="sxs-lookup"><span data-stu-id="a13c2-178">JavaScript client method syntax</span></span>

<span data-ttu-id="a13c2-179">Die JavaScript-Syntax wurde von der vorherigen Version von signalr geändert.</span><span class="sxs-lookup"><span data-stu-id="a13c2-179">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="a13c2-180">Anstatt das `$connection` -Objekt zu verwenden, erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) -API eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a13c2-180">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="a13c2-181">Verwenden Sie die [on](/javascript/api/@aspnet/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="a13c2-181">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="a13c2-182">Nachdem Sie die Client Methode erstellt haben, starten Sie die Hub-Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a13c2-182">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="a13c2-183">Verketten einer [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) -Methode zum Protokollieren oder behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="a13c2-183">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="a13c2-184">Knotenproxys</span><span class="sxs-lookup"><span data-stu-id="a13c2-184">Hub proxies</span></span>

<span data-ttu-id="a13c2-185">Hub-Proxys werden nicht mehr automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="a13c2-185">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="a13c2-186">Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/%40aspnet/signalr/hubconnection#invoke) -API übermittelt.</span><span class="sxs-lookup"><span data-stu-id="a13c2-186">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="a13c2-187">.Net und andere Clients</span><span class="sxs-lookup"><span data-stu-id="a13c2-187">.NET and other clients</span></span>

<span data-ttu-id="a13c2-188">Das `Microsoft.AspNetCore.SignalR.Client` nuget-Paket enthält die .NET-Client Bibliotheken für ASP.net Core signalr.</span><span class="sxs-lookup"><span data-stu-id="a13c2-188">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="a13c2-189">Verwenden Sie [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) , um eine Instanz einer Verbindung mit einem Hub zu erstellen und zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a13c2-189">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="a13c2-190">Unterschiede bei horizontaler Skalierung</span><span class="sxs-lookup"><span data-stu-id="a13c2-190">Scaleout differences</span></span>

<span data-ttu-id="a13c2-191">ASP.net signalr unterstützt SQL Server und redis.</span><span class="sxs-lookup"><span data-stu-id="a13c2-191">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="a13c2-192">ASP.net Core signalr unterstützt den Azure signalr-Dienst und redis.</span><span class="sxs-lookup"><span data-stu-id="a13c2-192">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="a13c2-193">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a13c2-193">ASP.NET</span></span>

* [<span data-ttu-id="a13c2-194">Horizontale Skalierung von signalr mit Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="a13c2-194">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="a13c2-195">Horizontale Skalierung von signalr mit redis</span><span class="sxs-lookup"><span data-stu-id="a13c2-195">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="a13c2-196">Horizontale Skalierung von signalr mit SQL Server</span><span class="sxs-lookup"><span data-stu-id="a13c2-196">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="a13c2-197">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a13c2-197">ASP.NET Core</span></span>

* [<span data-ttu-id="a13c2-198">Azure SignalR-Dienst</span><span class="sxs-lookup"><span data-stu-id="a13c2-198">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="a13c2-199">Redis-Backplane</span><span class="sxs-lookup"><span data-stu-id="a13c2-199">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="a13c2-200">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a13c2-200">Additional resources</span></span>

* [<span data-ttu-id="a13c2-201">Hubs</span><span class="sxs-lookup"><span data-stu-id="a13c2-201">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a13c2-202">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-202">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="a13c2-203">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="a13c2-203">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a13c2-204">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="a13c2-204">Supported platforms</span></span>](xref:signalr/supported-platforms)
