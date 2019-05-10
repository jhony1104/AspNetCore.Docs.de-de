---
title: Unterschiede zwischen SignalR und ASP.NET Core SignalR
author: bradygaster
description: Unterschiede zwischen SignalR und ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 091fc44fccf820a394e7c6f775700c85bebc9101
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892027"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="1a379-103">Unterschiede zwischen SignalR für ASP.NET und ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1a379-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="1a379-104">ASP.NET Core SignalR nicht kompatibel mit Clients oder Servern für ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="1a379-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="1a379-105">In diesem Artikel werden die Funktionen, die in ASP.NET Core SignalR geändert oder entfernt wurden.</span><span class="sxs-lookup"><span data-stu-id="1a379-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="1a379-106">So identifizieren Sie die SignalR-version</span><span class="sxs-lookup"><span data-stu-id="1a379-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="1a379-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="1a379-107">ASP.NET SignalR</span></span> | <span data-ttu-id="1a379-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1a379-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="1a379-109">Server-NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="1a379-109">Server NuGet Package</span></span> | [<span data-ttu-id="1a379-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="1a379-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="1a379-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="1a379-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="1a379-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="1a379-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="1a379-113">Die NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="1a379-113">Client NuGet Packages</span></span> | [<span data-ttu-id="1a379-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="1a379-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="1a379-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="1a379-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="1a379-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="1a379-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="1a379-117">Client-Npm-Pakets</span><span class="sxs-lookup"><span data-stu-id="1a379-117">Client npm Package</span></span> | [<span data-ttu-id="1a379-118">signalr</span><span class="sxs-lookup"><span data-stu-id="1a379-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="1a379-119">Java-Client</span><span class="sxs-lookup"><span data-stu-id="1a379-119">Java Client</span></span> | <span data-ttu-id="1a379-120">[GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)</span><span class="sxs-lookup"><span data-stu-id="1a379-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="1a379-121">Maven-Paket [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="1a379-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="1a379-122">Server App-Typ</span><span class="sxs-lookup"><span data-stu-id="1a379-122">Server App Type</span></span> | <span data-ttu-id="1a379-123">ASP.NET ("System.Web") oder Selbstgehostetem</span><span class="sxs-lookup"><span data-stu-id="1a379-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="1a379-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a379-124">ASP.NET Core</span></span> |
| <span data-ttu-id="1a379-125">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="1a379-125">Supported Server Platforms</span></span> | <span data-ttu-id="1a379-126">.NET Framework 4.5 oder höher</span><span class="sxs-lookup"><span data-stu-id="1a379-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="1a379-127">.NET Framework 4.6.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="1a379-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="1a379-128">.NET Core 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="1a379-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="1a379-129">Funktionsunterschiede</span><span class="sxs-lookup"><span data-stu-id="1a379-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="1a379-130">Automatische Verbindungen</span><span class="sxs-lookup"><span data-stu-id="1a379-130">Automatic reconnects</span></span>

<span data-ttu-id="1a379-131">Automatische Verbindungen werden in ASP.NET Core SignalR nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1a379-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="1a379-132">Wenn der Client getrennt wird, muss der Benutzer eine neue Verbindung explizit starten, wenn sie erneut eine Verbindung herstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="1a379-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="1a379-133">SignalR versucht, in ASP.NET SignalR zum Server hergestellt werden soll, wenn die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="1a379-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="1a379-134">Unterstützung des Protokolls</span><span class="sxs-lookup"><span data-stu-id="1a379-134">Protocol support</span></span>

<span data-ttu-id="1a379-135">SignalR für ASP.NET Core unterstützt JSON als auch ein neues binäres Protokoll, die basierend auf [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="1a379-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="1a379-136">Darüber hinaus können benutzerdefinierte Protokolle erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="1a379-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="1a379-137">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="1a379-137">Transports</span></span>

<span data-ttu-id="1a379-138">Der Forever Frame-Transport wird in ASP.NET Core SignalR nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1a379-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="1a379-139">Unterschiede, auf dem server</span><span class="sxs-lookup"><span data-stu-id="1a379-139">Differences on the server</span></span>

<span data-ttu-id="1a379-140">Die ASP.NET Core SignalR serverseitige Bibliotheken befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) Paket, das Teil der **ASP.NET Core-Webanwendung** Vorlage für Razor und MVC Projekte.</span><span class="sxs-lookup"><span data-stu-id="1a379-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="1a379-141">ASP.NET Core SignalR ist eine ASP.NET Core-Middleware, sodass es durch Aufrufen von konfiguriert werden muss [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1a379-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="1a379-142">Zum Konfigurieren des Routings, ordnen Sie Routen für Hubs innerhalb der [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) -Methodenaufruf in der `Startup.Configure` Methode.</span><span class="sxs-lookup"><span data-stu-id="1a379-142">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="1a379-143">Persistente Sitzungen</span><span class="sxs-lookup"><span data-stu-id="1a379-143">Sticky sessions</span></span>

<span data-ttu-id="1a379-144">Das Modell mit horizontaler Skalierung für ASP.NET SignalR kann Clients erneut eine Verbindung herzustellen, und Senden von Nachrichten an einem beliebigen Server in der Farm.</span><span class="sxs-lookup"><span data-stu-id="1a379-144">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="1a379-145">In ASP.NET Core SignalR muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren.</span><span class="sxs-lookup"><span data-stu-id="1a379-145">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="1a379-146">Für die horizontale Skalierung mit Redis bedeutet dies, dass es sich bei persistente Sitzungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="1a379-146">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="1a379-147">Für die Verwendung der Sendewarteschlange für horizontale Skalierung [Azure SignalR Service](/azure/azure-signalr/), persistente Sitzungen sind nicht erforderlich, da der Dienst übernimmt die Verbindungen mit Clients.</span><span class="sxs-lookup"><span data-stu-id="1a379-147">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="1a379-148">Ein Hub pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="1a379-148">Single hub per connection</span></span>

<span data-ttu-id="1a379-149">In ASP.NET Core SignalR wurde das Verbindungsmodell vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="1a379-149">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="1a379-150">Verbindungen werden direkt an eine einzelne Verbindung, die Zugriff auf mehrere Hubs freigeben verwendet wird, anstatt einen einzelnen Hub hergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1a379-150">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="1a379-151">Streaming</span><span class="sxs-lookup"><span data-stu-id="1a379-151">Streaming</span></span>

<span data-ttu-id="1a379-152">Unterstützt nun das ASP.NET Core SignalR [Streamingdaten](xref:signalr/streaming) aus dem Hub an den Client.</span><span class="sxs-lookup"><span data-stu-id="1a379-152">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="1a379-153">Zustand</span><span class="sxs-lookup"><span data-stu-id="1a379-153">State</span></span>

<span data-ttu-id="1a379-154">Die Möglichkeit, die oft ausgegebene Befehlszeilen Zustand zwischen Clients und dem Hub (oft als HubState bezeichnet) übergeben wurde, sowie Unterstützung für Meldungen zum Druckfortschritt aufgehoben.</span><span class="sxs-lookup"><span data-stu-id="1a379-154">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="1a379-155">Es gibt keine Entsprechung der Hub-Proxys im Moment ein.</span><span class="sxs-lookup"><span data-stu-id="1a379-155">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="1a379-156">PersistentConnection entfernen</span><span class="sxs-lookup"><span data-stu-id="1a379-156">PersistentConnection removal</span></span>

<span data-ttu-id="1a379-157">In ASP.NET Core SignalR die [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) Klasse wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="1a379-157">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span> 

### <a name="globalhost"></a><span data-ttu-id="1a379-158">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="1a379-158">GlobalHost</span></span>

<span data-ttu-id="1a379-159">ASP.NET Core verfügt über Abhängigkeitsinjektion (Dependency Injection), die im Framework integriert.</span><span class="sxs-lookup"><span data-stu-id="1a379-159">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="1a379-160">Dienste können DI verwenden, um Zugriff auf die [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="1a379-160">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="1a379-161">Die `GlobalHost` -Objekt, das in ASP.NET SignalR, zum Abrufen verwendet wird einer `HubContext` in ASP.NET Core SignalR nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1a379-161">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="1a379-162">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="1a379-162">HubPipeline</span></span>

<span data-ttu-id="1a379-163">ASP.NET Core SignalR keine Unterstützung für `HubPipeline` Module.</span><span class="sxs-lookup"><span data-stu-id="1a379-163">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="1a379-164">Unterschiede, auf dem client</span><span class="sxs-lookup"><span data-stu-id="1a379-164">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="1a379-165">TypeScript</span><span class="sxs-lookup"><span data-stu-id="1a379-165">TypeScript</span></span>

<span data-ttu-id="1a379-166">Der ASP.NET Core SignalR-Client hat [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="1a379-166">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="1a379-167">Sie können in JavaScript oder TypeScript schreiben, bei Verwendung der [JavaScript-Client](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="1a379-167">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="1a379-168">Der JavaScript-Client befindet sich am [Npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="1a379-168">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="1a379-169">In früheren Versionen wurde der JavaScript-Client über NuGet-Paket in Visual Studio abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a379-169">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="1a379-170">Für die Core-Versionen die [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) Npm-Paket enthält die JavaScript-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="1a379-170">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="1a379-171">Dieses Paket nicht enthalten, der **ASP.NET Core-Webanwendung** Vorlage.</span><span class="sxs-lookup"><span data-stu-id="1a379-171">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="1a379-172">Mithilfe von Npm abrufen und Installieren der `@aspnet/signalr` Npm-Paket.</span><span class="sxs-lookup"><span data-stu-id="1a379-172">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="1a379-173">jQuery</span><span class="sxs-lookup"><span data-stu-id="1a379-173">jQuery</span></span>

<span data-ttu-id="1a379-174">Die Abhängigkeit von jQuery wurde entfernt, aber Projekte jQuery weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="1a379-174">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="1a379-175">Internet Explorer-support</span><span class="sxs-lookup"><span data-stu-id="1a379-175">Internet Explorer support</span></span>

<span data-ttu-id="1a379-176">ASP.NET Core SignalR erfordert Microsoft InternetExplorer 11 oder höher (ASP.NET SignalR unterstützt Microsoft InternetExplorer 8 und höher).</span><span class="sxs-lookup"><span data-stu-id="1a379-176">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="1a379-177">JavaScript-Client-Methodensyntax</span><span class="sxs-lookup"><span data-stu-id="1a379-177">JavaScript client method syntax</span></span>

<span data-ttu-id="1a379-178">Die JavaScript-Syntax wurde aus der vorherigen Version von SignalR geändert.</span><span class="sxs-lookup"><span data-stu-id="1a379-178">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="1a379-179">Anstatt die `$connection` Objekt, erstellen Sie eine Verbindung mit der [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="1a379-179">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="1a379-180">Verwenden der [auf](/javascript/api/@aspnet/signalr/HubConnection#on) Methode, um die Clientmethoden angeben, die der Hub aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="1a379-180">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="1a379-181">Starten Sie nach dem Erstellen der-Methode, die Hub-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="1a379-181">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="1a379-182">Kette eine [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) Methode zum Anmelden oder Fehler zu beheben.</span><span class="sxs-lookup"><span data-stu-id="1a379-182">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="1a379-183">Hub-Proxys</span><span class="sxs-lookup"><span data-stu-id="1a379-183">Hub proxies</span></span>

<span data-ttu-id="1a379-184">Hub-Proxys werden nicht mehr automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="1a379-184">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="1a379-185">Stattdessen wird der Name der Methode übergeben, in der [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API als Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="1a379-185">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="1a379-186">.NET und andere clients</span><span class="sxs-lookup"><span data-stu-id="1a379-186">.NET and other clients</span></span>

<span data-ttu-id="1a379-187">Die `Microsoft.AspNetCore.SignalR.Client` NuGet-Paket enthält die .NET Client-Bibliotheken für ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="1a379-187">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="1a379-188">Verwenden der [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) erstellen und entwickeln eine Instanz einer Verbindung mit einem Hub.</span><span class="sxs-lookup"><span data-stu-id="1a379-188">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="1a379-189">Horizontale Skalierung Unterschiede</span><span class="sxs-lookup"><span data-stu-id="1a379-189">Scaleout differences</span></span>

<span data-ttu-id="1a379-190">ASP.NET SignalR unterstützt SQL Server und Redis.</span><span class="sxs-lookup"><span data-stu-id="1a379-190">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="1a379-191">ASP.NET Core SignalR unterstützt Azure SignalR Service und Redis.</span><span class="sxs-lookup"><span data-stu-id="1a379-191">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="1a379-192">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1a379-192">ASP.NET</span></span>

* [<span data-ttu-id="1a379-193">Horizontale Skalierung in SignalR mit Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="1a379-193">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="1a379-194">Horizontale Skalierung in SignalR mit Redis</span><span class="sxs-lookup"><span data-stu-id="1a379-194">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="1a379-195">Horizontale Skalierung in SignalR mit SQL Server</span><span class="sxs-lookup"><span data-stu-id="1a379-195">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="1a379-196">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a379-196">ASP.NET Core</span></span>

* [<span data-ttu-id="1a379-197">Azure SignalR-Dienst</span><span class="sxs-lookup"><span data-stu-id="1a379-197">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="1a379-198">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1a379-198">Additional resources</span></span>

* [<span data-ttu-id="1a379-199">Hubs</span><span class="sxs-lookup"><span data-stu-id="1a379-199">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1a379-200">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="1a379-200">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="1a379-201">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="1a379-201">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1a379-202">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="1a379-202">Supported platforms</span></span>](xref:signalr/supported-platforms)
