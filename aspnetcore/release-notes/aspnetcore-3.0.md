---
title: Neuerungen in ASP.NET Core 3.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: aspnetcore-3.0
ms.openlocfilehash: 90433773bec2efc5a2bc39d71ce7ae324b922046
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165356"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="5e3f2-103">Neuerungen in ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="5e3f2-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="5e3f2-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## <a name="blazor"></a><span data-ttu-id="5e3f2-105">Blazor</span><span class="sxs-lookup"><span data-stu-id="5e3f2-105">Blazor</span></span>

<span data-ttu-id="5e3f2-106">Blazor ist ein Framework in ASP.NET Core zum Erstellen von interaktiven clientseitigen Webbenutzeroberflächen mit .NET:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-106">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="5e3f2-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="5e3f2-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="5e3f2-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="5e3f2-110">Vom Blazor-Framework unterstützte Szenarien:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-110">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="5e3f2-111">Wiederverwendbare Benutzeroberflächenkomponenten (Razor-Komponenten)</span><span class="sxs-lookup"><span data-stu-id="5e3f2-111">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="5e3f2-112">Clientseitiges Routing</span><span class="sxs-lookup"><span data-stu-id="5e3f2-112">Client-side routing</span></span>
* <span data-ttu-id="5e3f2-113">Komponentenlayouts</span><span class="sxs-lookup"><span data-stu-id="5e3f2-113">Component layouts</span></span>
* <span data-ttu-id="5e3f2-114">Unterstützung für Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="5e3f2-114">Support for dependency injection</span></span>
* <span data-ttu-id="5e3f2-115">Formulare und Überprüfung</span><span class="sxs-lookup"><span data-stu-id="5e3f2-115">Forms and validation</span></span>
* <span data-ttu-id="5e3f2-116">Erstellen von Komponentenbibliotheken mit Razor-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="5e3f2-116">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="5e3f2-117">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="5e3f2-117">JavaScript interop</span></span>

<span data-ttu-id="5e3f2-118">Weitere Informationen finden Sie unter <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-118">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a><span data-ttu-id="5e3f2-119">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="5e3f2-119">Blazor Server</span></span>

