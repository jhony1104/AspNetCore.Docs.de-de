---
title: Unterschiede zwischen SignalR und ASP.net Core SignalR
author: bradygaster
description: Unterschiede zwischen SignalR und ASP.net Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963721"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="72757-103">Unterschiede zwischen ASP.net-SignalR und ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="72757-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="72757-104">ASP.net Core SignalR ist für ASP.net-SignalRnicht mit Clients oder Servern kompatibel.</span><span class="sxs-lookup"><span data-stu-id="72757-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="72757-105">In diesem Artikel werden die Features erläutert, die in ASP.net Core SignalRentfernt oder geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="72757-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="72757-106">Identifizieren der SignalR Version</span><span class="sxs-lookup"><span data-stu-id="72757-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="72757-107">ASP.net SignalR</span><span class="sxs-lookup"><span data-stu-id="72757-107">ASP.NET SignalR</span></span> | <span data-ttu-id="72757-108">ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="72757-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="72757-109">Server-nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="72757-109">Server NuGet Package</span></span> | <span data-ttu-id="72757-110">[Microsoft. Aspnet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="72757-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="72757-111">[Microsoft. aspnetcore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="72757-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="72757-112">[Microsoft. aspnetcore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="72757-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="72757-113">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="72757-113">(.NET Framework)</span></span> |
| <span data-ttu-id="72757-114">Client-nuget-Pakete</span><span class="sxs-lookup"><span data-stu-id="72757-114">Client NuGet Packages</span></span> | <span data-ttu-id="72757-115">[Microsoft. Aspnet.SignalR. Ent](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="72757-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="72757-116">[Microsoft. Aspnet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="72757-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="72757-117">[Microsoft. aspnetcore.SignalR. Ent](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="72757-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="72757-118">NPM-Client Paket</span><span class="sxs-lookup"><span data-stu-id="72757-118">Client npm Package</span></span> | [<span data-ttu-id="72757-119">signalr</span><span class="sxs-lookup"><span data-stu-id="72757-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="72757-120">Java-Client</span><span class="sxs-lookup"><span data-stu-id="72757-120">Java Client</span></span> | <span data-ttu-id="72757-121">[GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)</span><span class="sxs-lookup"><span data-stu-id="72757-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="72757-122">Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="72757-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="72757-123">Server-App-Typ</span><span class="sxs-lookup"><span data-stu-id="72757-123">Server App Type</span></span> | <span data-ttu-id="72757-124">ASP.net (System. Web) oder owin-Self-Host</span><span class="sxs-lookup"><span data-stu-id="72757-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="72757-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72757-125">ASP.NET Core</span></span> |
| <span data-ttu-id="72757-126">Unterstützte Server Plattformen</span><span class="sxs-lookup"><span data-stu-id="72757-126">Supported Server Platforms</span></span> | <span data-ttu-id="72757-127">.NET Framework 4,5 oder höher</span><span class="sxs-lookup"><span data-stu-id="72757-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="72757-128">.NET Framework 4.6.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="72757-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="72757-129">.Net Core 2,1 oder höher</span><span class="sxs-lookup"><span data-stu-id="72757-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="72757-130">Funktions Unterschiede</span><span class="sxs-lookup"><span data-stu-id="72757-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="72757-131">Automatische erneute Verbindungen</span><span class="sxs-lookup"><span data-stu-id="72757-131">Automatic reconnects</span></span>

<span data-ttu-id="72757-132">Automatische erneute Verbindungen werden in ASP.net Core SignalRnicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="72757-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="72757-133">Wenn der Client getrennt wird, muss der Benutzer explizit eine neue Verbindung starten, wenn er erneut eine Verbindung herstellen möchte.</span><span class="sxs-lookup"><span data-stu-id="72757-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="72757-134">In ASP.net SignalRversucht SignalR, erneut eine Verbindung mit dem Server herzustellen, wenn die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="72757-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="72757-135">Protokoll Unterstützung</span><span class="sxs-lookup"><span data-stu-id="72757-135">Protocol support</span></span>

<span data-ttu-id="72757-136">ASP.net Core SignalR unterstützt JSON und ein neues binäres Protokoll, das auf [messagepack](xref:signalr/messagepackhubprotocol)basiert.</span><span class="sxs-lookup"><span data-stu-id="72757-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="72757-137">Außerdem können benutzerdefinierte Protokolle erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72757-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="72757-138">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="72757-138">Transports</span></span>

<span data-ttu-id="72757-139">Der unveränderte Frame-Transport wird in ASP.net Core SignalRnicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="72757-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="72757-140">Unterschiede auf dem Server</span><span class="sxs-lookup"><span data-stu-id="72757-140">Differences on the server</span></span>

<span data-ttu-id="72757-141">Die ASP.net Core SignalR serverseitigen Bibliotheken sind im [metapaketpaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) " enthalten, das Teil der **ASP.net Core Webanwendungs** Vorlage für Razor-und MVC-Projekte ist.</span><span class="sxs-lookup"><span data-stu-id="72757-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="72757-142">ASP.net Core SignalR eine ASP.net Core Middleware ist, muss Sie durch Aufrufen von [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="72757-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72757-143">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) -Methoden Aufrufes in der `Startup.Configure`-Methode zu.</span><span class="sxs-lookup"><span data-stu-id="72757-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="72757-144">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [usesignalr](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) -Methoden Aufrufes in der `Startup.Configure`-Methode zu.</span><span class="sxs-lookup"><span data-stu-id="72757-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="72757-145">Persistente Sitzungen</span><span class="sxs-lookup"><span data-stu-id="72757-145">Sticky sessions</span></span>

<span data-ttu-id="72757-146">Das horizontale Skalierungs Modell für ASP.net-SignalR ermöglicht Clients das erneute Verbinden und Senden von Nachrichten an einen beliebigen Server in der Farm.</span><span class="sxs-lookup"><span data-stu-id="72757-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="72757-147">In ASP.net Core SignalRmuss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren.</span><span class="sxs-lookup"><span data-stu-id="72757-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="72757-148">Für horizontales Skalieren mit redis bedeutet das, dass persistente Sitzungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="72757-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="72757-149">Für horizontales Skalieren mit [Azure SignalR Service](/azure/azure-signalr/)sind keine persistenten Sitzungen erforderlich, da der Dienst Verbindungen mit Clients verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="72757-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="72757-150">Einzelner Hub pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="72757-150">Single hub per connection</span></span>

<span data-ttu-id="72757-151">In ASP.net Core SignalRwurde das Verbindungs Modell vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="72757-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="72757-152">Verbindungen werden direkt mit einem einzelnen Hub hergestellt, anstatt mit einer einzelnen Verbindung, die für die gemeinsame Nutzung des Zugriffs auf mehrere Hubs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="72757-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="72757-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="72757-153">Streaming</span></span>

<span data-ttu-id="72757-154">ASP.net Core SignalR unterstützt jetzt das [Streaming von Daten](xref:signalr/streaming) vom Hub zum Client.</span><span class="sxs-lookup"><span data-stu-id="72757-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="72757-155">Zustand</span><span class="sxs-lookup"><span data-stu-id="72757-155">State</span></span>

<span data-ttu-id="72757-156">Die Möglichkeit, den beliebigen Zustand zwischen Clients und dem Hub (häufig "hubstate" genannt) zu übergeben, wurde entfernt sowie Unterstützung für Fortschrittsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="72757-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="72757-157">Zurzeit gibt es keine Entsprechung von Hub-Proxys.</span><span class="sxs-lookup"><span data-stu-id="72757-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="72757-158">Entfernen von persistentconnection</span><span class="sxs-lookup"><span data-stu-id="72757-158">PersistentConnection removal</span></span>

<span data-ttu-id="72757-159">In ASP.net Core SignalRwurde die [persistentconnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) -Klasse entfernt.</span><span class="sxs-lookup"><span data-stu-id="72757-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="72757-160">Global Host</span><span class="sxs-lookup"><span data-stu-id="72757-160">GlobalHost</span></span>

<span data-ttu-id="72757-161">ASP.net Core ist eine Abhängigkeitsinjektion (di) in das Framework integriert.</span><span class="sxs-lookup"><span data-stu-id="72757-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="72757-162">Dienste können mit di auf den [hubcontext](xref:signalr/hubcontext)zugreifen.</span><span class="sxs-lookup"><span data-stu-id="72757-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="72757-163">Das `GlobalHost` Objekt, das in ASP.net SignalR verwendet wird, um eine `HubContext` zu erhalten, ist in ASP.net Core SignalRnicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="72757-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="72757-164">Hubpipeline</span><span class="sxs-lookup"><span data-stu-id="72757-164">HubPipeline</span></span>

<span data-ttu-id="72757-165">ASP.net Core SignalR unterstützt nicht `HubPipeline` Modulen.</span><span class="sxs-lookup"><span data-stu-id="72757-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="72757-166">Unterschiede auf dem Client</span><span class="sxs-lookup"><span data-stu-id="72757-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="72757-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="72757-167">TypeScript</span></span>

<span data-ttu-id="72757-168">Der ASP.net Core SignalR-Client wird in [typescript](https://www.typescriptlang.org/)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="72757-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="72757-169">Sie können in JavaScript oder typescript schreiben, wenn Sie den [JavaScript-Client](xref:signalr/javascript-client)verwenden.</span><span class="sxs-lookup"><span data-stu-id="72757-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="72757-170">Der JavaScript-Client wird auf [NPM](https://www.npmjs.com/) gehostet.</span><span class="sxs-lookup"><span data-stu-id="72757-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="72757-171">In früheren Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen.</span><span class="sxs-lookup"><span data-stu-id="72757-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="72757-172">Bei den Kern Versionen enthält das [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) NPM-Paket die JavaScript-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="72757-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="72757-173">Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten.</span><span class="sxs-lookup"><span data-stu-id="72757-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="72757-174">Verwenden Sie NPM, um das `@aspnet/signalr` NPM-Paket abzurufen und zu installieren.</span><span class="sxs-lookup"><span data-stu-id="72757-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="72757-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="72757-175">jQuery</span></span>

<span data-ttu-id="72757-176">Die Abhängigkeit von jQuery wurde entfernt, aber Projekte können weiterhin jQuery verwenden.</span><span class="sxs-lookup"><span data-stu-id="72757-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="72757-177">Internet Explorer-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="72757-177">Internet Explorer support</span></span>

<span data-ttu-id="72757-178">ASP.net Core SignalR erfordert Microsoft Internet Explorer 11 oder höher (ASP.net SignalR unterstützt Microsoft Internet Explorer 8 und höher).</span><span class="sxs-lookup"><span data-stu-id="72757-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="72757-179">Syntax der JavaScript-Client Methode</span><span class="sxs-lookup"><span data-stu-id="72757-179">JavaScript client method syntax</span></span>

<span data-ttu-id="72757-180">Die JavaScript-Syntax wurde von der vorherigen Version von SignalRgeändert.</span><span class="sxs-lookup"><span data-stu-id="72757-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="72757-181">Anstatt das `$connection`-Objekt zu verwenden, erstellen Sie eine Verbindung mithilfe der [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) -API.</span><span class="sxs-lookup"><span data-stu-id="72757-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="72757-182">Verwenden Sie die [on](/javascript/api/@aspnet/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="72757-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="72757-183">Nachdem Sie die Client Methode erstellt haben, starten Sie die Hub-Verbindung.</span><span class="sxs-lookup"><span data-stu-id="72757-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="72757-184">Verketten einer [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) -Methode zum Protokollieren oder behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="72757-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="72757-185">Knotenproxys</span><span class="sxs-lookup"><span data-stu-id="72757-185">Hub proxies</span></span>

<span data-ttu-id="72757-186">Hub-Proxys werden nicht mehr automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="72757-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="72757-187">Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/%40aspnet/signalr/hubconnection#invoke) -API übermittelt.</span><span class="sxs-lookup"><span data-stu-id="72757-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="72757-188">.Net und andere Clients</span><span class="sxs-lookup"><span data-stu-id="72757-188">.NET and other clients</span></span>

<span data-ttu-id="72757-189">Das `Microsoft.AspNetCore.SignalR.Client` nuget-Paket enthält die .NET-Client Bibliotheken für ASP.net Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="72757-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="72757-190">Verwenden Sie [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) , um eine Instanz einer Verbindung mit einem Hub zu erstellen und zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="72757-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="72757-191">Unterschiede bei horizontaler Skalierung</span><span class="sxs-lookup"><span data-stu-id="72757-191">Scaleout differences</span></span>

<span data-ttu-id="72757-192">ASP.net SignalR unterstützt SQL Server und redis.</span><span class="sxs-lookup"><span data-stu-id="72757-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="72757-193">ASP.net Core SignalR unterstützt Azure SignalR Service und redis.</span><span class="sxs-lookup"><span data-stu-id="72757-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="72757-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="72757-194">ASP.NET</span></span>

* <span data-ttu-id="72757-195">[SignalR horizontales hochskalieren mit Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="72757-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="72757-196">[horizontales Skalieren mit redis SignalR](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="72757-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="72757-197">[SignalR horizontales hochskalieren mit SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="72757-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="72757-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72757-198">ASP.NET Core</span></span>

* <span data-ttu-id="72757-199">[Azure SignalR-Dienst](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="72757-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="72757-200">Redis-Backplane</span><span class="sxs-lookup"><span data-stu-id="72757-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="72757-201">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="72757-201">Additional resources</span></span>

* [<span data-ttu-id="72757-202">Hubs</span><span class="sxs-lookup"><span data-stu-id="72757-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="72757-203">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="72757-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="72757-204">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="72757-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="72757-205">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="72757-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
