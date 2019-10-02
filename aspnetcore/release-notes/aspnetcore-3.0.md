---
title: Neuerungen in ASP.NET Core 3.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: aspnetcore-3.0
ms.openlocfilehash: 490d00da7282e2efe28fcc52e593dd71d7324d3f
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198990"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="202b3-103">Neuerungen in ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="202b3-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="202b3-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="202b3-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## <a name="blazor"></a><span data-ttu-id="202b3-105">Blazor</span><span class="sxs-lookup"><span data-stu-id="202b3-105">Blazor</span></span>

<span data-ttu-id="202b3-106">Blazor ist ein Framework in ASP.NET Core zum Erstellen von interaktiven clientseitigen Webbenutzeroberflächen mit .NET:</span><span class="sxs-lookup"><span data-stu-id="202b3-106">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="202b3-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="202b3-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="202b3-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="202b3-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="202b3-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="202b3-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="202b3-110">Vom Blazor-Framework unterstützte Szenarien:</span><span class="sxs-lookup"><span data-stu-id="202b3-110">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="202b3-111">Wiederverwendbare Benutzeroberflächenkomponenten (Razor-Komponenten)</span><span class="sxs-lookup"><span data-stu-id="202b3-111">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="202b3-112">Clientseitiges Routing</span><span class="sxs-lookup"><span data-stu-id="202b3-112">Client-side routing</span></span>
* <span data-ttu-id="202b3-113">Komponentenlayouts</span><span class="sxs-lookup"><span data-stu-id="202b3-113">Component layouts</span></span>
* <span data-ttu-id="202b3-114">Unterstützung für Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="202b3-114">Support for dependency injection</span></span>
* <span data-ttu-id="202b3-115">Formulare und Überprüfung</span><span class="sxs-lookup"><span data-stu-id="202b3-115">Forms and validation</span></span>
* <span data-ttu-id="202b3-116">Erstellen von Komponentenbibliotheken mit Razor-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="202b3-116">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="202b3-117">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="202b3-117">JavaScript interop</span></span>

<span data-ttu-id="202b3-118">Weitere Informationen finden Sie unter <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="202b3-118">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a><span data-ttu-id="202b3-119">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="202b3-119">Blazor Server</span></span>

