---
title: Open Web Interface for .NET (OWIN) mit ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core die Open Web Interface for .NET (OWIN) unterstützt, die das Entkoppeln von Web-Apps von Webservern ermöglicht.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 7edb4db026f1b778d43ac72883690a0b2a18ee31
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814898"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Open Web Interface for .NET (OWIN) mit ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core unterstützt Open Web Interface for .NET (OWIN). Mit OWIN können Web-Apps von Webservern entkoppelt werden. OWIN legt eine Standardmöglichkeit des Einsatzes von Middleware in einer Pipeline zum Verarbeiten von Anforderungen und den entsprechenden Antworten fest. ASP.NET Core-Anwendungen und -Middleware können mit auf OWIN basierten Anwendungen, Servern und OWIN basierter Middleware zusammenarbeiten.

OWIN bietet eine Entkopplungsebene, die das gemeinsame Verwenden zweier Frameworks mit unterschiedlichen Objektmodellen zulässt. Das `Microsoft.AspNetCore.Owin`-Paket bietet zwei Adapterimplementierungen:

* ASP.NET Core auf OWIN 
* OWIN auf ASP.NET Core

So kann ASP.NET Core auf einem mit OWIN kompatiblen Server bzw. Host gehostet werden. Alternativ können andere mit OWIN kompatible Komponenten über ASP.NET Core ausgeführt werden.

> [!NOTE]
> Das Verwenden dieser Adapter führt zu Leistungseinbußen. Apps, die nur ASP.NET Core-Komponenten verwenden, sollten keine `Microsoft.AspNetCore.Owin`-Pakete oder Adapter verwenden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Ausführen von OWIN-Middleware in der ASP.NET Core-Pipeline

Die OWIN-Unterstützung von ASP.NET Core wird im Rahmen des `Microsoft.AspNetCore.Owin`-Pakets bereitgestellt. Sie können OWIN-Unterstützung in Ihrem Projekt ermöglichen, indem Sie dieses Paket installieren.

OWIN-Middleware stimmt mit der [OWIN-Spezifikation](https://owin.org/spec/spec/owin-1.0.0.html) überein, die eine `Func<IDictionary<string, object>, Task>`-Schnittstelle und das Festlegen spezifischer Schlüssel erfordert (wie z.B. `owin.ResponseBody`). Die folgende einfache OWIN-Middleware zeigt „Hello World“ (Hallo Welt) an:

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

Die Beispielsignatur gibt ein `Task`-Objekt zurück und akzeptiert ein `IDictionary<string, object>`, wie OWIN es erfordert.

Der folgende Code veranschaulicht, wie Sie die `OwinHello`-Middleware (siehe oben) der ASP.NET Core-Pipeline mit der `UseOwin`-Erweiterungsmethode hinzufügen.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Sie können andere Aktionen konfigurieren, die in der OWIN-Pipeline durchgeführt werden sollen.

> [!NOTE]
> Antwortheader sollten nur vor dem ersten Schreiben in den Antwortstream modifiziert werden.

> [!NOTE]
> Mehrere Aufrufe von `UseOwin` werden aus Leistungsgründen nicht empfohlen. OWIN-Komponenten funktionieren am besten, wenn sie gruppiert werden.

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>ASP.NET Core-Hosting auf einem auf OWIN basierten Server

Auf OWIN basierte Server können ASP.NET Core-Anwendungen hosten. Ein derartiger Server ist [Nowin](https://github.com/Bobris/Nowin), ein Webserver von .NET OWIN. Das Beispiel für diesen Artikel enthält ein Projekt, das auf Nowin verweist und ihn verwendet, um einen `IServer` zu erstellen, der ASP.NET Core eigenständig hosten kann.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer` ist eine Schnittstelle, die eine `Features`-Eigenschaft und eine `Start`-Methode erfordert.

`Start` ist für die Konfiguration und das Starten des Server zuständig. In diesem Fall erfolgt dies durch eine Folge von Aufrufen von Fluent-APIs, die Adressen festgelegt haben, die von IServerAddressesFeature analysiert wurden. Beachten Sie, dass die fließende Konfiguration der `_builder`-Variablen angibt, das Anforderungen vom `appFunc`-Objekt verarbeitet werden, das bereits in der Methode festgelegt wurde. Dieses `Func`-Objekt wird bei jeder Anforderung aufgerufen, um eingehende Anforderungen zu verarbeiten.

Außerdem fügen wir eine `IWebHostBuilder`-Erweiterung hinzu, um den Nowin-Server leicht hinzufügen und konfigurieren zu können.

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

Rufen Sie anschließend die Erweiterung in *Program.cs* auf, um eine ASP.NET Core-App auszuführen, die diesen benutzerdefinierten Server verwendet:

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

Weitere Informationen zu [ASP.NET Core-Servern](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Ausführen von ASP.NET Core auf einem auf OWIN basierten Server und Nutzen der WebSockets-Unterstützung

Der Zugriff auf Features wie WebSockets ist ein weiteres Beispiel dafür, wie ASP.NET Core Features von auf OWIN basierten Servern nutzen kann. Der .NET OWIN-Webserver, der im vorherigen Beispiel verwendet wurde, verfügt über integrierte Unterstützung für WebSockets. Dies kann von einer ASP.NET Core-Anwendung genutzt werden. Das unten stehende Beispiel zeigt eine einfache Web-App, die WebSockets unterstützt und alles zurück gibt, was über WebSockets an den Server gesendet wurde.

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

Dieses [Beispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) wird mit dem gleichen `NowinServer` wie vorher konfiguriert. Der einzige Unterschied besteht in der Konfiguration der Anwendung in deren `Configure`-Methode. Ein Text mit einem [einfachen WebSocket-Client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) veranschaulicht die Anwendung:

![WebSocket-Testclient](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>OWIN-Umgebung

Sie können mit `HttpContext` eine OWIN-Umgebung erstellen.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN-Schlüssel

OWIN ist von einem `IDictionary<string,object>`-Objekt abhängig, um Informationen innerhalb eines Austauschs einer HTTP-Anforderung und einer Antwort weiterzugeben. ASP.NET Core implementiert die unten aufgelisteten Schlüssel. Weitere Informationen finden Sie in den [Hauptspezifikationen und Erweiterungen](https://owin.org/#spec) und in den [OWIN Key Guidelines and Common Keys (Wichtigste Richtlinien und gängige Schlüsse von OWIN)](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Anforderungsdaten (OWIN v1.0.0)

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Anforderungsdaten (OWIN v1.1.0)

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | Optional |

### <a name="response-data-owin-v100"></a>Antwortdaten (OWIN v1.0.0)

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | Optional |
| owin.ResponseReasonPhrase | `String` | Optional |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Andere Daten (OWIN v1.0.0)

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>Gängige Schlüssel

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Pro Anforderung |

### <a name="opaque-v030"></a>Opaque v0.3.0

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Key               | Wert (Typ) | BESCHREIBUNG |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | n/v |
| websocket.SubProtocol | `String` | Siehe [RFC6455 Abschnitt 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2), Schritt 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | Optional |
| websocket.ClientCloseDescription | `String` | Optional |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Server](xref:fundamentals/servers/index)