<span data-ttu-id="5e3f2-120">Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-120">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="5e3f2-121">Blazor Server bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-121">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="5e3f2-122">Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-122">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="5e3f2-123">Blazor Server wird in ASP.NET Core 3.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-123">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="5e3f2-124">Blazor WebAssembly (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="5e3f2-124">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="5e3f2-125">Blazor-Apps können auch mit einer WebAssembly-basierten .NET-Runtime direkt im Browser ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-125">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="5e3f2-126">Blazor WebAssembly befindet sich in der Vorschau und wird in ASP.NET Core 3.0 *nicht* unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-126">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5e3f2-127">Blazor WebAssembly wird in einer zukünftigen Version von ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-127">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="5e3f2-128">Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="5e3f2-128">Razor components</span></span>

<span data-ttu-id="5e3f2-129">Blazor-Apps sind auf Komponenten aufgebaut.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-129">Blazor apps are built from components.</span></span> <span data-ttu-id="5e3f2-130">Komponenten sind eigenständige Elemente einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-130">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="5e3f2-131">Komponenten sind normale .NET-Klassen, die die Renderinglogik für die Benutzeroberfläche sowie clientseitige Ereignishandler definieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-131">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="5e3f2-132">Damit können Sie umfangreiche interaktive Web-Apps ohne JavaScript erstellen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-132">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="5e3f2-133">Komponenten in Razor werden üblicherweise mit der Razor-Syntax erstellt, einer natürlichen Mischung aus HTML und C#.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-133">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="5e3f2-134">Razor-Komponenten ähneln Razor Pages und MVC-Ansichten dahingehend, dass sie ebenfalls Razor verwenden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-134">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="5e3f2-135">Im Gegensatz zu Pages und Ansichten, die auf einem Anforderung/Antwort-Modell basieren, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-135">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="5e3f2-136">gRPC</span><span class="sxs-lookup"><span data-stu-id="5e3f2-136">gRPC</span></span>

<span data-ttu-id="5e3f2-137">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="5e3f2-137">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="5e3f2-138">Ein beliebtes, hochleistungsfähiges RPC-Framework (Remote Procedure Call, Remoteprozeduraufruf).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-138">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="5e3f2-139">Bietet einen strikten Contract First-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-139">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="5e3f2-140">Verwendet moderne Technologien wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-140">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="5e3f2-141">HTTP/2 für den Transport.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-141">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="5e3f2-142">Protokollpuffer als Sprache zur Schnittstellenbeschreibung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-142">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="5e3f2-143">Format der binären Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-143">Binary serialization format.</span></span>
* <span data-ttu-id="5e3f2-144">Bietet beispielsweise folgende Features:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-144">Provides features such as:</span></span>

  * <span data-ttu-id="5e3f2-145">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e3f2-145">Authentication</span></span>
  * <span data-ttu-id="5e3f2-146">Bidirektionales Streaming und Flusssteuerung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-146">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="5e3f2-147">Stornierung und Timeouts.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-147">Cancellation and timeouts.</span></span>

<span data-ttu-id="5e3f2-148">Die gRPC-Funktionalität in ASP.NET Core 3.0 umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-148">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="5e3f2-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ein ASP.NET Core-Framework zum Hosten von gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="5e3f2-150">gRPC in ASP.NET Core lässt sich in ASP.NET Core-Standardfeatures wie Protokollierung, Abhängigkeitsinjektion (Dependency Injection, DI), Authentifizierung und Autorisierung integrieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-150">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication and authorization.</span></span>
* <span data-ttu-id="5e3f2-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; ein gRPC-Client für .NET Core, der auf dem vertrauten `HttpClient` aufbaut.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="5e3f2-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC-Clientintegration mit `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="5e3f2-153">Weitere Informationen finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-153">For more information, see <xref:grpc/index>.</span></span>

## <a name="signalr"></a><span data-ttu-id="5e3f2-154">SignalR</span><span class="sxs-lookup"><span data-stu-id="5e3f2-154">SignalR</span></span>

<span data-ttu-id="5e3f2-155">Anleitungen zur Migration finden Sie unter [Aktualisieren von SignalR-Code](xref:migration/22-to-30#signalr).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-155">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="5e3f2-156">SignalR verwendet jetzt `System.Text.Json`, um JSON-Nachrichten zu serialisieren bzw. zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-156">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="5e3f2-157">Anleitungen zum Wiederherstellen des `Newtonsoft.Json`-basierten Serialisierungsmoduls finden Sie unter [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) (Wechseln zu Newtonsoft.Json).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-157">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="5e3f2-158">In den JavaScript- und .NET-Clients für SignalR wurde Unterstützung für eine automatische erneute Verbindungsherstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-158">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="5e3f2-159">Standardmäßig versucht der Client sofort, die Verbindung wiederherzustellen, und wiederholt den Versuch nötigenfalls nach 2, 10 und 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-159">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="5e3f2-160">Wenn der Client die Verbindung erfolgreich wiederherstellt, erhält er eine neue Verbindungs-ID.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-160">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="5e3f2-161">Die automatische erneute Herstellung einer Verbindung kann optional aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-161">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="5e3f2-162">Die Intervalle für die Wiederherstellung einer Verbindung können durch Übergabe eines Arrays aus millisekundenbasierten Zeitspannen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-162">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="5e3f2-163">Zur vollständigen Steuerung der Wiederherstellungsintervalle kann eine benutzerdefinierte Implementierung übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-163">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="5e3f2-164">Wenn die Verbindung nicht wiederhergestellt werden kann, passiert nach dem letzten Intervall Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-164">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="5e3f2-165">Der Client betrachtet die Verbindung als offline.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-165">The client considers the connection is offline.</span></span>
* <span data-ttu-id="5e3f2-166">Der Client versucht nicht mehr, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-166">The client stops trying to reconnect.</span></span>

<span data-ttu-id="5e3f2-167">Aktualisieren Sie die App-Benutzeroberfläche während der Verbindungsversuche, um den Benutzer darüber zu informieren, dass versucht wird, erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-167">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="5e3f2-168">Um bei einer unterbrochenen Verbindung Feedback auf der Benutzeroberfläche bereitstellen zu können, wurde die SignalR-Client-API erweitert und umfasst jetzt die folgenden Ereignishandler:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-168">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="5e3f2-169">`onreconnecting`:  Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu deaktivieren oder Benutzer darüber zu informieren, dass die App offline ist.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-169">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="5e3f2-170">`onreconnected`: Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu aktualisieren, sobald die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-170">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="5e3f2-171">Der folgende Code verwendet `onreconnecting`, um die Benutzeroberfläche während der Verbindungsversuche zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-171">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="5e3f2-172">Der folgende Code verwendet `onreconnected`, um die Benutzeroberfläche nach erfolgreich hergestellter Verbindung zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-172">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="5e3f2-173">SignalR 3.0 und höher bietet eine benutzerdefinierte Ressource für Autorisierungshandler, wenn eine Hubmethode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-173">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="5e3f2-174">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-174">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="5e3f2-175">Der `HubInvocationContext` umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-175">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="5e3f2-176">Den Namen der aufgerufenen Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-176">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="5e3f2-177">Argumente für die Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-177">Arguments to the hub method.</span></span>

<span data-ttu-id="5e3f2-178">Sehen Sie sich das folgende Beispiel einer Chatroom-App an, die mehreren Mitgliedern einer Organisation die Anmeldung über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-178">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="5e3f2-179">Jede Person mit einem Microsoft-Konto kann sich beim Chat anmelden, aber nur Mitglieder der besitzenden Organisation können Benutzer sperren oder den Chatverlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users’ chat histories.</span></span> <span data-ttu-id="5e3f2-180">Die App könnte bestimmte Funktionen für bestimmte Benutzer einschränken.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-180">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="5e3f2-181">Im vorherigen Code dient `DomainRestrictedRequirement` als benutzerdefinierte `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-181">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="5e3f2-182">Da der Ressourcenparameter `HubInvocationContext` übergeben wird, kann die interne Logik folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-182">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="5e3f2-183">Sie kann den Kontext untersuchen, in dem der Hub aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-183">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="5e3f2-184">Sie kann Entscheidungen darüber treffen, ob einem Benutzer die Ausführung einzelner Hubmethoden gestattet wird.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-184">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="5e3f2-185">Einzelne Hubmethoden können durch den Namen der Richtlinie ergänzt werden, die der Code zur Laufzeit prüft.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-185">Individual Hub methods can be decorated with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="5e3f2-186">Wenn Clients versuchen, einzelne Hubmethoden aufzurufen, wird der `DomainRestrictedRequirement`-Handler ausgeführt und steuert den Zugriff auf die Methoden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-186">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="5e3f2-187">Basierend auf der Art und Weise, in der `DomainRestrictedRequirement` den Zugriff steuert, kann Folgendes passieren:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-187">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="5e3f2-188">Alle angemeldeten Benutzer können die `SendMessage`-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-188">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="5e3f2-189">Nur Benutzer, die sich mit einer `@jabbr.net`-E-Mail-Adresse angemeldet haben, können den Verlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-189">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="5e3f2-190">Nur `bob42@jabbr.net` kann Benutzer aus dem Chatroom ausschließen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-190">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="5e3f2-191">Die Erstellung der `DomainRestricted`-Richtlinie kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-191">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="5e3f2-192">Fügen Sie die neue Richtlinie in *Startup.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-192">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="5e3f2-193">Stellen Sie die benutzerdefinierte Anforderung `DomainRestrictedRequirement` als Parameter bereit.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-193">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="5e3f2-194">Registrieren Sie `DomainRestricted` bei der Autorisierungsmiddleware.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-194">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="5e3f2-195">SignalR-Hubs verwenden [Endpunktrouting](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-195">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="5e3f2-196">Eine SignalR-Hubverbindung wurde zuvor explizit eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-196">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="5e3f2-197">In der vorherigen Version mussten Entwickler Controller, Razor-Seiten und Hubs an den unterschiedlichsten Orten miteinander verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-197">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of different places.</span></span> <span data-ttu-id="5e3f2-198">Explizite Verbindungen führen zu einer Reihe nahezu identischer Routingsegmente:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-198">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="5e3f2-199">SignalR 3.0-Hubs können per Endpunktrouting weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-199">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="5e3f2-200">Beim Endpunktrouting kann in der Regel das gesamte Routing in `UseRouting` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-200">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="5e3f2-201">In ASP.NET Core 3.0 durch SignalR hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-201">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="5e3f2-202">Streaming vom Client zum Server.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-202">Client-to-server streaming.</span></span> <span data-ttu-id="5e3f2-203">Mit dem Streaming vom Client zum Server können serverseitige Methoden Instanzen von `IAsyncEnumerable<T>` oder `ChannelReader<T>` akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-203">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="5e3f2-204">Im folgenden C#-Beispiel empfängt die `UploadStream`-Methode im Hub einen Zeichenfolgenstream vom Client:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-204">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="5e3f2-205">.NET-Client-Apps können entweder eine `IAsyncEnumerable<T>`-oder eine `ChannelReader<T>`-Instanz als `stream`-Argument der oben genannten `UploadStream`-Hubmethode übergeben.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-205">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="5e3f2-206">Nachdem die `for`-Schleife abgeschlossen ist und die lokale Funktion existiert, wird der Streamabschluss gesendet:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-206">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="5e3f2-207">JavaScript-Client-Apps verwenden das SignalR-`Subject` (oder ein [RxJS-Subject](https://rxjs.dev/api/index/class/Subject)) für das `stream`-Argument der oben genannten `UploadStream`-Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-207">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="5e3f2-208">Der JavaScript-Code könnte die `subject.next`-Methode verwenden, um Zeichenfolgen zu verarbeiten, sobald diese erfasst werden und zum Senden an den Server bereit sin.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-208">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="5e3f2-209">Mit Code wie in den beiden obigen Codeausschnitten lassen sich Echtzeitstreamingfunktionen erzeugen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-209">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="5e3f2-210">Neue JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="5e3f2-210">New JSON serialization</span></span>

<span data-ttu-id="5e3f2-211">ASP.NET Core 3.0 verwendet jetzt standardmäßig <xref:System.Text.Json> zur JSON-Serialisierung:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-211">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="5e3f2-212">Lese- und Schreibvorgänge in JSON erfolgen asynchron.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-212">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="5e3f2-213">Der Code ist für UTF-8-Text optimiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-213">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="5e3f2-214">In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-214">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="5e3f2-215">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-215">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="5e3f2-216">Neue Razor-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="5e3f2-216">New Razor directives</span></span>

<span data-ttu-id="5e3f2-217">Die folgende Liste enthält neue Razor-Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-217">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="5e3f2-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; die `@attribute`-Anweisung wendet das angegebene Attribut auf die Klasse der generierten Seite oder Ansicht an.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="5e3f2-219">Beispielsweise `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-219">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="5e3f2-220">[@implements](xref:mvc/views/razor#implements) &ndash; die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-220">[@implements](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="5e3f2-221">Beispielsweise `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-221">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="5e3f2-222">IdentityServer4 unterstützt die Authentifizierung und Autorisierung für Web-APIs und SPAs.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-222">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="5e3f2-223">[IdentityServer4](https://identityserver.io) ist ein Framework für OpenID Connect und OAuth 2.0 für ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-223">[IdentityServer4](https://identityserver.io) is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="5e3f2-224">IdentityServer4 ermöglicht die folgenden Sicherheitsfeatures:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-224">IdentityServer4 enables the following security features:</span></span>

* <span data-ttu-id="5e3f2-225">Authentifizierung als Dienst</span><span class="sxs-lookup"><span data-stu-id="5e3f2-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="5e3f2-226">Einmaliges Anmelden und einmaliges Abmelden für mehrere Anwendungstypen</span><span class="sxs-lookup"><span data-stu-id="5e3f2-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="5e3f2-227">Zugriffssteuerung für APIs</span><span class="sxs-lookup"><span data-stu-id="5e3f2-227">Access control for APIs</span></span>
* <span data-ttu-id="5e3f2-228">Federation Gateway</span><span class="sxs-lookup"><span data-stu-id="5e3f2-228">Federation Gateway</span></span>

<span data-ttu-id="5e3f2-229">Weitere Informationen finden Sie unter [Willkommen bei IdentityServer4](http://docs.identityserver.io/en/latest/index.html).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-229">For more information, see [Welcome to IdentityServer4](http://docs.identityserver.io/en/latest/index.html).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="5e3f2-230">Zertifikat- und Kerberos-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e3f2-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="5e3f2-231">Die Zertifikatsauthentifizierung erfordert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="5e3f2-232">Der Server muss so konfiguriert werden, dass er Zertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="5e3f2-233">Die Authentifizierungsmiddleware muss in `Startup.Configure` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="5e3f2-234">Der Zertifikatauthentifizierungsdienst muss in `Startup.ConfigureServices` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="5e3f2-235">Die Optionen für die Zertifikatauthentifizierung umfassen folgende Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="5e3f2-236">Akzeptieren selbstsignierter Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="5e3f2-237">Überprüfen auf Zertifikatswiderruf.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="5e3f2-238">Überprüfen, ob das angebotene Zertifikat die richtigen Nutzungsflags enthält.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="5e3f2-239">Aus den Zertifikateigenschaften wird ein Standardbenutzerprinzipal erstellt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="5e3f2-240">Der Benutzerprinzipal enthält ein Ereignis, das das Ergänzen oder Ersetzen des Prinzipals ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="5e3f2-241">Weitere Informationen finden Sie unter <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="5e3f2-242">Die [Windows-Authentifizierung](/windows-server/security/windows-authentication/windows-authentication-overview) wurde auf Linux und macOS erweitert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="5e3f2-243">In vorherigen Versionen war die Windows-Authentifizierung auf [IIS](xref:host-and-deploy/iis/index) und [HttpSys](xref:fundamentals/servers/httpsys) beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="5e3f2-244">In ASP.NET Core 3.0 besitzt [Kestrel](xref:fundamentals/servers/kestrel) die Möglichkeit, Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) und [NTLM unter Windows](/windows-server/security/kerberos/ntlm-overview), Linux und macOS für Hosts zu verwenden, die der Windows-Domäne beigetreten sind.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="5e3f2-245">Die Kestrel-Unterstützung für diese Authentifizierungsschemas wird durch das Paket [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="5e3f2-246">Wie bei anderen Authentifizierungsdiensten auch sollte erst die Authentifizierung auf App-Ebene und dann der Dienst konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="5e3f2-247">Hostanforderungen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-247">Host requirements:</span></span>

* <span data-ttu-id="5e3f2-248">Windows-Hosts müssen dem Benutzerkonto, das die App hostet, [Dienstprinzipalnamen](/windows/win32/ad/service-principal-names) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="5e3f2-249">Linux- und macOS-Computer müssen der Domäne beigetreten sein.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="5e3f2-250">Für den Webprozess müssen Dienstprinzipalnamen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="5e3f2-251">[Keytab-Dateien](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) müssen generiert und auf dem Hostcomputer konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="5e3f2-252">Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="5e3f2-253">Änderungen an Vorlagen</span><span class="sxs-lookup"><span data-stu-id="5e3f2-253">Template changes</span></span>

<span data-ttu-id="5e3f2-254">Aus den Vorlagen für Webbenutzeroberflächen (Razor Pages, MVC mit Controller und Ansichten) wurde Folgendes entfernt:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="5e3f2-255">Die Benutzeroberfläche für die Zustimmung zu Cookies ist nicht mehr enthalten.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="5e3f2-256">Informationen dazu, wie Sie die Zustimmung zu Cookies in einer aus einer ASP.NET Core 3.0-Vorlage generierten App aktivieren, finden Sie unter <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="5e3f2-257">Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs mehr dafür verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="5e3f2-258">Weitere Informationen finden Sie unter [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350) (Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs basierend auf der aktuellen Umgebung mehr verwendet).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="5e3f2-259">Die Angular-Vorlage wurde auf Angular 8 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="5e3f2-260">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="5e3f2-261">Eine neue Vorlagenoption in Visual Studio bietet Vorlagenunterstützung für Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="5e3f2-262">Übergeben Sie beim Erstellen einer RCL aus einer Vorlage in einer Befehlsshell die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="5e3f2-263">Generischer Host</span><span class="sxs-lookup"><span data-stu-id="5e3f2-263">Generic Host</span></span>

<span data-ttu-id="5e3f2-264">Die ASP.NET Core 3.0-Vorlagen verwenden <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="5e3f2-265">In vorherigen Versionen wurde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="5e3f2-266">Durch Verwendung des generischen .NET Core-Hosts (<xref:Microsoft.Extensions.Hosting.HostBuilder>) lässt sich eine bessere Integration von ASP.NET Core-Apps in andere, nicht webspezifische Serverszenarien erzielen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that are not web specific.</span></span> <span data-ttu-id="5e3f2-267">Weitere Informationen finden Sie unter [HostBuilder ersetzt WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="5e3f2-268">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="5e3f2-268">Host configuration</span></span>

<span data-ttu-id="5e3f2-269">Vor ASP.NET Core 3.0 wurden Umgebungsvariablen mit dem Präfix `ASPNETCORE_` für die Hostkonfiguration des Webhosts geladen.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="5e3f2-270">In Version 3.0 wird `AddEnvironmentVariables` zum Laden von Umgebungsvariablen mit dem Präfix `DOTNET_` zur Hostkonfiguration mit `CreateDefaultBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-contructor-injection"></a><span data-ttu-id="5e3f2-271">Änderungen an der Startkonstruktorinjektion</span><span class="sxs-lookup"><span data-stu-id="5e3f2-271">Changes to Startup contructor injection</span></span>

<span data-ttu-id="5e3f2-272">Der generische Host unterstützt nur die folgenden Typen für die Injektion des `Startup`-Konstruktors:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="5e3f2-273">Alle Dienste können weiterhin direkt als Argumente in die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="5e3f2-274">Weitere Informationen finden Sie unter [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353) (Generischer Host schränkt Startkonstruktorinjektion ein).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="5e3f2-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e3f2-275">Kestrel</span></span>

* <span data-ttu-id="5e3f2-276">Die Kestrel-Konfiguration wurde für die Migration zum generischen Host aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="5e3f2-277">In Version 3.0 ist Kestrel im Webhostgenerator konfiguriert, der durch `ConfigureWebHostDefaults` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="5e3f2-278">Verbindungsadapter wurden aus Kestrel entfernt und durch Verbindungsmiddleware ersetzt. Diese ähnelt der HTTP-Middleware in der ASP.NET Core-Pipeline, ist aber für Verbindungen auf niedrigerer Ebene konzipiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="5e3f2-279">Die Kestrel-Transportschicht wurde in `Connections.Abstractions` als öffentliche Schnittstelle verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="5e3f2-280">Eine Mehrdeutigkeit zwischen Headern und Trailern wurde aufgelöst, indem nachstehende Header in eine neue Sammlung verschoben wurden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="5e3f2-281">APIs mit synchronen E/A-Vorgängen, wie z. B. `HttpRequest.Body.Read`, sind eine häufige Quelle für einen Ressourcenmangel im Thread, der zu App-Abstürzen führt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-281">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="5e3f2-282">In 3.0 ist `AllowSynchronousIO` standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="5e3f2-283">Weitere Informationen finden Sie unter <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="5e3f2-284">HTTP/2 standardmäßig aktiviert</span><span class="sxs-lookup"><span data-stu-id="5e3f2-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="5e3f2-285">HTTP/2 ist in Kestrel für HTTPS-Endpunkte standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="5e3f2-286">Die HTTP/2-Unterstützung für IIS oder HTTP.sys ist aktiviert, wenn dies vom Betriebssystem unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="5e3f2-287">EventCounters auf Anforderung</span><span class="sxs-lookup"><span data-stu-id="5e3f2-287">EventCounters on request</span></span>

<span data-ttu-id="5e3f2-288">Die Hostingereignisquelle `Microsoft.AspNetCore.Hosting` gibt die folgenden neuen <xref:System.Diagnostics.Tracing.EventCounter>-Typen zurück, die im Zusammenhang mit eingehenden Anforderungen stehen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="5e3f2-289">Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="5e3f2-289">Endpoint routing</span></span>

<span data-ttu-id="5e3f2-290">Das Endpunktrouting, das den reibungslosen Einsatz von Frameworks (z. B. MVC) mit Middleware ermöglicht, wurde erweitert:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="5e3f2-291">Die Reihenfolge von Middlewarekomponenten und Endpunkten kann in der `Startup.Configure`-Pipeline für die Anforderungsverarbeitung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="5e3f2-292">Endpunkte und Middlewarekomponenten lassen sich gut mit anderen ASP.NET Core-basierten Technologien wie z. B. Integritätsprüfungen kombinieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="5e3f2-293">Endpunkte können sowohl in der Middleware als auch in MVC eine Richtlinie wie beispielsweise CORS oder Autorisierung implementieren.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="5e3f2-294">Filter und Attribute können in Controllermethoden platziert werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="5e3f2-295">Weitere Informationen finden Sie unter <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="5e3f2-296">Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="5e3f2-296">Health Checks</span></span>

<span data-ttu-id="5e3f2-297">Integritätsprüfungen verwenden das Endpunktrouting mit dem generischen Host.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="5e3f2-298">Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="5e3f2-299">Für Endpunkte für Integritätsprüfungen gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="5e3f2-300">Sie geben mindestens einen zugelassenen Host oder Port an.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="5e3f2-301">Sie erfordern Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-301">Require authorization.</span></span>
* <span data-ttu-id="5e3f2-302">Sie erfordern CORS.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-302">Require CORS.</span></span>

<span data-ttu-id="5e3f2-303">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="5e3f2-304">Pipes in HttpContext</span><span class="sxs-lookup"><span data-stu-id="5e3f2-304">Pipes on HttpContext</span></span>

<span data-ttu-id="5e3f2-305">Es ist jetzt möglich, mit der <xref:System.IO.Pipelines>-API den Anforderungstext zu lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="5e3f2-306">Die</span><span class="sxs-lookup"><span data-stu-id="5e3f2-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="5e3f2-307">`HttpRequest.BodyReader`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeReader> bereit, der zum Lesen des Anforderungstexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="5e3f2-308">Die</span><span class="sxs-lookup"><span data-stu-id="5e3f2-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="5e3f2-309">`HttpResponse.BodyWriter`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeWriter> bereit, der zum Schreiben des Antworttexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="5e3f2-310">`HttpRequest.BodyReader` funktioniert analog zum `HttpRequest.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="5e3f2-311">`HttpResponse.BodyWriter` funktioniert analog zum `HttpResponse.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="5e3f2-312">Verbesserte Fehlerberichterstellung in IIS</span><span class="sxs-lookup"><span data-stu-id="5e3f2-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="5e3f2-313">Startfehler beim Hosten von ASP.NET Core-Apps in IIS erzeugen jetzt umfangreichere Diagnosedaten.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="5e3f2-314">Diese Fehler werden mit Stapelüberwachung im Windows-Ereignisprotokoll gemeldet, wann immer möglich.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="5e3f2-315">Darüber hinaus werden alle Warnungen, Fehler und Ausnahmefehler im Windows-Ereignisprotokoll protokolliert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="5e3f2-316">Workerdienst und Worker SDK</span><span class="sxs-lookup"><span data-stu-id="5e3f2-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="5e3f2-317">.NET Core 3.0 führt eine neue Vorlage für die Workerdienst-App ein.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="5e3f2-318">Diese Vorlage dient als Ausgangspunkt für das Schreiben von zeitintensiven Diensten in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="5e3f2-319">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-319">For more information, see:</span></span>

* <span data-ttu-id="5e3f2-320">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/) (.NET Core-Worker als Windows-Dienste)</span><span class="sxs-lookup"><span data-stu-id="5e3f2-320">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)</span></span>
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="5e3f2-321">Verbesserungen an Middleware für weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="5e3f2-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="5e3f2-322">In früheren Versionen von ASP.NET Core war der Aufruf von <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> und <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problematisch, wenn die Bereitstellung auf einem Azure Linux-Computer oder hinter einem anderen Reverseproxy als IIS erfolgt war.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="5e3f2-323">Die Problembehebung für frühere Versionen ist unter [Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies) dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="5e3f2-324">Dieses Szenario wurde in ASP.NET Core 3.0 behoben.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5e3f2-325">Der Host aktiviert die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options), wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="5e3f2-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` ist in unseren Containerimages auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="5e3f2-327">Leistungsverbesserungen</span><span class="sxs-lookup"><span data-stu-id="5e3f2-327">Performance improvements</span></span>

<span data-ttu-id="5e3f2-328">ASP.NET Core 3.0 enthält viele Verbesserungen, die die Arbeitsspeichernutzung reduzieren und den Durchsatz erhöhen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="5e3f2-329">Bei Verwendung des integrierten Containers für die Abhängigkeitsinjektion für bereichsbezogene Dienste wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="5e3f2-330">Im gesamten Framework, einschließlich Middlewareszenarien und Routing, wurden Zuweisungen reduziert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="5e3f2-331">Für Websocketverbindungen wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="5e3f2-332">Bei HTTPS-Verbindungen wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="5e3f2-333">Es steht ein neues optimiertes und vollständig asynchrones JSON-Serialisierungsmodul zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="5e3f2-334">Bei der Formularanalyse wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="5e3f2-335">ASP.NET Core 3.0 funktioniert nur in .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="5e3f2-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="5e3f2-336">Ab ASP.NET Core 3.0 ist .NET Framework kein unterstütztes Zielframework mehr.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="5e3f2-337">Projekte, die auf .NET Framework abzielen, können mit dem [.NET Core 2.1 LTS-Release](https://www.microsoft.com/net/download/dotnet-core/2.1) weiterhin mit vollständiger Unterstützung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="5e3f2-338">Die meisten auf ASP.NET Core 2.1.x bezogenen Pakete werden unbegrenzt unterstützt, auch nach dem 3-jährigen LTS-Zeitraum für .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the 3 year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="5e3f2-339">Informationen zur Migration finden Sie unter [Portieren Ihres Codes von .NET Framework auf .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="5e3f2-340">Verwenden des freigegebenen ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="5e3f2-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="5e3f2-341">Das freigegebene ASP.NET Core 3.0-Framework, das im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten ist, erfordert kein explizites `<PackageReference />`-Element in der Projektdatei mehr.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="5e3f2-342">Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="5e3f2-343">Aus dem freigegebenen ASP.NET Core-Framework entfernte Assemblys</span><span class="sxs-lookup"><span data-stu-id="5e3f2-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="5e3f2-344">Die wichtigsten Assemblys, die aus dem freigegebenen ASP.NET Core 3.0-Framework entfernt wurden:</span><span class="sxs-lookup"><span data-stu-id="5e3f2-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="5e3f2-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="5e3f2-346">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="5e3f2-347">ASP.NET Core 3.0 führt `System.Text.Json` zum Lesen und Schreiben von JSON ein.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="5e3f2-348">Weitere Informationen finden Sie im Abschnitt [Neue JSON-Serialisierung](#new-json-serialization) im vorliegenden Dokument.</span><span class="sxs-lookup"><span data-stu-id="5e3f2-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="5e3f2-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5e3f2-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="5e3f2-350">Eine vollständige Liste der Assemblys, die aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755) (Assemblys, die aus Microsoft.AspNetCore.App 3.0 entfernt werden).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="5e3f2-351">Weitere Informationen zu den Beweggründen für diese Änderung finden Sie unter [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) (Breaking Changes an Microsoft.AspNetCore.App in Version 3.0) und [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Ein erster Blick auf die Änderungen in ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="5e3f2-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