<span data-ttu-id="202b3-120">Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="202b3-120">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="202b3-121">Blazor Server bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="202b3-121">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="202b3-122">Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="202b3-122">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="202b3-123">Blazor Server wird in ASP.NET Core 3.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="202b3-123">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="202b3-124">Blazor WebAssembly (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="202b3-124">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="202b3-125">Blazor-Apps können auch mit einer WebAssembly-basierten .NET-Runtime direkt im Browser ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-125">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="202b3-126">Blazor WebAssembly befindet sich in der Vorschau und wird in ASP.NET Core 3.0 *nicht* unterstützt.</span><span class="sxs-lookup"><span data-stu-id="202b3-126">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="202b3-127">Blazor WebAssembly wird in einer zukünftigen Version von ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="202b3-127">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="202b3-128">Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="202b3-128">Razor components</span></span>

<span data-ttu-id="202b3-129">Blazor-Apps sind auf Komponenten aufgebaut.</span><span class="sxs-lookup"><span data-stu-id="202b3-129">Blazor apps are built from components.</span></span> <span data-ttu-id="202b3-130">Komponenten sind eigenständige Elemente einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="202b3-130">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="202b3-131">Komponenten sind normale .NET-Klassen, die die Renderinglogik für die Benutzeroberfläche sowie clientseitige Ereignishandler definieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-131">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="202b3-132">Damit können Sie umfangreiche interaktive Web-Apps ohne JavaScript erstellen.</span><span class="sxs-lookup"><span data-stu-id="202b3-132">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="202b3-133">Komponenten in Razor werden üblicherweise mit der Razor-Syntax erstellt, einer natürlichen Mischung aus HTML und C#.</span><span class="sxs-lookup"><span data-stu-id="202b3-133">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="202b3-134">Razor-Komponenten ähneln Razor Pages und MVC-Ansichten dahingehend, dass sie ebenfalls Razor verwenden.</span><span class="sxs-lookup"><span data-stu-id="202b3-134">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="202b3-135">Im Gegensatz zu Pages und Ansichten, die auf einem Anforderung/Antwort-Modell basieren, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="202b3-135">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="202b3-136">gRPC</span><span class="sxs-lookup"><span data-stu-id="202b3-136">gRPC</span></span>

<span data-ttu-id="202b3-137">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="202b3-137">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="202b3-138">Ein beliebtes, hochleistungsfähiges RPC-Framework (Remote Procedure Call, Remoteprozeduraufruf).</span><span class="sxs-lookup"><span data-stu-id="202b3-138">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="202b3-139">Bietet einen strikten Contract First-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="202b3-139">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="202b3-140">Verwendet moderne Technologien wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="202b3-140">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="202b3-141">HTTP/2 für den Transport.</span><span class="sxs-lookup"><span data-stu-id="202b3-141">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="202b3-142">Protokollpuffer als Sprache zur Schnittstellenbeschreibung.</span><span class="sxs-lookup"><span data-stu-id="202b3-142">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="202b3-143">Format der binären Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="202b3-143">Binary serialization format.</span></span>
* <span data-ttu-id="202b3-144">Bietet beispielsweise folgende Features:</span><span class="sxs-lookup"><span data-stu-id="202b3-144">Provides features such as:</span></span>

  * <span data-ttu-id="202b3-145">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="202b3-145">Authentication</span></span>
  * <span data-ttu-id="202b3-146">Bidirektionales Streaming und Flusssteuerung.</span><span class="sxs-lookup"><span data-stu-id="202b3-146">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="202b3-147">Stornierung und Timeouts.</span><span class="sxs-lookup"><span data-stu-id="202b3-147">Cancellation and timeouts.</span></span>

<span data-ttu-id="202b3-148">Die gRPC-Funktionalität in ASP.NET Core 3.0 umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="202b3-148">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="202b3-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ein ASP.NET Core-Framework zum Hosten von gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="202b3-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="202b3-150">gRPC in ASP.NET Core lässt sich in ASP.NET Core-Standardfeatures wie Protokollierung, Abhängigkeitsinjektion (Dependency Injection, DI), Authentifizierung und Autorisierung integrieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-150">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication and authorization.</span></span>
* <span data-ttu-id="202b3-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; ein gRPC-Client für .NET Core, der auf dem vertrauten `HttpClient` aufbaut.</span><span class="sxs-lookup"><span data-stu-id="202b3-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="202b3-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC-Clientintegration mit `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="202b3-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="202b3-153">Weitere Informationen finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="202b3-153">For more information, see <xref:grpc/index>.</span></span>

## <a name="signalr"></a><span data-ttu-id="202b3-154">SignalR</span><span class="sxs-lookup"><span data-stu-id="202b3-154">SignalR</span></span>

<span data-ttu-id="202b3-155">Anleitungen zur Migration finden Sie unter [Aktualisieren von SignalR-Code](xref:migration/22-to-30#signalr).</span><span class="sxs-lookup"><span data-stu-id="202b3-155">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="202b3-156">SignalR verwendet jetzt `System.Text.Json`, um JSON-Nachrichten zu serialisieren bzw. zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-156">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="202b3-157">Anleitungen zum Wiederherstellen des `Newtonsoft.Json`-basierten Serialisierungsmoduls finden Sie unter [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) (Wechseln zu Newtonsoft.Json).</span><span class="sxs-lookup"><span data-stu-id="202b3-157">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="202b3-158">In den JavaScript- und .NET-Clients für SignalR wurde Unterstützung für eine automatische erneute Verbindungsherstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="202b3-158">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="202b3-159">Standardmäßig versucht der Client sofort, die Verbindung wiederherzustellen, und wiederholt den Versuch nötigenfalls nach 2, 10 und 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="202b3-159">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="202b3-160">Wenn der Client die Verbindung erfolgreich wiederherstellt, erhält er eine neue Verbindungs-ID.</span><span class="sxs-lookup"><span data-stu-id="202b3-160">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="202b3-161">Die automatische erneute Herstellung einer Verbindung kann optional aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="202b3-161">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="202b3-162">Die Intervalle für die Wiederherstellung einer Verbindung können durch Übergabe eines Arrays aus millisekundenbasierten Zeitspannen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="202b3-162">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="202b3-163">Zur vollständigen Steuerung der Wiederherstellungsintervalle kann eine benutzerdefinierte Implementierung übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-163">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="202b3-164">Wenn die Verbindung nicht wiederhergestellt werden kann, passiert nach dem letzten Intervall Folgendes:</span><span class="sxs-lookup"><span data-stu-id="202b3-164">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="202b3-165">Der Client betrachtet die Verbindung als offline.</span><span class="sxs-lookup"><span data-stu-id="202b3-165">The client considers the connection is offline.</span></span>
* <span data-ttu-id="202b3-166">Der Client versucht nicht mehr, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="202b3-166">The client stops trying to reconnect.</span></span>

<span data-ttu-id="202b3-167">Aktualisieren Sie die App-Benutzeroberfläche während der Verbindungsversuche, um den Benutzer darüber zu informieren, dass versucht wird, erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="202b3-167">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="202b3-168">Um bei einer unterbrochenen Verbindung Feedback auf der Benutzeroberfläche bereitstellen zu können, wurde die SignalR-Client-API erweitert und umfasst jetzt die folgenden Ereignishandler:</span><span class="sxs-lookup"><span data-stu-id="202b3-168">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="202b3-169">`onreconnecting`:  Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu deaktivieren oder Benutzer darüber zu informieren, dass die App offline ist.</span><span class="sxs-lookup"><span data-stu-id="202b3-169">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="202b3-170">`onreconnected`: Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu aktualisieren, sobald die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="202b3-170">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="202b3-171">Der folgende Code verwendet `onreconnecting`, um die Benutzeroberfläche während der Verbindungsversuche zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="202b3-171">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="202b3-172">Der folgende Code verwendet `onreconnected`, um die Benutzeroberfläche nach erfolgreich hergestellter Verbindung zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="202b3-172">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="202b3-173">SignalR 3.0 und höher bietet eine benutzerdefinierte Ressource für Autorisierungshandler, wenn eine Hubmethode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="202b3-173">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="202b3-174">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="202b3-174">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="202b3-175">Der `HubInvocationContext` umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="202b3-175">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="202b3-176">Den Namen der aufgerufenen Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="202b3-176">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="202b3-177">Argumente für die Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="202b3-177">Arguments to the hub method.</span></span>

<span data-ttu-id="202b3-178">Sehen Sie sich das folgende Beispiel einer Chatroom-App an, die mehreren Mitgliedern einer Organisation die Anmeldung über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="202b3-178">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="202b3-179">Jede Person mit einem Microsoft-Konto kann sich beim Chat anmelden, aber nur Mitglieder der besitzenden Organisation können Benutzer sperren oder den Chatverlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="202b3-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users’ chat histories.</span></span> <span data-ttu-id="202b3-180">Die App könnte bestimmte Funktionen für bestimmte Benutzer einschränken.</span><span class="sxs-lookup"><span data-stu-id="202b3-180">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="202b3-181">Im vorherigen Code dient `DomainRestrictedRequirement` als benutzerdefinierte `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="202b3-181">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="202b3-182">Da der Ressourcenparameter `HubInvocationContext` übergeben wird, kann die interne Logik folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="202b3-182">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="202b3-183">Sie kann den Kontext untersuchen, in dem der Hub aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="202b3-183">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="202b3-184">Sie kann Entscheidungen darüber treffen, ob einem Benutzer die Ausführung einzelner Hubmethoden gestattet wird.</span><span class="sxs-lookup"><span data-stu-id="202b3-184">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="202b3-185">Einzelne Hubmethoden können durch den Namen der Richtlinie ergänzt werden, die der Code zur Laufzeit prüft.</span><span class="sxs-lookup"><span data-stu-id="202b3-185">Individual Hub methods can be decorated with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="202b3-186">Wenn Clients versuchen, einzelne Hubmethoden aufzurufen, wird der `DomainRestrictedRequirement`-Handler ausgeführt und steuert den Zugriff auf die Methoden.</span><span class="sxs-lookup"><span data-stu-id="202b3-186">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="202b3-187">Basierend auf der Art und Weise, in der `DomainRestrictedRequirement` den Zugriff steuert, kann Folgendes passieren:</span><span class="sxs-lookup"><span data-stu-id="202b3-187">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="202b3-188">Alle angemeldeten Benutzer können die `SendMessage`-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="202b3-188">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="202b3-189">Nur Benutzer, die sich mit einer `@jabbr.net`-E-Mail-Adresse angemeldet haben, können den Verlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="202b3-189">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="202b3-190">Nur `bob42@jabbr.net` kann Benutzer aus dem Chatroom ausschließen.</span><span class="sxs-lookup"><span data-stu-id="202b3-190">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="202b3-191">Die Erstellung der `DomainRestricted`-Richtlinie kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="202b3-191">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="202b3-192">Fügen Sie die neue Richtlinie in *Startup.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="202b3-192">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="202b3-193">Stellen Sie die benutzerdefinierte Anforderung `DomainRestrictedRequirement` als Parameter bereit.</span><span class="sxs-lookup"><span data-stu-id="202b3-193">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="202b3-194">Registrieren Sie `DomainRestricted` bei der Autorisierungsmiddleware.</span><span class="sxs-lookup"><span data-stu-id="202b3-194">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

<span data-ttu-id="202b3-195">SignalR-Hubs verwenden [Endpunktrouting](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="202b3-195">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="202b3-196">Eine SignalR-Hubverbindung wurde zuvor explizit eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="202b3-196">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="202b3-197">In der vorherigen Version mussten Entwickler Controller, Razor-Seiten und Hubs an den unterschiedlichsten Orten miteinander verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="202b3-197">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of different places.</span></span> <span data-ttu-id="202b3-198">Explizite Verbindungen führen zu einer Reihe nahezu identischer Routingsegmente:</span><span class="sxs-lookup"><span data-stu-id="202b3-198">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

<span data-ttu-id="202b3-199">SignalR 3.0-Hubs können per Endpunktrouting weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-199">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="202b3-200">Beim Endpunktrouting kann in der Regel das gesamte Routing in `UseRouting` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="202b3-200">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="202b3-201">In ASP.NET Core 3.0 durch SignalR hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="202b3-201">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="202b3-202">Streaming vom Client zum Server.</span><span class="sxs-lookup"><span data-stu-id="202b3-202">Client-to-server streaming.</span></span> <span data-ttu-id="202b3-203">Mit dem Streaming vom Client zum Server können serverseitige Methoden Instanzen von `IAsyncEnumerable<T>` oder `ChannelReader<T>` akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-203">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="202b3-204">Im folgenden C#-Beispiel empfängt die `UploadStream`-Methode im Hub einen Zeichenfolgenstream vom Client:</span><span class="sxs-lookup"><span data-stu-id="202b3-204">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="202b3-205">.NET-Client-Apps können entweder eine `IAsyncEnumerable<T>`-oder eine `ChannelReader<T>`-Instanz als `stream`-Argument der oben genannten `UploadStream`-Hubmethode übergeben.</span><span class="sxs-lookup"><span data-stu-id="202b3-205">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="202b3-206">Nachdem die `for`-Schleife abgeschlossen ist und die lokale Funktion existiert, wird der Streamabschluss gesendet:</span><span class="sxs-lookup"><span data-stu-id="202b3-206">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="202b3-207">JavaScript-Client-Apps verwenden das SignalR-`Subject` (oder ein [RxJS-Subject](https://rxjs.dev/api/index/class/Subject)) für das `stream`-Argument der oben genannten `UploadStream`-Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="202b3-207">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="202b3-208">Der JavaScript-Code könnte die `subject.next`-Methode verwenden, um Zeichenfolgen zu verarbeiten, sobald diese erfasst werden und zum Senden an den Server bereit sin.</span><span class="sxs-lookup"><span data-stu-id="202b3-208">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="202b3-209">Mit Code wie in den beiden obigen Codeausschnitten lassen sich Echtzeitstreamingfunktionen erzeugen.</span><span class="sxs-lookup"><span data-stu-id="202b3-209">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

### <a name="new-json-serialization"></a><span data-ttu-id="202b3-210">Neue JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="202b3-210">New JSON serialization</span></span>

<span data-ttu-id="202b3-211">ASP.NET Core 3.0 verwendet jetzt standardmäßig <xref:System.Text.Json> zur JSON-Serialisierung:</span><span class="sxs-lookup"><span data-stu-id="202b3-211">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="202b3-212">Lese- und Schreibvorgänge in JSON erfolgen asynchron.</span><span class="sxs-lookup"><span data-stu-id="202b3-212">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="202b3-213">Der Code ist für UTF-8-Text optimiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-213">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="202b3-214">In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.</span><span class="sxs-lookup"><span data-stu-id="202b3-214">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="202b3-215">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="202b3-215">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="202b3-216">Neue Razor-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="202b3-216">New Razor directives</span></span>

<span data-ttu-id="202b3-217">Die folgende Liste enthält neue Razor-Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="202b3-217">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="202b3-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; die `@attribute`-Anweisung wendet das angegebene Attribut auf die Klasse der generierten Seite oder Ansicht an.</span><span class="sxs-lookup"><span data-stu-id="202b3-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="202b3-219">Beispielsweise `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="202b3-219">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="202b3-220">[@implements](xref:mvc/views/razor#implements) &ndash; die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.</span><span class="sxs-lookup"><span data-stu-id="202b3-220">[@implements](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="202b3-221">Beispielsweise `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="202b3-221">For example, `@implements IDisposable`.</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="202b3-222">Zertifikat- und Kerberos-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="202b3-222">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="202b3-223">Die Zertifikatsauthentifizierung erfordert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="202b3-223">Certificate authentication requires:</span></span>

* <span data-ttu-id="202b3-224">Der Server muss so konfiguriert werden, dass er Zertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-224">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="202b3-225">Die Authentifizierungsmiddleware muss in `Startup.Configure` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-225">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="202b3-226">Der Zertifikatauthentifizierungsdienst muss in `Startup.ConfigureServices` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-226">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="202b3-227">Die Optionen für die Zertifikatauthentifizierung umfassen folgende Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="202b3-227">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="202b3-228">Akzeptieren selbstsignierter Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="202b3-228">Accept self-signed certificates.</span></span>
* <span data-ttu-id="202b3-229">Überprüfen auf Zertifikatswiderruf.</span><span class="sxs-lookup"><span data-stu-id="202b3-229">Check for certificate revocation.</span></span>
* <span data-ttu-id="202b3-230">Überprüfen, ob das angebotene Zertifikat die richtigen Nutzungsflags enthält.</span><span class="sxs-lookup"><span data-stu-id="202b3-230">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="202b3-231">Aus den Zertifikateigenschaften wird ein Standardbenutzerprinzipal erstellt.</span><span class="sxs-lookup"><span data-stu-id="202b3-231">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="202b3-232">Der Benutzerprinzipal enthält ein Ereignis, das das Ergänzen oder Ersetzen des Prinzipals ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="202b3-232">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="202b3-233">Weitere Informationen finden Sie unter <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="202b3-233">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="202b3-234">Die [Windows-Authentifizierung](/windows-server/security/windows-authentication/windows-authentication-overview) wurde auf Linux und macOS erweitert.</span><span class="sxs-lookup"><span data-stu-id="202b3-234">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="202b3-235">In vorherigen Versionen war die Windows-Authentifizierung auf [IIS](xref:host-and-deploy/iis/index) und [HttpSys](xref:fundamentals/servers/httpsys) beschränkt.</span><span class="sxs-lookup"><span data-stu-id="202b3-235">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="202b3-236">In ASP.NET Core 3.0 besitzt [Kestrel](xref:fundamentals/servers/kestrel) die Möglichkeit, Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) und [NTLM unter Windows](/windows-server/security/kerberos/ntlm-overview), Linux und macOS für Hosts zu verwenden, die der Windows-Domäne beigetreten sind.</span><span class="sxs-lookup"><span data-stu-id="202b3-236">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="202b3-237">Die Kestrel-Unterstützung für diese Authentifizierungsschemas wird durch das Paket [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="202b3-237">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="202b3-238">Wie bei anderen Authentifizierungsdiensten auch sollte erst die Authentifizierung auf App-Ebene und dann der Dienst konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="202b3-238">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="202b3-239">Hostanforderungen:</span><span class="sxs-lookup"><span data-stu-id="202b3-239">Host requirements:</span></span>

* <span data-ttu-id="202b3-240">Windows-Hosts müssen dem Benutzerkonto, das die App hostet, [Dienstprinzipalnamen](/windows/win32/ad/service-principal-names) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="202b3-240">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="202b3-241">Linux- und macOS-Computer müssen der Domäne beigetreten sein.</span><span class="sxs-lookup"><span data-stu-id="202b3-241">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="202b3-242">Für den Webprozess müssen Dienstprinzipalnamen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-242">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="202b3-243">[Keytab-Dateien](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) müssen generiert und auf dem Hostcomputer konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-243">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="202b3-244">Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="202b3-244">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="202b3-245">Änderungen an Vorlagen</span><span class="sxs-lookup"><span data-stu-id="202b3-245">Template changes</span></span>

<span data-ttu-id="202b3-246">Aus den Vorlagen für Webbenutzeroberflächen (Razor Pages, MVC mit Controller und Ansichten) wurde Folgendes entfernt:</span><span class="sxs-lookup"><span data-stu-id="202b3-246">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="202b3-247">Die Benutzeroberfläche für die Zustimmung zu Cookies ist nicht mehr enthalten.</span><span class="sxs-lookup"><span data-stu-id="202b3-247">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="202b3-248">Informationen dazu, wie Sie die Zustimmung zu Cookies in einer aus einer ASP.NET Core 3.0-Vorlage generierten App aktivieren, finden Sie unter <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="202b3-248">To enable the cookie consent feature in an ASP.NET Core 3.0 template generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="202b3-249">Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs mehr dafür verwendet.</span><span class="sxs-lookup"><span data-stu-id="202b3-249">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="202b3-250">Weitere Informationen finden Sie unter [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350) (Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs basierend auf der aktuellen Umgebung mehr verwendet).</span><span class="sxs-lookup"><span data-stu-id="202b3-250">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="202b3-251">Die Angular-Vorlage wurde auf Angular 8 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-251">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="202b3-252">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="202b3-252">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="202b3-253">Eine neue Vorlagenoption in Visual Studio bietet Vorlagenunterstützung für Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="202b3-253">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="202b3-254">Übergeben Sie beim Erstellen einer RCL aus einer Vorlage in einer Befehlsshell die Option `-support-pages-and-views` (`dotnet new razorclasslib -support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="202b3-254">When creating an RCL from the template in a command shell, pass the `-support-pages-and-views` option (`dotnet new razorclasslib -support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="202b3-255">Generischer Host</span><span class="sxs-lookup"><span data-stu-id="202b3-255">Generic Host</span></span>

<span data-ttu-id="202b3-256">Die ASP.NET Core 3.0-Vorlagen verwenden <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="202b3-256">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="202b3-257">In vorherigen Versionen wurde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> verwendet.</span><span class="sxs-lookup"><span data-stu-id="202b3-257">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="202b3-258">Durch Verwendung des generischen .NET Core-Hosts (<xref:Microsoft.Extensions.Hosting.HostBuilder>) lässt sich eine bessere Integration von ASP.NET Core-Apps in andere, nicht webspezifische Serverszenarien erzielen.</span><span class="sxs-lookup"><span data-stu-id="202b3-258">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that are not web specific.</span></span> <span data-ttu-id="202b3-259">Weitere Informationen finden Sie unter [HostBuilder ersetzt WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="202b3-259">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="202b3-260">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="202b3-260">Host configuration</span></span>

<span data-ttu-id="202b3-261">Vor ASP.NET Core 3.0 wurden Umgebungsvariablen mit dem Präfix `ASPNETCORE_` für die Hostkonfiguration des Webhosts geladen.</span><span class="sxs-lookup"><span data-stu-id="202b3-261">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="202b3-262">In Version 3.0 wird `AddEnvironmentVariables` zum Laden von Umgebungsvariablen mit dem Präfix `DOTNET_` zur Hostkonfiguration mit `CreateDefaultBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="202b3-262">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-contructor-injection"></a><span data-ttu-id="202b3-263">Änderungen an der Startkonstruktorinjektion</span><span class="sxs-lookup"><span data-stu-id="202b3-263">Changes to Startup contructor injection</span></span>

<span data-ttu-id="202b3-264">Der generische Host unterstützt nur die folgenden Typen für die Injektion des `Startup`-Konstruktors:</span><span class="sxs-lookup"><span data-stu-id="202b3-264">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="202b3-265">Alle Dienste können weiterhin direkt als Argumente in die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-265">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="202b3-266">Weitere Informationen finden Sie unter [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353) (Generischer Host schränkt Startkonstruktorinjektion ein).</span><span class="sxs-lookup"><span data-stu-id="202b3-266">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="202b3-267">Kestrel</span><span class="sxs-lookup"><span data-stu-id="202b3-267">Kestrel</span></span>

* <span data-ttu-id="202b3-268">Die Kestrel-Konfiguration wurde für die Migration zum generischen Host aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-268">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="202b3-269">In Version 3.0 ist Kestrel im Webhostgenerator konfiguriert, der durch `ConfigureWebHostDefaults` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="202b3-269">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="202b3-270">Verbindungsadapter wurden aus Kestrel entfernt und durch Verbindungsmiddleware ersetzt. Diese ähnelt der HTTP-Middleware in der ASP.NET Core-Pipeline, ist aber für Verbindungen auf niedrigerer Ebene konzipiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-270">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="202b3-271">Die Kestrel-Transportschicht wurde in `Connections.Abstractions` als öffentliche Schnittstelle verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="202b3-271">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="202b3-272">Eine Mehrdeutigkeit zwischen Headern und Trailern wurde aufgelöst, indem nachstehende Header in eine neue Sammlung verschoben wurden.</span><span class="sxs-lookup"><span data-stu-id="202b3-272">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="202b3-273">APIs mit synchronen E/A-Vorgängen, wie z. B. `HttpReqeuest.Body.Read`, sind eine häufige Quelle für einen Ressourcenmangel im Thread, der zu App-Abstürzen führt.</span><span class="sxs-lookup"><span data-stu-id="202b3-273">Synchronous IO APIs, such as `HttpReqeuest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="202b3-274">In 3.0 ist `AllowSynchronousIO` standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="202b3-274">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="202b3-275">Weitere Informationen finden Sie unter <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="202b3-275">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="202b3-276">HTTP/2 standardmäßig aktiviert</span><span class="sxs-lookup"><span data-stu-id="202b3-276">HTTP/2 enabled by default</span></span>

<span data-ttu-id="202b3-277">HTTP/2 ist in Kestrel für HTTPS-Endpunkte standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="202b3-277">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="202b3-278">Die HTTP/2-Unterstützung für IIS oder HTTP.sys ist aktiviert, wenn dies vom Betriebssystem unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="202b3-278">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="request-counters"></a><span data-ttu-id="202b3-279">Anforderungszähler</span><span class="sxs-lookup"><span data-stu-id="202b3-279">Request counters</span></span>

<span data-ttu-id="202b3-280">Die Hostingereignisquelle (Microsoft.AspNetCore.Hosting) gibt die folgenden Ereigniszähler in Zusammenhang mit eingehenden Anforderungen aus:</span><span class="sxs-lookup"><span data-stu-id="202b3-280">The Hosting EventSource (Microsoft.AspNetCore.Hosting) emits the following EventCounters related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="202b3-281">Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="202b3-281">Endpoint routing</span></span>

<span data-ttu-id="202b3-282">Das Endpunktrouting, das den reibungslosen Einsatz von Frameworks (z. B. MVC) mit Middleware ermöglicht, wurde erweitert:</span><span class="sxs-lookup"><span data-stu-id="202b3-282">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="202b3-283">Die Reihenfolge von Middlewarekomponenten und Endpunkten kann in der `Startup.Configure`-Pipeline für die Anforderungsverarbeitung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-283">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="202b3-284">Endpunkte und Middlewarekomponenten lassen sich gut mit anderen ASP.NET Core-basierten Technologien wie z. B. Integritätsprüfungen kombinieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-284">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="202b3-285">Endpunkte können sowohl in der Middleware als auch in MVC eine Richtlinie wie beispielsweise CORS oder Autorisierung implementieren.</span><span class="sxs-lookup"><span data-stu-id="202b3-285">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="202b3-286">Filter und Attribute können in Controllermethoden platziert werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-286">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="202b3-287">Weitere Informationen finden Sie unter <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="202b3-287">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="202b3-288">Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="202b3-288">Health Checks</span></span>

<span data-ttu-id="202b3-289">Integritätsprüfungen verwenden das Endpunktrouting mit dem generischen Host.</span><span class="sxs-lookup"><span data-stu-id="202b3-289">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="202b3-290">Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:</span><span class="sxs-lookup"><span data-stu-id="202b3-290">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="202b3-291">Für Endpunkte für Integritätsprüfungen gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="202b3-291">Health Checks endpoints can:</span></span>

* <span data-ttu-id="202b3-292">Sie geben mindestens einen zugelassenen Host oder Port an.</span><span class="sxs-lookup"><span data-stu-id="202b3-292">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="202b3-293">Sie erfordern Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="202b3-293">Require authorization.</span></span>
* <span data-ttu-id="202b3-294">Sie erfordern CORS.</span><span class="sxs-lookup"><span data-stu-id="202b3-294">Require CORS.</span></span>

<span data-ttu-id="202b3-295">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="202b3-295">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="202b3-296">Pipes in HttpContext</span><span class="sxs-lookup"><span data-stu-id="202b3-296">Pipes on HttpContext</span></span>

<span data-ttu-id="202b3-297">Es ist jetzt möglich, mit der <xref:System.IO.Pipelines>-API den Anforderungstext zu lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="202b3-297">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="202b3-298">Die</span><span class="sxs-lookup"><span data-stu-id="202b3-298">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="202b3-299">`HttpRequest.BodyReader`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeReader> bereit, der zum Lesen des Anforderungstexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="202b3-299">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="202b3-300">Die</span><span class="sxs-lookup"><span data-stu-id="202b3-300">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="202b3-301">`HttpResponse.BodyWriter`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeWriter> bereit, der zum Schreiben des Antworttexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="202b3-301">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="202b3-302">`HttpRequest.BodyReader` funktioniert analog zum `HttpRequest.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="202b3-302">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="202b3-303">`HttpResponse.BodyWriter` funktioniert analog zum `HttpResponse.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="202b3-303">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="202b3-304">Verbesserte Fehlerberichterstellung in IIS</span><span class="sxs-lookup"><span data-stu-id="202b3-304">Improved error reporting in IIS</span></span>

<span data-ttu-id="202b3-305">Startfehler beim Hosten von ASP.NET Core-Apps in IIS erzeugen jetzt umfangreichere Diagnosedaten.</span><span class="sxs-lookup"><span data-stu-id="202b3-305">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="202b3-306">Diese Fehler werden mit Stapelüberwachung im Windows-Ereignisprotokoll gemeldet, wann immer möglich.</span><span class="sxs-lookup"><span data-stu-id="202b3-306">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="202b3-307">Darüber hinaus werden alle Warnungen, Fehler und Ausnahmefehler im Windows-Ereignisprotokoll protokolliert.</span><span class="sxs-lookup"><span data-stu-id="202b3-307">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="202b3-308">Workerdienst und Worker SDK</span><span class="sxs-lookup"><span data-stu-id="202b3-308">Worker Service and Worker SDK</span></span>

<span data-ttu-id="202b3-309">.NET Core 3.0 führt eine neue Vorlage für die Workerdienst-App ein.</span><span class="sxs-lookup"><span data-stu-id="202b3-309">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="202b3-310">Diese Vorlage dient als Ausgangspunkt für das Schreiben von Diensten mit langer Laufzeit in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="202b3-310">This template is provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="202b3-311">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="202b3-311">For more information, see:</span></span>

* <span data-ttu-id="202b3-312">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/) (.NET Core-Worker als Windows-Dienste)</span><span class="sxs-lookup"><span data-stu-id="202b3-312">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)</span></span>
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="202b3-313">Verbesserungen an Middleware für weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="202b3-313">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="202b3-314">In früheren Versionen von ASP.NET Core war der Aufruf von <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> und <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problematisch, wenn die Bereitstellung auf einem Azure Linux-Computer oder hinter einem anderen Reverseproxy als IIS erfolgt war.</span><span class="sxs-lookup"><span data-stu-id="202b3-314">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="202b3-315">Die Problembehebung für frühere Versionen ist unter [Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies) dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="202b3-315">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="202b3-316">Dieses Szenario wurde in ASP.NET Core 3.0 behoben.</span><span class="sxs-lookup"><span data-stu-id="202b3-316">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="202b3-317">Der Host aktiviert die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options), wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="202b3-317">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="202b3-318">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` ist in unseren Containerimages auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="202b3-318">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="202b3-319">Leistungsverbesserungen</span><span class="sxs-lookup"><span data-stu-id="202b3-319">Performance improvements</span></span>

<span data-ttu-id="202b3-320">ASP.NET Core 3.0 enthält viele Verbesserungen, die die Arbeitsspeichernutzung reduzieren und den Durchsatz erhöhen:</span><span class="sxs-lookup"><span data-stu-id="202b3-320">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="202b3-321">Bei Verwendung des integrierten Containers für die Abhängigkeitsinjektion für bereichsbezogene Dienste wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="202b3-321">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="202b3-322">Im gesamten Framework, einschließlich Middlewareszenarien und Routing, wurden Zuweisungen reduziert.</span><span class="sxs-lookup"><span data-stu-id="202b3-322">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="202b3-323">Für Websocketverbindungen wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="202b3-323">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="202b3-324">Bei HTTPS-Verbindungen wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="202b3-324">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="202b3-325">Es steht ein neues optimiertes und vollständig asynchrones JSON-Serialisierungsmodul zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="202b3-325">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="202b3-326">Bei der Formularanalyse wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="202b3-326">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="202b3-327">ASP.NET Core 3.0 funktioniert nur in .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="202b3-327">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="202b3-328">Ab ASP.NET Core 3.0 ist .NET Framework kein unterstütztes Zielframework mehr.</span><span class="sxs-lookup"><span data-stu-id="202b3-328">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="202b3-329">Projekte, die auf .NET Framework abzielen, können mit dem [.NET Core 2.1 LTS-Release](https://www.microsoft.com/net/download/dotnet-core/2.1) weiterhin mit vollständiger Unterstützung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="202b3-329">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="202b3-330">Die meisten auf ASP.NET Core 2.1.x bezogenen Pakete werden unbegrenzt unterstützt, auch nach dem 3-jährigen LTS-Zeitraum für .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="202b3-330">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the 3 year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="202b3-331">Informationen zur Migration finden Sie unter [Portieren Ihres Codes von .NET Framework auf .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="202b3-331">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="202b3-332">Verwenden des freigegebenen ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="202b3-332">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="202b3-333">Das freigegebene ASP.NET Core 3.0-Framework, das im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten ist, erfordert kein explizites `<PackageReference />`-Element in der Projektdatei mehr.</span><span class="sxs-lookup"><span data-stu-id="202b3-333">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="202b3-334">Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:</span><span class="sxs-lookup"><span data-stu-id="202b3-334">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="202b3-335">Aus dem freigegebenen ASP.NET Core-Framework entfernte Assemblys</span><span class="sxs-lookup"><span data-stu-id="202b3-335">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="202b3-336">Die wichtigsten Assemblys, die aus dem freigegebenen ASP.NET Core 3.0-Framework entfernt wurden:</span><span class="sxs-lookup"><span data-stu-id="202b3-336">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="202b3-337">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="202b3-337">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="202b3-338">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="202b3-338">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="202b3-339">ASP.NET Core 3.0 führt `System.Text.Json` zum Lesen und Schreiben von JSON ein.</span><span class="sxs-lookup"><span data-stu-id="202b3-339">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="202b3-340">Weitere Informationen finden Sie im Abschnitt [Neue JSON-Serialisierung](#new-json-serialization) im vorliegenden Dokument.</span><span class="sxs-lookup"><span data-stu-id="202b3-340">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="202b3-341">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="202b3-341">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="202b3-342">Eine vollständige Liste der Assemblys, die aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755) (Assemblys, die aus Microsoft.AspNetCore.App 3.0 entfernt werden).</span><span class="sxs-lookup"><span data-stu-id="202b3-342">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="202b3-343">Weitere Informationen zu den Beweggründen für diese Änderung finden Sie unter [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) (Breaking Changes an Microsoft.AspNetCore.App in Version 3.0) und [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Ein erster Blick auf die Änderungen in ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="202b3-343">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
