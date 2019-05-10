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
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Unterschiede zwischen SignalR für ASP.NET und ASP.NET Core SignalR

ASP.NET Core SignalR nicht kompatibel mit Clients oder Servern für ASP.NET SignalR. In diesem Artikel werden die Funktionen, die in ASP.NET Core SignalR geändert oder entfernt wurden.

## <a name="how-to-identify-the-signalr-version"></a>So identifizieren Sie die SignalR-version

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Server-NuGet-Paket | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Die NuGet-Pakete | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Client-Npm-Pakets | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java-Client | [GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)  | Maven-Paket [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Server App-Typ | ASP.NET ("System.Web") oder Selbstgehostetem | ASP.NET Core |
| Unterstützte Plattformen | .NET Framework 4.5 oder höher | .NET Framework 4.6.1 oder höher<br>.NET Core 2.1 oder höher |

## <a name="feature-differences"></a>Funktionsunterschiede

### <a name="automatic-reconnects"></a>Automatische Verbindungen

Automatische Verbindungen werden in ASP.NET Core SignalR nicht unterstützt. Wenn der Client getrennt wird, muss der Benutzer eine neue Verbindung explizit starten, wenn sie erneut eine Verbindung herstellen möchten. SignalR versucht, in ASP.NET SignalR zum Server hergestellt werden soll, wenn die Verbindung getrennt wird. 

### <a name="protocol-support"></a>Unterstützung des Protokolls

SignalR für ASP.NET Core unterstützt JSON als auch ein neues binäres Protokoll, die basierend auf [MessagePack](xref:signalr/messagepackhubprotocol). Darüber hinaus können benutzerdefinierte Protokolle erstellt werden.

### <a name="transports"></a>Transportprotokolle

Der Forever Frame-Transport wird in ASP.NET Core SignalR nicht unterstützt.

## <a name="differences-on-the-server"></a>Unterschiede, auf dem server

Die ASP.NET Core SignalR serverseitige Bibliotheken befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) Paket, das Teil der **ASP.NET Core-Webanwendung** Vorlage für Razor und MVC Projekte.

ASP.NET Core SignalR ist eine ASP.NET Core-Middleware, sodass es durch Aufrufen von konfiguriert werden muss [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

Zum Konfigurieren des Routings, ordnen Sie Routen für Hubs innerhalb der [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) -Methodenaufruf in der `Startup.Configure` Methode.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a>Persistente Sitzungen

Das Modell mit horizontaler Skalierung für ASP.NET SignalR kann Clients erneut eine Verbindung herzustellen, und Senden von Nachrichten an einem beliebigen Server in der Farm. In ASP.NET Core SignalR muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren. Für die horizontale Skalierung mit Redis bedeutet dies, dass es sich bei persistente Sitzungen erforderlich sind. Für die Verwendung der Sendewarteschlange für horizontale Skalierung [Azure SignalR Service](/azure/azure-signalr/), persistente Sitzungen sind nicht erforderlich, da der Dienst übernimmt die Verbindungen mit Clients. 

### <a name="single-hub-per-connection"></a>Ein Hub pro Verbindung

In ASP.NET Core SignalR wurde das Verbindungsmodell vereinfacht. Verbindungen werden direkt an eine einzelne Verbindung, die Zugriff auf mehrere Hubs freigeben verwendet wird, anstatt einen einzelnen Hub hergestellt werden.

### <a name="streaming"></a>Streaming

Unterstützt nun das ASP.NET Core SignalR [Streamingdaten](xref:signalr/streaming) aus dem Hub an den Client.

### <a name="state"></a>Zustand

Die Möglichkeit, die oft ausgegebene Befehlszeilen Zustand zwischen Clients und dem Hub (oft als HubState bezeichnet) übergeben wurde, sowie Unterstützung für Meldungen zum Druckfortschritt aufgehoben. Es gibt keine Entsprechung der Hub-Proxys im Moment ein.

### <a name="persistentconnection-removal"></a>PersistentConnection entfernen

In ASP.NET Core SignalR die [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) Klasse wurde entfernt. 

### <a name="globalhost"></a>GlobalHost

ASP.NET Core verfügt über Abhängigkeitsinjektion (Dependency Injection), die im Framework integriert. Dienste können DI verwenden, um Zugriff auf die [HubContext](xref:signalr/hubcontext). Die `GlobalHost` -Objekt, das in ASP.NET SignalR, zum Abrufen verwendet wird einer `HubContext` in ASP.NET Core SignalR nicht vorhanden ist.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR keine Unterstützung für `HubPipeline` Module.

## <a name="differences-on-the-client"></a>Unterschiede, auf dem client

### <a name="typescript"></a>TypeScript

Der ASP.NET Core SignalR-Client hat [TypeScript](https://www.typescriptlang.org/). Sie können in JavaScript oder TypeScript schreiben, bei Verwendung der [JavaScript-Client](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>Der JavaScript-Client befindet sich am [Npm](https://www.npmjs.com/)

In früheren Versionen wurde der JavaScript-Client über NuGet-Paket in Visual Studio abgerufen. Für die Core-Versionen die [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) Npm-Paket enthält die JavaScript-Bibliotheken. Dieses Paket nicht enthalten, der **ASP.NET Core-Webanwendung** Vorlage. Mithilfe von Npm abrufen und Installieren der `@aspnet/signalr` Npm-Paket.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

Die Abhängigkeit von jQuery wurde entfernt, aber Projekte jQuery weiterhin verwenden können.

### <a name="internet-explorer-support"></a>Internet Explorer-support

ASP.NET Core SignalR erfordert Microsoft InternetExplorer 11 oder höher (ASP.NET SignalR unterstützt Microsoft InternetExplorer 8 und höher).

### <a name="javascript-client-method-syntax"></a>JavaScript-Client-Methodensyntax

Die JavaScript-Syntax wurde aus der vorherigen Version von SignalR geändert. Anstatt die `$connection` Objekt, erstellen Sie eine Verbindung mit der [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Verwenden der [auf](/javascript/api/@aspnet/signalr/HubConnection#on) Methode, um die Clientmethoden angeben, die der Hub aufgerufen werden kann.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

Starten Sie nach dem Erstellen der-Methode, die Hub-Verbindung ein. Kette eine [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) Methode zum Anmelden oder Fehler zu beheben.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>Hub-Proxys

Hub-Proxys werden nicht mehr automatisch generiert. Stattdessen wird der Name der Methode übergeben, in der [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API als Zeichenfolge.

### <a name="net-and-other-clients"></a>.NET und andere clients

Die `Microsoft.AspNetCore.SignalR.Client` NuGet-Paket enthält die .NET Client-Bibliotheken für ASP.NET Core SignalR.

Verwenden der [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) erstellen und entwickeln eine Instanz einer Verbindung mit einem Hub.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Horizontale Skalierung Unterschiede

ASP.NET SignalR unterstützt SQL Server und Redis. ASP.NET Core SignalR unterstützt Azure SignalR Service und Redis.

### <a name="aspnet"></a>ASP.NET

* [Horizontale Skalierung in SignalR mit Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Horizontale Skalierung in SignalR mit Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [Horizontale Skalierung in SignalR mit SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR-Dienst](/azure/azure-signalr/)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [.NET-Client](xref:signalr/dotnet-client)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
