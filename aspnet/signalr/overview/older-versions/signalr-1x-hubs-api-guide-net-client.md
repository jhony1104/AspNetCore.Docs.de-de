---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-net-client
title: Leitfaden für ASP.NET SignalR Hubs-API – .NET-Client (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für den SignalR .NET-Client zu erhalten, wie z. B. Windows Store (WinRT), WPF, Silverlight und Nachteile Version 2...
ms.author: bradyg
ms.date: 04/17/2013
ms.assetid: c334adc3-d6dc-44f3-9f06-f7634475aad3
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: dbf62b2f9851e3612885aa5375cd2c3432570643
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836196"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-signalr-1x"></a><span data-ttu-id="94fd5-103">Leitfaden für ASP.NET SignalR Hubs-API – .NET-Client (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="94fd5-103">ASP.NET SignalR Hubs API Guide - .NET Client (SignalR 1.x)</span></span>
====================
<span data-ttu-id="94fd5-104">durch [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="94fd5-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="94fd5-105">Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für den SignalR .NET-Client zu erhalten, wie z. B. Windows Store (WinRT), WPF, Silverlight und konsolenanwendungen Version 2.</span><span class="sxs-lookup"><span data-stu-id="94fd5-105">This document provides an introduction to using the Hubs API for SignalR version 2 in .NET clients, such as Windows Store (WinRT), WPF, Silverlight, and console applications.</span></span>
> 
> <span data-ttu-id="94fd5-106">Der SignalR-Hubs-API können Sie die Remoteprozeduraufrufe (RPCs) von einem Server verbundene Clients und von den Clients an den Server vornehmen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="94fd5-107">Im Server-Code Sie Methoden definieren, die von Clients aufgerufen werden können, und rufen Sie Methoden, die auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="94fd5-108">Im Clientcode Sie Methoden definieren, die vom Server aufgerufen werden können, und rufen Sie Methoden, die auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="94fd5-109">SignalR ist für alle Client-zu-Server sich für Sie übernimmt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
> 
> <span data-ttu-id="94fd5-110">SignalR bietet außerdem eine Low-Level-API wird aufgerufen, dauerhafte Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="94fd5-111">Eine Einführung in die SignalR-Hubs und dauerhafte Verbindungen oder für ein Lernprogramm, das zeigt, wie Sie eine vollständige SignalR-Anwendung erstellen, finden Sie unter [SignalR - erste Schritte](../getting-started/index.md).</span><span class="sxs-lookup"><span data-stu-id="94fd5-111">For an introduction to SignalR, Hubs, and Persistent Connections, or for a tutorial that shows how to build a complete SignalR application, see [SignalR - Getting Started](../getting-started/index.md).</span></span>


## <a name="overview"></a><span data-ttu-id="94fd5-112">Übersicht</span><span class="sxs-lookup"><span data-stu-id="94fd5-112">Overview</span></span>

<span data-ttu-id="94fd5-113">Dieses Dokument enthält folgende Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="94fd5-113">This document contains the following sections:</span></span>

- [<span data-ttu-id="94fd5-114">Client-Setup</span><span class="sxs-lookup"><span data-stu-id="94fd5-114">Client Setup</span></span>](#clientsetup)
- [<span data-ttu-id="94fd5-115">Gewusst wie: Herstellen einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-115">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="94fd5-116">Domänenübergreifende Verbindungen von Silverlight-clients</span><span class="sxs-lookup"><span data-stu-id="94fd5-116">Cross-domain connections from Silverlight clients</span></span>](#slcrossdomain)
- [<span data-ttu-id="94fd5-117">Gewusst wie: Konfigurieren der Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-117">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="94fd5-118">Die maximale Anzahl gleichzeitiger Verbindungen in WPF-Clients festlegen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-118">How to set the maximum number of concurrent connections in WPF clients</span></span>](#maxconnections)
    - [<span data-ttu-id="94fd5-119">Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter</span><span class="sxs-lookup"><span data-stu-id="94fd5-119">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="94fd5-120">Das Angeben der Transportmethode</span><span class="sxs-lookup"><span data-stu-id="94fd5-120">How to specify the transport method</span></span>](#transport)
    - [<span data-ttu-id="94fd5-121">Gewusst wie: Angeben von HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="94fd5-121">How to specify HTTP headers</span></span>](#httpheaders)
    - [<span data-ttu-id="94fd5-122">Gewusst wie: Geben Sie Clientzertifikate</span><span class="sxs-lookup"><span data-stu-id="94fd5-122">How to specify client certificates</span></span>](#clientcertificate)
- [<span data-ttu-id="94fd5-123">Vorgehensweise: Erstellen Sie die Hubproxy-Klasse</span><span class="sxs-lookup"><span data-stu-id="94fd5-123">How to create the Hub proxy</span></span>](#proxy)
- [<span data-ttu-id="94fd5-124">Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="94fd5-124">How to define methods on the client that the server can call</span></span>](#callclient)

    - [<span data-ttu-id="94fd5-125">Methoden ohne Parameter</span><span class="sxs-lookup"><span data-stu-id="94fd5-125">Methods without parameters</span></span>](#clientmethodswithoutparms)
    - [<span data-ttu-id="94fd5-126">Methoden mit Parametern, Parametertypen angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-126">Methods with parameters, specifying parameter types</span></span>](#clientmethodswithparmtypes)
    - [<span data-ttu-id="94fd5-127">Methoden mit Parametern, dynamische Objekte für die Parameter angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-127">Methods with parameters, specifying dynamic objects for the parameters</span></span>](#clientmethodswithdynamparms)
    - [<span data-ttu-id="94fd5-128">Vorgehensweise beim Entfernen eines Handlers</span><span class="sxs-lookup"><span data-stu-id="94fd5-128">How to remove a handler</span></span>](#removehandler)
- [<span data-ttu-id="94fd5-129">Gewusst wie: Servermethoden vom Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-129">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="94fd5-130">Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-130">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="94fd5-131">Gewusst wie: Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="94fd5-131">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="94fd5-132">Gewusst wie: Aktivieren der clientseitigen Protokollierung</span><span class="sxs-lookup"><span data-stu-id="94fd5-132">How to enable client-side logging</span></span>](#logging)
- [<span data-ttu-id="94fd5-133">WPF, Silverlight und Konsolenanwendung Codebeispiele für Clientmethoden, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="94fd5-133">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>](#wpfsl)

<span data-ttu-id="94fd5-134">Ein .NET Client Beispielprojekte finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="94fd5-134">For a sample .NET client projects, see the following resources:</span></span>

- <span data-ttu-id="94fd5-135">[Gustavo-Armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) auf "github.com" (app-Beispiele in WinRT, Silverlight-Konsole).</span><span class="sxs-lookup"><span data-stu-id="94fd5-135">[gustavo-armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) on GitHub.com (WinRT, Silverlight, console app examples).</span></span>
- <span data-ttu-id="94fd5-136">[DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) auf GitHub.com (WPF-Beispiel).</span><span class="sxs-lookup"><span data-stu-id="94fd5-136">[DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) on GitHub.com (WPF example).</span></span>
- <span data-ttu-id="94fd5-137">[SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) auf "github.com" (Console app-Beispiel).</span><span class="sxs-lookup"><span data-stu-id="94fd5-137">[SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) on GitHub.com (Console app example).</span></span>

<span data-ttu-id="94fd5-138">Dokumentation zum Programmieren des Servers oder Clients mit JavaScript finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="94fd5-138">For documentation on how to program the server or JavaScript clients, see the following resources:</span></span>

- [<span data-ttu-id="94fd5-139">SignalR-Hubs-API-Guide - Server</span><span class="sxs-lookup"><span data-stu-id="94fd5-139">SignalR Hubs API Guide - Server</span></span>](../guide-to-the-api/hubs-api-guide-server.md)
- [<span data-ttu-id="94fd5-140">SignalR-Hubs-API-Leitfaden – JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="94fd5-140">SignalR Hubs API Guide - JavaScript Client</span></span>](../guide-to-the-api/hubs-api-guide-javascript-client.md)

<span data-ttu-id="94fd5-141">Links zu Themen,-API-Referenz sind, .NET 4.5-Version der API.</span><span class="sxs-lookup"><span data-stu-id="94fd5-141">Links to API Reference topics are to the .NET 4.5 version of the API.</span></span> <span data-ttu-id="94fd5-142">Wenn Sie .NET 4 verwenden, finden Sie unter [.NET 4-Version der API-Themen](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span><span class="sxs-lookup"><span data-stu-id="94fd5-142">If you're using .NET 4, see [the .NET 4 version of the API topics](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="94fd5-143">Client-setup</span><span class="sxs-lookup"><span data-stu-id="94fd5-143">Client setup</span></span>

<span data-ttu-id="94fd5-144">Installieren Sie die [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet-Paket (nicht die [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) Paket).</span><span class="sxs-lookup"><span data-stu-id="94fd5-144">Install the [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet package (not the [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) package).</span></span> <span data-ttu-id="94fd5-145">Dieses Paket unterstützt WinRT, Silverlight, WPF, Konsolenanwendung und Windows Phone-Clients, die für .NET 4 und .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="94fd5-145">This package supports WinRT, Silverlight, WPF, console application, and Windows Phone clients, for both .NET 4 and .NET 4.5.</span></span>

<span data-ttu-id="94fd5-146">Ist die Version von SignalR, die Sie auf dem Client haben sich von der Version, die Sie auf dem Server haben, ist SignalR häufig auf den Unterschied anpassen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-146">If the version of SignalR that you have on the client is different from the version that you have on the server, SignalR is often able to adapt to the difference.</span></span> <span data-ttu-id="94fd5-147">Beispielsweise wird bei der SignalR-Version 2.0 wird freigegeben, und Sie installieren, auf dem Server, der Server Clients unterstützt, die sind 1.1.x sowie Clients, auf denen 2.0 installiert ist.</span><span class="sxs-lookup"><span data-stu-id="94fd5-147">For example, when SignalR version 2.0 is released and you install that on the server, the server will support clients that have 1.1.x installed as well as clients that have 2.0 installed.</span></span> <span data-ttu-id="94fd5-148">Der Unterschied zwischen der Version auf dem Server und die Version auf dem Client zu groß ist, löst SignalR eine `InvalidOperationException` Ausnahme aus, wenn der Client versucht, eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-148">If the difference between the version on the server and the version on the client is too great, SignalR throws an `InvalidOperationException` exception when the client tries to establish a connection.</span></span> <span data-ttu-id="94fd5-149">Die Fehlermeldung lautet "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".</span><span class="sxs-lookup"><span data-stu-id="94fd5-149">The error message is "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="94fd5-150">Gewusst wie: Herstellen einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-150">How to establish a connection</span></span>

<span data-ttu-id="94fd5-151">Bevor Sie eine Verbindung herstellen können, müssen Sie erstellen eine `HubConnection` Objekt, und erstellen Sie einen Proxy.</span><span class="sxs-lookup"><span data-stu-id="94fd5-151">Before you can establish a connection, you have to create a `HubConnection` object and create a proxy.</span></span> <span data-ttu-id="94fd5-152">Rufen Sie zum Herstellen einer Verbindung, die `Start` Methode für die `HubConnection` Objekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-152">To establish the connection, call the `Start` method on the `HubConnection` object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> <span data-ttu-id="94fd5-153">JavaScript-Clients müssen Sie mindestens einen Ereignishandler vor dem Aufruf registriert die `Start` Methode zum Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="94fd5-153">For JavaScript clients you have to register at least one event handler before calling the `Start` method to establish the connection.</span></span> <span data-ttu-id="94fd5-154">Dies ist nicht für .NET-Clients erforderlich.</span><span class="sxs-lookup"><span data-stu-id="94fd5-154">This is not necessary for .NET clients.</span></span> <span data-ttu-id="94fd5-155">Für JavaScript-Clients, erstellt des generierte Proxycodes-Proxys für alle Hubs, die vorhanden sind automatisch auf dem Server, und Registrieren eines Handlers ist, geben Sie die Hubs an der Client verwenden will.</span><span class="sxs-lookup"><span data-stu-id="94fd5-155">For JavaScript clients, the generated proxy code automatically creates proxies for all Hubs that exist on the server, and registering a handler is how you indicate which Hubs your client intends to use.</span></span> <span data-ttu-id="94fd5-156">Aber für einen .NET Client Hub Proxys manuell erstellt, damit SignalR wird davon ausgegangen, dass Sie eine Hub-Instanz verwendet werden, die Sie über einen Proxy für erstellen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-156">But for a .NET client you create Hub proxies manually, so SignalR assumes that you will be using any Hub that you create a proxy for.</span></span>


<span data-ttu-id="94fd5-157">Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="94fd5-157">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="94fd5-158">WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="94fd5-158">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="94fd5-159">Wenn die `Start` Methode asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="94fd5-159">The `Start` method executes asynchronously.</span></span> <span data-ttu-id="94fd5-160">Um sicherzustellen, dass es sich bei nachfolgenden Codezeilen nicht erst ausführen, nachdem die Verbindung hergestellt ist, verwenden Sie `await` in einer asynchronen Methode von ASP.NET 4.5 oder `.Wait()` in eine synchrone Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-160">To make sure that subsequent lines of code don't execute until after the connection is established, use `await` in an ASP.NET 4.5 asynchronous method or `.Wait()` in a synchronous method.</span></span> <span data-ttu-id="94fd5-161">Verwenden Sie keine `.Wait()` in einem WinRT-Client.</span><span class="sxs-lookup"><span data-stu-id="94fd5-161">Don't use `.Wait()` in a WinRT client.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](signalr-1x-hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<span data-ttu-id="94fd5-162">Die `HubConnection`-Klasse ist threadsicher.</span><span class="sxs-lookup"><span data-stu-id="94fd5-162">The `HubConnection` class is thread-safe.</span></span>

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a><span data-ttu-id="94fd5-163">Domänenübergreifende Verbindungen von Silverlight-clients</span><span class="sxs-lookup"><span data-stu-id="94fd5-163">Cross-domain connections from Silverlight clients</span></span>

<span data-ttu-id="94fd5-164">Informationen dazu, wie Sie die domänenübergreifende Verbindungen von Silverlight-Clients zu aktivieren, finden Sie unter [vornehmen einer Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).</span><span class="sxs-lookup"><span data-stu-id="94fd5-164">For information about how to enable cross-domain connections from Silverlight clients, see [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).</span></span>

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="94fd5-165">Gewusst wie: Konfigurieren der Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-165">How to configure the connection</span></span>

<span data-ttu-id="94fd5-166">Bevor Sie eine Verbindung herstellen, können Sie eine der folgenden Optionen angeben:</span><span class="sxs-lookup"><span data-stu-id="94fd5-166">Before you establish a connection, you can specify any of the following options:</span></span>

- <span data-ttu-id="94fd5-167">Gleichzeitige Verbindungen beschränken.</span><span class="sxs-lookup"><span data-stu-id="94fd5-167">Concurrent connections limit.</span></span>
- <span data-ttu-id="94fd5-168">Abfragezeichenfolgenparameter.</span><span class="sxs-lookup"><span data-stu-id="94fd5-168">Query string parameters.</span></span>
- <span data-ttu-id="94fd5-169">Die Transportmethode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-169">The transport method.</span></span>
- <span data-ttu-id="94fd5-170">HTTP-Header.</span><span class="sxs-lookup"><span data-stu-id="94fd5-170">HTTP headers.</span></span>
- <span data-ttu-id="94fd5-171">Clientzertifikate.</span><span class="sxs-lookup"><span data-stu-id="94fd5-171">Client certificates.</span></span>

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a><span data-ttu-id="94fd5-172">Die maximale Anzahl gleichzeitiger Verbindungen in WPF-Clients festlegen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-172">How to set the maximum number of concurrent connections in WPF clients</span></span>

<span data-ttu-id="94fd5-173">In WPF-Clients müssen Sie möglicherweise die maximale Anzahl gleichzeitiger Verbindungen vom Standardwert 2 erhöhen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-173">In WPF clients, you might have to increase the maximum number of concurrent connections from its default value of 2.</span></span> <span data-ttu-id="94fd5-174">Der empfohlene Wert ist 10.</span><span class="sxs-lookup"><span data-stu-id="94fd5-174">The recommended value is 10.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

<span data-ttu-id="94fd5-175">Weitere Informationen finden Sie unter [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).</span><span class="sxs-lookup"><span data-stu-id="94fd5-175">For more information, see [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="94fd5-176">Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter</span><span class="sxs-lookup"><span data-stu-id="94fd5-176">How to specify query string parameters</span></span>

<span data-ttu-id="94fd5-177">Wenn Sie möchten die Daten an den Server gesendet, wenn der Client eine Verbindung herstellt, können Sie Abfragezeichenfolgen-Parameter an das Verbindungsobjekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-177">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="94fd5-178">Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter in Client-Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-178">The following example shows how to set a query string parameter in client code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample5.cs)]

<span data-ttu-id="94fd5-179">Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter im Servercode zu lesen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-179">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="94fd5-180">Das Angeben der Transportmethode</span><span class="sxs-lookup"><span data-stu-id="94fd5-180">How to specify the transport method</span></span>

<span data-ttu-id="94fd5-181">Als Teil der Prozess der verbindungsherstellung verhandelt ein SignalR-Client normalerweise mit dem Server um den besten Transport zu bestimmen, der unterstützt wird, indem sowohl Server-als auch.</span><span class="sxs-lookup"><span data-stu-id="94fd5-181">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="94fd5-182">Wenn Sie bereits wissen, dass Transport Sie verwenden möchten, können Sie dieser Aushandlungsprozess umgehen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-182">If you already know which transport you want to use, you can bypass this negotiation process.</span></span> <span data-ttu-id="94fd5-183">Um die Transportmethode anzugeben, übergeben Sie einen Transport-Objekt an die Start-Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-183">To specify the transport method, pass in a transport object to the Start method.</span></span> <span data-ttu-id="94fd5-184">Das folgende Beispiel zeigt, wie die Transportmethode im Clientcode angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="94fd5-184">The following example shows how to specify the transport method in client code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

<span data-ttu-id="94fd5-185">Die [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) Namespace enthält die folgenden Klassen, die Sie verwenden können, an den Transport.</span><span class="sxs-lookup"><span data-stu-id="94fd5-185">The [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) namespace includes the following classes that you can use to specify the transport.</span></span>

- <span data-ttu-id="94fd5-186">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="94fd5-186">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="94fd5-187">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="94fd5-187">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="94fd5-188">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (nur verfügbar, wenn sowohl Server-als auch .NET 4.5 verwenden.)</span><span class="sxs-lookup"><span data-stu-id="94fd5-188">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (Available only when both server and client use .NET 4.5.)</span></span>
- <span data-ttu-id="94fd5-189">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (wählt automatisch den besten Transport, die von dem Client und dem Server unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="94fd5-189">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (Automatically chooses the best transport that is supported by both the client and the server.</span></span> <span data-ttu-id="94fd5-190">Dies ist der Standardtransport.</span><span class="sxs-lookup"><span data-stu-id="94fd5-190">This is the default transport.</span></span> <span data-ttu-id="94fd5-191">Übergeben dies in der `Start` Methode hat dieselbe Wirkung wie das alles nicht übergeben.)</span><span class="sxs-lookup"><span data-stu-id="94fd5-191">Passing this in to the `Start` method has the same effect as not passing in anything.)</span></span>

<span data-ttu-id="94fd5-192">Der Transport ForeverFrame ist nicht in dieser Liste enthalten, da er nur von Browsern verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="94fd5-192">The ForeverFrame transport is not included in this list because it is used only by browsers.</span></span>

<span data-ttu-id="94fd5-193">Informationen zum Überprüfen der Transportmethode in Server-Code finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server – zum Abrufen von Informationen über den Client aus der Kontexteigenschaft](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="94fd5-193">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="94fd5-194">Weitere Informationen zu Transporten und Fallbacks, finden Sie unter [Einführung zu SignalR - Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="94fd5-194">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a><span data-ttu-id="94fd5-195">Gewusst wie: Angeben von HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="94fd5-195">How to specify HTTP headers</span></span>

<span data-ttu-id="94fd5-196">Um HTTP-Header festzulegen, verwenden die `Headers` Eigenschaft für das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-196">To set HTTP headers, use the `Headers` property on the connection object.</span></span> <span data-ttu-id="94fd5-197">Das folgende Beispiel zeigt, wie Sie einen HTTP-Header hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-197">The following example shows how to add an HTTP header.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a><span data-ttu-id="94fd5-198">Gewusst wie: Geben Sie Clientzertifikate</span><span class="sxs-lookup"><span data-stu-id="94fd5-198">How to specify client certificates</span></span>

<span data-ttu-id="94fd5-199">Verwenden Sie zum Hinzufügen von Clientzertifikaten die `AddClientCertificate` Methode für das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-199">To add client certificates, use the `AddClientCertificate` method on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a><span data-ttu-id="94fd5-200">Vorgehensweise: Erstellen Sie die Hubproxy-Klasse</span><span class="sxs-lookup"><span data-stu-id="94fd5-200">How to create the Hub proxy</span></span>

<span data-ttu-id="94fd5-201">Um Methoden auf dem Client definieren, die ein Hub vom Server aufrufen können, und zum Aufrufen von Methoden für einen Hub auf dem Server, erstellen Sie einen Proxy für den Hub durch Aufrufen von `CreateHubProxy` für das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-201">In order to define methods on the client that a Hub can call from the server, and to invoke methods on a Hub at the server, create a proxy for the Hub by calling `CreateHubProxy` on the connection object.</span></span> <span data-ttu-id="94fd5-202">Die Zeichenfolge übergebenen `CreateHubProxy` ist der Name der Hub-Klasse, oder den Namen trägt die `HubName` Attribut, wenn auf dem Server verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="94fd5-202">The string you pass in to `CreateHubProxy` is the name of your Hub class, or the name specified by the `HubName` attribute if one was used on the server.</span></span> <span data-ttu-id="94fd5-203">Die namenszuordnung wird Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="94fd5-203">Name matching is case-insensitive.</span></span>

<span data-ttu-id="94fd5-204">**Hub-Klasse auf server**</span><span class="sxs-lookup"><span data-stu-id="94fd5-204">**Hub class on server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

<span data-ttu-id="94fd5-205">**Erstellen des Clientproxys für Hub-Klasse**</span><span class="sxs-lookup"><span data-stu-id="94fd5-205">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

<span data-ttu-id="94fd5-206">Wenn Sie ergänzen, um Ihre hubklasse mit einem `HubName` -Attributs festzulegen, verwenden Sie diesen Namen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-206">If you decorate your Hub class with a `HubName` attribute, use that name.</span></span>

<span data-ttu-id="94fd5-207">**Hub-Klasse auf server**</span><span class="sxs-lookup"><span data-stu-id="94fd5-207">**Hub class on server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample12.cs)]

<span data-ttu-id="94fd5-208">**Erstellen des Clientproxys für Hub-Klasse**</span><span class="sxs-lookup"><span data-stu-id="94fd5-208">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

<span data-ttu-id="94fd5-209">Das Proxyobjekt ist threadsicher.</span><span class="sxs-lookup"><span data-stu-id="94fd5-209">The proxy object is thread-safe.</span></span> <span data-ttu-id="94fd5-210">In der Tat, wenn Sie aufrufen `HubConnection.CreateHubProxy` mehrmals mit dem gleichen `hubName`, erhalten Sie dieselbe zwischengespeichert `IHubProxy` Objekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-210">In fact, if you call `HubConnection.CreateHubProxy` multiple times with the same `hubName`, you get the same cached `IHubProxy` object.</span></span>

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="94fd5-211">Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="94fd5-211">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="94fd5-212">Um eine Methode definieren, die der Server aufrufen können, verwenden Sie den Proxy des `On` Methode, um einen Ereignishandler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="94fd5-212">To define a method that the server can call, use the proxy's `On` method to register an event handler.</span></span>

<span data-ttu-id="94fd5-213">Methode-namenszuordnung wird Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="94fd5-213">Method name matching is case-insensitive.</span></span> <span data-ttu-id="94fd5-214">Z. B. `Clients.All.UpdateStockPrice` auf dem Server führt `updateStockPrice`, `updatestockprice`, oder `UpdateStockPrice` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="94fd5-214">For example, `Clients.All.UpdateStockPrice` on the server will execute `updateStockPrice`, `updatestockprice`, or `UpdateStockPrice` on the client.</span></span>

<span data-ttu-id="94fd5-215">Verschiedene Clientplattformen haben unterschiedliche Anforderungen an wie Sie den Code schreiben, die Benutzeroberfläche aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="94fd5-215">Different client platforms have different requirements for how you write method code to update the UI.</span></span> <span data-ttu-id="94fd5-216">Die dargestellten Beispiele gelten für WinRT (Windows Store-.NET)-Clients.</span><span class="sxs-lookup"><span data-stu-id="94fd5-216">The examples shown are for WinRT (Windows Store .NET) clients.</span></span> <span data-ttu-id="94fd5-217">WPF, Silverlight und Beispiele für Konsole finden Sie unter [einem separaten Abschnitt weiter unten in diesem Thema](#wpfsl).</span><span class="sxs-lookup"><span data-stu-id="94fd5-217">WPF, Silverlight, and console application examples are provided in [a separate section later in this topic](#wpfsl).</span></span>

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a><span data-ttu-id="94fd5-218">Methoden ohne Parameter</span><span class="sxs-lookup"><span data-stu-id="94fd5-218">Methods without parameters</span></span>

<span data-ttu-id="94fd5-219">Wenn die Methode Sie verarbeiten nicht über Parameter verfügt, verwenden Sie die nicht generische Überladung von der `On` Methode:</span><span class="sxs-lookup"><span data-stu-id="94fd5-219">If the method you're handling does not have parameters, use the non-generic overload of the `On` method:</span></span>

<span data-ttu-id="94fd5-220">**Clientmethode ohne Parameter aufrufen Servercode**</span><span class="sxs-lookup"><span data-stu-id="94fd5-220">**Server code calling client method without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

<span data-ttu-id="94fd5-221">**WinRT-Clientcode für die Methode, die vom Server ohne Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="94fd5-221">**WinRT Client code for method called from server without parameters ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="94fd5-222">Methoden mit Parametern, die Parametertypen angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-222">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="94fd5-223">Wenn die Methode, die Sie verarbeiten Parameter verfügt, geben Sie die Typen der Parameter als die generischen Typen von der `On` Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-223">If the method you're handling has parameters, specify the types of the parameters as the generic types of the `On` method.</span></span> <span data-ttu-id="94fd5-224">Es gibt generische Überladung von der `On` Methode, um Ihnen die Angabe von bis zu 8 Parameter (4 unter Windows Phone 7) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="94fd5-224">There are generic overloads of the `On` method to enable you to specify up to 8 parameters (4 on Windows Phone 7).</span></span> <span data-ttu-id="94fd5-225">Im folgenden Beispiel wird ein Parameter gesendet, auf die `UpdateStockPrice` Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-225">In the following example, one parameter is sent to the `UpdateStockPrice` method.</span></span>

<span data-ttu-id="94fd5-226">**Aufrufen der Methode mit einem Parameter Servercode**</span><span class="sxs-lookup"><span data-stu-id="94fd5-226">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

<span data-ttu-id="94fd5-227">**Die Stock-Klasse für den Parameter verwendet**</span><span class="sxs-lookup"><span data-stu-id="94fd5-227">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample17.cs)]

<span data-ttu-id="94fd5-228">**WinRT-Clientcode für eine Methode, die vom Server mit einem Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="94fd5-228">**WinRT Client code for a method called from server with a parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="94fd5-229">Methoden mit Parametern, dynamische Objekte für die Parameter angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-229">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="94fd5-230">Als Alternative zum Angeben von Parametern als generische Typen, die von der `On` -Methode, Sie können Parameter angeben, als dynamische Objekte:</span><span class="sxs-lookup"><span data-stu-id="94fd5-230">As an alternative to specifying parameters as generic types of the `On` method, you can specify parameters as dynamic objects:</span></span>

<span data-ttu-id="94fd5-231">**Aufrufen der Methode mit einem Parameter Servercode**</span><span class="sxs-lookup"><span data-stu-id="94fd5-231">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

<span data-ttu-id="94fd5-232">**Die Stock-Klasse für den Parameter verwendet**</span><span class="sxs-lookup"><span data-stu-id="94fd5-232">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample20.cs)]

<span data-ttu-id="94fd5-233">**WinRT-Clientcode für eine Methode, die vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="94fd5-233">**WinRT Client code for a method called from server with a parameter, using a dynamic object for the parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a><span data-ttu-id="94fd5-234">Vorgehensweise beim Entfernen eines Handlers</span><span class="sxs-lookup"><span data-stu-id="94fd5-234">How to remove a handler</span></span>

<span data-ttu-id="94fd5-235">Um einen Handler zu entfernen, rufen Sie die `Dispose` Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-235">To remove a handler, call its `Dispose` method.</span></span>

<span data-ttu-id="94fd5-236">**Clientcode für eine vom Server aufgerufene Methode**</span><span class="sxs-lookup"><span data-stu-id="94fd5-236">**Client code for a method called from server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

<span data-ttu-id="94fd5-237">**Client-Code, um den Ereignishandler zu entfernen**</span><span class="sxs-lookup"><span data-stu-id="94fd5-237">**Client code to remove the handler**</span></span>

[!code-css[Main](signalr-1x-hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="94fd5-238">Gewusst wie: Servermethoden vom Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94fd5-238">How to call server methods from the client</span></span>

<span data-ttu-id="94fd5-239">Verwenden Sie zum Aufrufen einer Methode auf dem Server die `Invoke` Methode für die Hubproxy-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94fd5-239">To call a method on the server, use the `Invoke` method on the Hub proxy.</span></span>

<span data-ttu-id="94fd5-240">Verfügt die Server-Methode keinen Wert zurückgibt, verwenden Sie die nicht generische Überladung von der `Invoke` Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-240">If the server method has no return value, use the non-generic overload of the `Invoke` method.</span></span>

<span data-ttu-id="94fd5-241">**Servercode für eine Methode, die keinen zurückgibt Wert**</span><span class="sxs-lookup"><span data-stu-id="94fd5-241">**Server code for a method that has no return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

<span data-ttu-id="94fd5-242">**Client-Code Aufrufen einer Methode, die keinen zurückgibt Wert**</span><span class="sxs-lookup"><span data-stu-id="94fd5-242">**Client code calling a method that has no return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

<span data-ttu-id="94fd5-243">Wenn die Server-Methode einen Rückgabewert verfügt, geben Sie den Rückgabetyp als den generischen Typ, der die `Invoke` Methode.</span><span class="sxs-lookup"><span data-stu-id="94fd5-243">If the server method has a return value, specify the return type as the generic type of the `Invoke` method.</span></span>

<span data-ttu-id="94fd5-244">**Servercode für eine Methode, die hat einen Rückgabewert und nimmt einen Parameter des komplexen Typs**</span><span class="sxs-lookup"><span data-stu-id="94fd5-244">**Server code for a method that has a return value and takes a complex type parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

<span data-ttu-id="94fd5-245">**Die Stock-Klasse, die für die Parameter und Rückgabewert verwendet**</span><span class="sxs-lookup"><span data-stu-id="94fd5-245">**The Stock class used for the parameter and return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample27.cs)]

<span data-ttu-id="94fd5-246">**Client-Code Aufrufen einer Methode, die einen Rückgabewert und nimmt einen Parameter des komplexen Typs, in einer ASP.NET 4.5-Async-Methode**</span><span class="sxs-lookup"><span data-stu-id="94fd5-246">**Client code calling a method that has a return value and takes a complex type parameter, in an ASP.NET 4.5 async method**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

<span data-ttu-id="94fd5-247">**Aufrufen einer Methode, die einen Rückgabewert und eine synchrone Methode nimmt einen Parameter des komplexen Typs, der Client-code**</span><span class="sxs-lookup"><span data-stu-id="94fd5-247">**Client code calling a method that has a return value and takes a complex type parameter, in a synchronous method**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

<span data-ttu-id="94fd5-248">Die `Invoke` Methode asynchron ausgeführt wird, und gibt eine `Task` Objekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-248">The `Invoke` method executes asynchronously and returns a `Task` object.</span></span> <span data-ttu-id="94fd5-249">Wenn Sie keinen `await` oder `.Wait()`, die nächste Codezeile ausgeführt, bevor die Methode, die Sie aufrufen beendet wurde.</span><span class="sxs-lookup"><span data-stu-id="94fd5-249">If you don't specify `await` or `.Wait()`, the next line of code will execute before the method that you invoke has finished executing.</span></span>

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="94fd5-250">Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung</span><span class="sxs-lookup"><span data-stu-id="94fd5-250">How to handle connection lifetime events</span></span>

<span data-ttu-id="94fd5-251">SignalR bietet die folgenden Verbindung, die Objektlebensdauer-Ereignisse, die Sie behandeln können:</span><span class="sxs-lookup"><span data-stu-id="94fd5-251">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="94fd5-252">`Received`: Wird ausgelöst, wenn alle Daten für die Verbindung empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="94fd5-252">`Received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="94fd5-253">Stellt die empfangenen Daten bereit.</span><span class="sxs-lookup"><span data-stu-id="94fd5-253">Provides the received data.</span></span>
- <span data-ttu-id="94fd5-254">`ConnectionSlow`: Wird ausgelöst, wenn der Client eine Verbindung langsame ist oder häufig löschen erkennt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-254">`ConnectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="94fd5-255">`Reconnecting`: Wird ausgelöst, wenn des zugrunde liegenden Transports, erneut eine Verbindung herzustellen beginnt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-255">`Reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="94fd5-256">`Reconnected`: Wird ausgelöst, wenn der zugrunde liegenden Transport die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="94fd5-256">`Reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="94fd5-257">`StateChanged`: Wird ausgelöst, wenn der Verbindungsstatus ändert.</span><span class="sxs-lookup"><span data-stu-id="94fd5-257">`StateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="94fd5-258">Enthält der alte Status und den neuen Zustand.</span><span class="sxs-lookup"><span data-stu-id="94fd5-258">Provides the old state and the new state.</span></span> <span data-ttu-id="94fd5-259">Informationen zur Verbindung finden Sie unter Statuswerte [ConnectionState-Enumeration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="94fd5-259">For information about connection state values see [ConnectionState Enumeration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).</span></span>
- <span data-ttu-id="94fd5-260">`Closed`: Wird ausgelöst, wenn die Verbindung getrennt wurde.</span><span class="sxs-lookup"><span data-stu-id="94fd5-260">`Closed`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="94fd5-261">Beispielsweise sollten Sie Warnmeldungen für Fehler angezeigt, die nicht schwerwiegend sind, aber dazu führen, dass zeitweilige Verbindungsprobleme, wie die Langsamkeit oder häufige Löschen der Verbindung, behandeln die `ConnectionSlow` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="94fd5-261">For example, if you want to display warning messages for errors that are not fatal but cause intermittent connection problems, such as slowness or frequent dropping of the connection, handle the `ConnectionSlow` event.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample30.cs)]

<span data-ttu-id="94fd5-262">Weitere Informationen finden Sie unter [verstehen und Behandeln von Ereignissen für Lebensdauer in SignalR](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="94fd5-262">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="94fd5-263">Gewusst wie: Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="94fd5-263">How to handle errors</span></span>

<span data-ttu-id="94fd5-264">Wenn Sie detaillierte Fehlermeldungen auf dem Server nicht explizit aktivieren, enthält das Ausnahmeobjekt an, dem SignalR nach einem Fehler zurückgibt minimale Informationen über den Fehler an.</span><span class="sxs-lookup"><span data-stu-id="94fd5-264">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="94fd5-265">Beispielsweise wenn ein Aufruf von `newContosoChatMessage` ein Fehler auftritt, der die Fehlermeldung in das Fehlerobjekt enthält "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Senden detaillierte Fehlermeldungen für Clients in einer produktionsumgebung wird nicht empfohlen aus Sicherheitsgründen aber wenn Sie möchten detaillierte Fehlermeldungen für aktivieren Problembehandlung, verwenden Sie den folgenden Code auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="94fd5-265">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

<span data-ttu-id="94fd5-266">Zur Behandlung von Fehlern, die SignalR auslöst, können Sie hinzufügen, einen Handler für die `Error` Ereignis für das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-266">To handle errors that SignalR raises, you can add a handler for the `Error` event on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample32.cs)]

<span data-ttu-id="94fd5-267">Zur Behandlung von Fehlern von Methodenaufrufen, umschließen Sie den Code in einem Try / Catch-Block.</span><span class="sxs-lookup"><span data-stu-id="94fd5-267">To handle errors from method invocations, wrap the code in a try-catch block.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="94fd5-268">Gewusst wie: Aktivieren der clientseitigen Protokollierung</span><span class="sxs-lookup"><span data-stu-id="94fd5-268">How to enable client-side logging</span></span>

<span data-ttu-id="94fd5-269">Um die clientseitige Protokollierung zu aktivieren, legen die `TraceLevel` und `TraceWriter` Eigenschaften für das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="94fd5-269">To enable client-side logging, set the `TraceLevel` and `TraceWriter` properties on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a><span data-ttu-id="94fd5-270">WPF, Silverlight und Konsolenanwendung Codebeispiele für Clientmethoden, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="94fd5-270">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>

<span data-ttu-id="94fd5-271">Die Codebeispiele, die zuvor gezeigte zum Definieren von Clientmethoden, die der Server aufrufen kann, gelten für WinRT-Clients.</span><span class="sxs-lookup"><span data-stu-id="94fd5-271">The code samples shown earlier for defining client methods that the server can call apply to WinRT clients.</span></span> <span data-ttu-id="94fd5-272">Den folgenden Beispielen wird den entsprechenden Code für WPF, Silverlight und Console Application-Clients.</span><span class="sxs-lookup"><span data-stu-id="94fd5-272">The following samples show the equivalent code for WPF, Silverlight, and console application clients.</span></span>

### <a name="methods-without-parameters"></a><span data-ttu-id="94fd5-273">Methoden ohne Parameter</span><span class="sxs-lookup"><span data-stu-id="94fd5-273">Methods without parameters</span></span>

<span data-ttu-id="94fd5-274">**WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen.**</span><span class="sxs-lookup"><span data-stu-id="94fd5-274">**WPF client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

<span data-ttu-id="94fd5-275">**Silverlight-Client-Code für die Methode wird vom Server ohne Parameter aufgerufen.**</span><span class="sxs-lookup"><span data-stu-id="94fd5-275">**Silverlight client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

<span data-ttu-id="94fd5-276">**Console Application-Clientcode für die Methode aufgerufen wird, vom Server ohne Parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-276">**Console application client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="94fd5-277">Methoden mit Parametern, die Parametertypen angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-277">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="94fd5-278">**WPF-Client-Code für eine Methode aufgerufen wird, vom Server mit einem parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-278">**WPF client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

<span data-ttu-id="94fd5-279">**Silverlight-Client-Code für eine Methode aufgerufen wird, vom Server mit einem parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-279">**Silverlight client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

<span data-ttu-id="94fd5-280">**Console Application-Clientcode für eine Methode aufgerufen wird, vom Server mit einem parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-280">**Console application client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="94fd5-281">Methoden mit Parametern, dynamische Objekte für die Parameter angeben</span><span class="sxs-lookup"><span data-stu-id="94fd5-281">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="94fd5-282">**WPF-Client-Code für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-282">**WPF client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

<span data-ttu-id="94fd5-283">**Silverlight-Client-Code für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-283">**Silverlight client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

<span data-ttu-id="94fd5-284">**Console Application-Clientcode für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**</span><span class="sxs-lookup"><span data-stu-id="94fd5-284">**Console application client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]
