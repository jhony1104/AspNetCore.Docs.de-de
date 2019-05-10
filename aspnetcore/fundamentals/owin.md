---
title: Open Web Interface for .NET (OWIN) mit ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core die Open Web Interface for .NET (OWIN) unterstützt, die das Entkoppeln von Web-Apps von Webservern ermöglicht.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 9d6ce79c15fe768c260c6361ac3babecab5f3f9b
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087299"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="d897f-103">Open Web Interface for .NET (OWIN) mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d897f-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="d897f-104">Von [Steve Smith](https://ardalis.com/) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d897f-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d897f-105">ASP.NET Core unterstützt Open Web Interface for .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="d897f-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="d897f-106">Mit OWIN können Web-Apps von Webservern entkoppelt werden.</span><span class="sxs-lookup"><span data-stu-id="d897f-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="d897f-107">OWIN legt eine Standardmöglichkeit des Einsatzes von Middleware in einer Pipeline zum Verarbeiten von Anforderungen und den entsprechenden Antworten fest.</span><span class="sxs-lookup"><span data-stu-id="d897f-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="d897f-108">ASP.NET Core-Anwendungen und -Middleware können mit auf OWIN basierten Anwendungen, Servern und OWIN basierter Middleware zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d897f-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="d897f-109">OWIN bietet eine Entkopplungsebene, die das gemeinsame Verwenden zweier Frameworks mit unterschiedlichen Objektmodellen zulässt.</span><span class="sxs-lookup"><span data-stu-id="d897f-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="d897f-110">Das `Microsoft.AspNetCore.Owin`-Paket bietet zwei Adapterimplementierungen:</span><span class="sxs-lookup"><span data-stu-id="d897f-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="d897f-111">ASP.NET Core auf OWIN</span><span class="sxs-lookup"><span data-stu-id="d897f-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="d897f-112">OWIN auf ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d897f-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="d897f-113">So kann ASP.NET Core auf einem mit OWIN kompatiblen Server bzw. Host gehostet werden. Alternativ können andere mit OWIN kompatible Komponenten über ASP.NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d897f-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d897f-114">Das Verwenden dieser Adapter führt zu Leistungseinbußen.</span><span class="sxs-lookup"><span data-stu-id="d897f-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="d897f-115">Apps, die nur ASP.NET Core-Komponenten verwenden, sollten keine `Microsoft.AspNetCore.Owin`-Pakete oder Adapter verwenden.</span><span class="sxs-lookup"><span data-stu-id="d897f-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="d897f-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d897f-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="d897f-117">Ausführen von OWIN-Middleware in der ASP.NET Core-Pipeline</span><span class="sxs-lookup"><span data-stu-id="d897f-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="d897f-118">Die OWIN-Unterstützung von ASP.NET Core wird im Rahmen des `Microsoft.AspNetCore.Owin`-Pakets bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d897f-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="d897f-119">Sie können OWIN-Unterstützung in Ihrem Projekt ermöglichen, indem Sie dieses Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="d897f-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="d897f-120">OWIN-Middleware stimmt mit der [OWIN-Spezifikation](http://owin.org/spec/spec/owin-1.0.0.html) überein, die eine `Func<IDictionary<string, object>, Task>`-Schnittstelle und das Festlegen spezifischer Schlüssel erfordert (wie z.B. `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="d897f-120">OWIN middleware conforms to the [OWIN specification](http://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="d897f-121">Die folgende einfache OWIN-Middleware zeigt „Hello World“ (Hallo Welt) an:</span><span class="sxs-lookup"><span data-stu-id="d897f-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="d897f-122">Die Beispielsignatur gibt ein `Task`-Objekt zurück und akzeptiert ein `IDictionary<string, object>`, wie OWIN es erfordert.</span><span class="sxs-lookup"><span data-stu-id="d897f-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="d897f-123">Der folgende Code veranschaulicht, wie Sie die `OwinHello`-Middleware (siehe oben) der ASP.NET Core-Pipeline mit der `UseOwin`-Erweiterungsmethode hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d897f-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="d897f-124">Sie können andere Aktionen konfigurieren, die in der OWIN-Pipeline durchgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d897f-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="d897f-125">Antwortheader sollten nur vor dem ersten Schreiben in den Antwortstream modifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="d897f-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="d897f-126">Mehrere Aufrufe von `UseOwin` werden aus Leistungsgründen nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="d897f-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="d897f-127">OWIN-Komponenten funktionieren am besten, wenn sie gruppiert werden.</span><span class="sxs-lookup"><span data-stu-id="d897f-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(async (next) =>
    {
        // do something before
        await OwinHello(new OwinEnvironment(HttpContext));
        // do something after
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="d897f-128">ASP.NET Core-Hosting auf einem auf OWIN basierten Server</span><span class="sxs-lookup"><span data-stu-id="d897f-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="d897f-129">Auf OWIN basierte Server können ASP.NET Core-Anwendungen hosten.</span><span class="sxs-lookup"><span data-stu-id="d897f-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="d897f-130">Ein derartiger Server ist [Nowin](https://github.com/Bobris/Nowin), ein Webserver von .NET OWIN.</span><span class="sxs-lookup"><span data-stu-id="d897f-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="d897f-131">Das Beispiel für diesen Artikel enthält ein Projekt, das auf Nowin verweist und ihn verwendet, um einen `IServer` zu erstellen, der ASP.NET Core eigenständig hosten kann.</span><span class="sxs-lookup"><span data-stu-id="d897f-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="d897f-132">`IServer` ist eine Schnittstelle, die eine `Features`-Eigenschaft und eine `Start`-Methode erfordert.</span><span class="sxs-lookup"><span data-stu-id="d897f-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="d897f-133">`Start` ist für die Konfiguration und das Starten des Server zuständig. In diesem Fall erfolgt dies durch eine Folge von Aufrufen von Fluent-APIs, die Adressen festgelegt haben, die von IServerAddressesFeature analysiert wurden.</span><span class="sxs-lookup"><span data-stu-id="d897f-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="d897f-134">Beachten Sie, dass die fließende Konfiguration der `_builder`-Variablen angibt, das Anforderungen vom `appFunc`-Objekt verarbeitet werden, das bereits in der Methode festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="d897f-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="d897f-135">Dieses `Func`-Objekt wird bei jeder Anforderung aufgerufen, um eingehende Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d897f-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="d897f-136">Außerdem fügen wir eine `IWebHostBuilder`-Erweiterung hinzu, um den Nowin-Server leicht hinzufügen und konfigurieren zu können.</span><span class="sxs-lookup"><span data-stu-id="d897f-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="d897f-137">Rufen Sie anschließend die Erweiterung in *Program.cs* auf, um eine ASP.NET Core-App auszuführen, die diesen benutzerdefinierten Server verwendet:</span><span class="sxs-lookup"><span data-stu-id="d897f-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="d897f-138">Weitere Informationen zu [ASP.NET Core-Servern](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d897f-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="d897f-139">Ausführen von ASP.NET Core auf einem auf OWIN basierten Server und Nutzen der WebSockets-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="d897f-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="d897f-140">Der Zugriff auf Features wie WebSockets ist ein weiteres Beispiel dafür, wie ASP.NET Core Features von auf OWIN basierten Servern nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="d897f-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="d897f-141">Der .NET OWIN-Webserver, der im vorherigen Beispiel verwendet wurde, verfügt über integrierte Unterstützung für WebSockets. Dies kann von einer ASP.NET Core-Anwendung genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="d897f-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="d897f-142">Das unten stehende Beispiel zeigt eine einfache Web-App, die WebSockets unterstützt und alles zurück gibt, was über WebSockets an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d897f-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="d897f-143">Dieses [Beispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) wird mit dem gleichen `NowinServer` wie vorher konfiguriert. Der einzige Unterschied besteht in der Konfiguration der Anwendung in deren `Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d897f-143">This [sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="d897f-144">Ein Text mit einem [einfachen WebSocket-Client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) veranschaulicht die Anwendung:</span><span class="sxs-lookup"><span data-stu-id="d897f-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![WebSocket-Testclient](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="d897f-146">OWIN-Umgebung</span><span class="sxs-lookup"><span data-stu-id="d897f-146">OWIN environment</span></span>

<span data-ttu-id="d897f-147">Sie können mit `HttpContext` eine OWIN-Umgebung erstellen.</span><span class="sxs-lookup"><span data-stu-id="d897f-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="d897f-148">OWIN-Schlüssel</span><span class="sxs-lookup"><span data-stu-id="d897f-148">OWIN keys</span></span>

<span data-ttu-id="d897f-149">OWIN ist von einem `IDictionary<string,object>`-Objekt abhängig, um Informationen innerhalb eines Austauschs einer HTTP-Anforderung und einer Antwort weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="d897f-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="d897f-150">ASP.NET Core implementiert die unten aufgelisteten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d897f-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="d897f-151">Weitere Informationen finden Sie in den [Hauptspezifikationen und Erweiterungen](http://owin.org/#spec) und in den [OWIN Key Guidelines and Common Keys (Wichtigste Richtlinien und gängige Schlüsse von OWIN)](http://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="d897f-151">See the [primary specification, extensions](http://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](http://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="d897f-152">Anforderungsdaten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d897f-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d897f-153">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-153">Key</span></span>               | <span data-ttu-id="d897f-154">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-154">Value (type)</span></span> | <span data-ttu-id="d897f-155">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="d897f-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="d897f-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="d897f-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="d897f-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="d897f-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="d897f-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="d897f-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="d897f-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="d897f-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="d897f-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="d897f-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="d897f-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="d897f-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d897f-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="d897f-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="d897f-164">Anforderungsdaten (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="d897f-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="d897f-165">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-165">Key</span></span>               | <span data-ttu-id="d897f-166">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-166">Value (type)</span></span> | <span data-ttu-id="d897f-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="d897f-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="d897f-169">Optional</span><span class="sxs-lookup"><span data-stu-id="d897f-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="d897f-170">Antwortdaten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d897f-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d897f-171">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-171">Key</span></span>               | <span data-ttu-id="d897f-172">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-172">Value (type)</span></span> | <span data-ttu-id="d897f-173">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="d897f-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="d897f-175">Optional</span><span class="sxs-lookup"><span data-stu-id="d897f-175">Optional</span></span> |
| <span data-ttu-id="d897f-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="d897f-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="d897f-177">Optional</span><span class="sxs-lookup"><span data-stu-id="d897f-177">Optional</span></span> |
| <span data-ttu-id="d897f-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="d897f-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d897f-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="d897f-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="d897f-180">Andere Daten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d897f-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d897f-181">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-181">Key</span></span>               | <span data-ttu-id="d897f-182">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-182">Value (type)</span></span> | <span data-ttu-id="d897f-183">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d897f-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d897f-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="d897f-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="d897f-186">Gängige Schlüssel</span><span class="sxs-lookup"><span data-stu-id="d897f-186">Common keys</span></span>

| <span data-ttu-id="d897f-187">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-187">Key</span></span>               | <span data-ttu-id="d897f-188">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-188">Value (type)</span></span> | <span data-ttu-id="d897f-189">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="d897f-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="d897f-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="d897f-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="d897f-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="d897f-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d897f-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="d897f-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="d897f-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="d897f-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d897f-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="d897f-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="d897f-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="d897f-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="d897f-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="d897f-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="d897f-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d897f-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="d897f-199">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-199">Key</span></span>               | <span data-ttu-id="d897f-200">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-200">Value (type)</span></span> | <span data-ttu-id="d897f-201">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d897f-202">sendfile.SendAsync</span></span> | <span data-ttu-id="d897f-203">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-203">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="d897f-204">Pro Anforderung</span><span class="sxs-lookup"><span data-stu-id="d897f-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="d897f-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d897f-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="d897f-206">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-206">Key</span></span>               | <span data-ttu-id="d897f-207">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-207">Value (type)</span></span> | <span data-ttu-id="d897f-208">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="d897f-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="d897f-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="d897f-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="d897f-211">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-211">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d897f-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="d897f-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="d897f-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d897f-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="d897f-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d897f-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="d897f-215">Key</span><span class="sxs-lookup"><span data-stu-id="d897f-215">Key</span></span>               | <span data-ttu-id="d897f-216">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="d897f-216">Value (type)</span></span> | <span data-ttu-id="d897f-217">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d897f-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d897f-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="d897f-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="d897f-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="d897f-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="d897f-220">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-220">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d897f-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="d897f-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="d897f-222">n/v</span><span class="sxs-lookup"><span data-stu-id="d897f-222">Non-spec</span></span> |
| <span data-ttu-id="d897f-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="d897f-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="d897f-224">Siehe [RFC6455 Abschnitt 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2), Schritt 5.5</span><span class="sxs-lookup"><span data-stu-id="d897f-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="d897f-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d897f-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="d897f-226">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-226">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d897f-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="d897f-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="d897f-228">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-228">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d897f-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="d897f-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="d897f-230">Siehe [Delegatsignatur](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d897f-230">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d897f-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d897f-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d897f-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="d897f-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="d897f-233">Optional</span><span class="sxs-lookup"><span data-stu-id="d897f-233">Optional</span></span> |
| <span data-ttu-id="d897f-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="d897f-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="d897f-235">Optional</span><span class="sxs-lookup"><span data-stu-id="d897f-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d897f-236">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d897f-236">Additional resources</span></span>

* [<span data-ttu-id="d897f-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="d897f-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d897f-238">Server</span><span class="sxs-lookup"><span data-stu-id="d897f-238">Servers</span></span>](xref:fundamentals/servers/index)
