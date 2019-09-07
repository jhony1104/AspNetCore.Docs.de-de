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
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Unterschiede zwischen ASP.net signalr und ASP.net Core signalr

ASP.net Core signalr ist nicht mit Clients oder Servern für ASP.net signalr kompatibel. In diesem Artikel werden die Features erläutert, die in ASP.net Core signalr entfernt oder geändert wurden.

## <a name="how-to-identify-the-signalr-version"></a>Identifizieren der signalr-Version

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Server-nuget-Paket | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. aspnetcore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[Microsoft. aspnetcore. signalr](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Client-nuget-Pakete | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| NPM-Client Paket | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java-Client | [GitHub-Repository](https://github.com/SignalR/java-client) veraltet  | Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Server-App-Typ | ASP.net (System. Web) oder owin-Self-Host | ASP.NET Core |
| Unterstützte Server Plattformen | .NET Framework 4,5 oder höher | .NET Framework 4.6.1 oder höher<br>.Net Core 2,1 oder höher |

## <a name="feature-differences"></a>Funktions Unterschiede

### <a name="automatic-reconnects"></a>Automatische erneute Verbindungen

Automatische erneute Verbindungen werden in ASP.net Core signalr nicht unterstützt. Wenn der Client getrennt wird, muss der Benutzer explizit eine neue Verbindung starten, wenn er erneut eine Verbindung herstellen möchte. In ASP.net signalr versucht signalr, erneut eine Verbindung mit dem Server herzustellen, wenn die Verbindung getrennt wird.

### <a name="protocol-support"></a>Protokoll Unterstützung

ASP.net Core signalr unterstützt JSON und ein neues binäres Protokoll, das auf [messagepack](xref:signalr/messagepackhubprotocol)basiert. Außerdem können benutzerdefinierte Protokolle erstellt werden.

### <a name="transports"></a>Transportprotokolle

Der unveränderte Frame-Transport wird in ASP.net Core signalr nicht unterstützt.

## <a name="differences-on-the-server"></a>Unterschiede auf dem Server

Die serverseitigen ASP.net Core signalr-Bibliotheken sind im [metapaketpaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) " enthalten, das Teil der **ASP.net Core Webanwendungs** Vorlage für Razor-und MVC-Projekte ist.

ASP.net Core signalr eine ASP.net Core Middleware ist, muss Sie durch Aufrufen von [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`konfiguriert werden.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) -Methoden `Startup.Configure` Aufrufes in der-Methode zu.


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des [usesignalr](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) -Methoden `Startup.Configure` Aufrufes in der-Methode zu.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Persistente Sitzungen

Mit dem Modell für horizontales Skalieren für ASP.net signalr können Clients die Verbindung wiederherstellen und Nachrichten an einen beliebigen Server in der Farm senden. In ASP.net Core signalr muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren. Für horizontales Skalieren mit redis bedeutet das, dass persistente Sitzungen erforderlich sind. Für horizontales Skalieren mit dem [Azure signalr-Dienst](/azure/azure-signalr/)sind keine persistenten Sitzungen erforderlich, da der Dienst Verbindungen mit Clients verarbeitet.

### <a name="single-hub-per-connection"></a>Einzelner Hub pro Verbindung

In ASP.net Core signalr wurde das Verbindungs Modell vereinfacht. Verbindungen werden direkt mit einem einzelnen Hub hergestellt, anstatt mit einer einzelnen Verbindung, die für die gemeinsame Nutzung des Zugriffs auf mehrere Hubs verwendet wird.

### <a name="streaming"></a>Streaming

ASP.net Core signalr unterstützt jetzt das [Streamen von Daten](xref:signalr/streaming) vom Hub an den Client.

### <a name="state"></a>Status

Die Möglichkeit, den beliebigen Zustand zwischen Clients und dem Hub (häufig "hubstate" genannt) zu übergeben, wurde entfernt sowie Unterstützung für Fortschrittsmeldungen. Zurzeit gibt es keine Entsprechung von Hub-Proxys.

### <a name="persistentconnection-removal"></a>Entfernen von persistentconnection

In ASP.net Core signalr wurde die [persistentconnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) -Klasse entfernt.

### <a name="globalhost"></a>GlobalHost

ASP.net Core ist eine Abhängigkeitsinjektion (di) in das Framework integriert. Dienste können mit di auf den [hubcontext](xref:signalr/hubcontext)zugreifen. Das `GlobalHost` -Objekt, das in ASP.net signalr verwendet wird, `HubContext` um ein-Objekt zu erhalten, ist nicht in ASP.net Core signalr vorhanden.

### <a name="hubpipeline"></a>Hubpipeline

ASP.net Core signalr bietet keine Unterstützung `HubPipeline` für Module.

## <a name="differences-on-the-client"></a>Unterschiede auf dem Client

### <a name="typescript"></a>TypeScript

Der ASP.net Core signalr-Client wird in [typescript](https://www.typescriptlang.org/)geschrieben. Sie können in JavaScript oder typescript schreiben, wenn Sie den [JavaScript-Client](xref:signalr/javascript-client)verwenden.

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>Der JavaScript-Client wird auf [NPM](https://www.npmjs.com/) gehostet.

In früheren Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen. Bei den Kern Versionen enthält das [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) NPM-Paket die JavaScript-Bibliotheken. Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten. Verwenden Sie NPM, um das `@aspnet/signalr` NPM-Paket abzurufen und zu installieren.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

Die Abhängigkeit von jQuery wurde entfernt, aber Projekte können weiterhin jQuery verwenden.

### <a name="internet-explorer-support"></a>Internet Explorer-Unterstützung

ASP.net Core signalr erfordert Microsoft Internet Explorer 11 oder höher (ASP.net signalr supported Microsoft Internet Explorer 8 und höher).

### <a name="javascript-client-method-syntax"></a>Syntax der JavaScript-Client Methode

Die JavaScript-Syntax wurde von der vorherigen Version von signalr geändert. Anstatt das `$connection` -Objekt zu verwenden, erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) -API eine Verbindung.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Verwenden Sie die [on](/javascript/api/@aspnet/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

Nachdem Sie die Client Methode erstellt haben, starten Sie die Hub-Verbindung. Verketten einer [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) -Methode zum Protokollieren oder behandeln von Fehlern.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>Knotenproxys

Hub-Proxys werden nicht mehr automatisch generiert. Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/%40aspnet/signalr/hubconnection#invoke) -API übermittelt.

### <a name="net-and-other-clients"></a>.Net und andere Clients

Das `Microsoft.AspNetCore.SignalR.Client` nuget-Paket enthält die .NET-Client Bibliotheken für ASP.net Core signalr.

Verwenden Sie [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) , um eine Instanz einer Verbindung mit einem Hub zu erstellen und zu erstellen.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Unterschiede bei horizontaler Skalierung

ASP.net signalr unterstützt SQL Server und redis. ASP.net Core signalr unterstützt den Azure signalr-Dienst und redis.

### <a name="aspnet"></a>ASP.NET

* [Horizontale Skalierung von signalr mit Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Horizontale Skalierung von signalr mit redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [Horizontale Skalierung von signalr mit SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR-Dienst](/azure/azure-signalr/)
* [Redis-Backplane](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [.NET-Client](xref:signalr/dotnet-client)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
