---
title: Verwenden von Hubs in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie mit Hubs in ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 244ddc40e647bfcc3ca8cda2797c51bc49174822
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320146"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="9690f-103">Verwenden von Hubs in SignalR für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9690f-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="9690f-104">Durch [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="9690f-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="9690f-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9690f-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="9690f-106">Was ist eine SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="9690f-106">What is a SignalR hub</span></span>

<span data-ttu-id="9690f-107">Der SignalR-Hubs-API können Sie zum Aufrufen von Methoden auf verbundene Clients vom Server.</span><span class="sxs-lookup"><span data-stu-id="9690f-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="9690f-108">In den Servercode definieren Sie Methoden, die vom Client aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9690f-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="9690f-109">Im Clientcode definieren Sie Methoden, die vom Server aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9690f-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="9690f-110">SignalR kümmert sich um alles, was hinter den Kulissen, die in Echtzeit-Client-zu-Server und Server-zu-Client-Kommunikation möglich macht.</span><span class="sxs-lookup"><span data-stu-id="9690f-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="9690f-111">Konfigurieren von SignalR-hubs</span><span class="sxs-lookup"><span data-stu-id="9690f-111">Configure SignalR hubs</span></span>

<span data-ttu-id="9690f-112">Die SignalR-Middleware ist erforderlich, einige Dienste, die durch den Aufruf konfiguriert werden `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="9690f-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

<span data-ttu-id="9690f-113">Beim Hinzufügen von SignalR-Funktionen zu einer ASP.NET Core-app einrichten SignalR-Routen, durch den Aufruf `app.UseSignalR` in die `Startup.Configure` Methode.</span><span class="sxs-lookup"><span data-stu-id="9690f-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a><span data-ttu-id="9690f-114">Erstellen und Verwenden von hubs</span><span class="sxs-lookup"><span data-stu-id="9690f-114">Create and use hubs</span></span>

<span data-ttu-id="9690f-115">Erstellen Sie einen Hub durch Deklarieren einer Klasse, die von erbt `Hub`, und fügen Sie öffentliche Methoden hinzu.</span><span class="sxs-lookup"><span data-stu-id="9690f-115">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="9690f-116">Clients können als definierten Methoden aufrufen `public`.</span><span class="sxs-lookup"><span data-stu-id="9690f-116">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="9690f-117">Sie können einen Rückgabetyp und Parameter, einschließlich von komplexen Typen und Arrays, wie in jeder C#-Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="9690f-117">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="9690f-118">SignalR verarbeitet die Serialisierung und Deserialisierung von komplexen Objekten und Arrays in Ihre Parameter und Rückgabewerte.</span><span class="sxs-lookup"><span data-stu-id="9690f-118">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="9690f-119">Hubs sind vorübergehend:</span><span class="sxs-lookup"><span data-stu-id="9690f-119">Hubs are transient:</span></span>
>
> * <span data-ttu-id="9690f-120">Speichern Sie Status nicht in einer Eigenschaft für die hubklasse.</span><span class="sxs-lookup"><span data-stu-id="9690f-120">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="9690f-121">Jeder Aufruf der Hub-Methode wird eine neue hubinstanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9690f-121">Every hub method call is executed on a new hub instance.</span></span>  
> * <span data-ttu-id="9690f-122">Verwendung `await` beim Aufrufen von asynchroner Methoden, die auf dem Hub bleiben aktiv abhängig sind.</span><span class="sxs-lookup"><span data-stu-id="9690f-122">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="9690f-123">Zum Beispiel eine Methode, z. B. `Clients.All.SendAsync(...)` kann fehlschlagen, wenn er, ohne aufgerufen wird `await` und die hubmethode abgeschlossen wird, bevor Sie `SendAsync` abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="9690f-123">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="9690f-124">Das Context-Objekt</span><span class="sxs-lookup"><span data-stu-id="9690f-124">The Context object</span></span>

<span data-ttu-id="9690f-125">Die `Hub` -Klasse verfügt über eine `Context` Eigenschaft, die mit Informationen über die Verbindung die folgenden Eigenschaften enthält:</span><span class="sxs-lookup"><span data-stu-id="9690f-125">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="9690f-126">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="9690f-126">Property</span></span> | <span data-ttu-id="9690f-127">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9690f-127">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="9690f-128">Ruft die eindeutige ID für die Verbindung von SignalR zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="9690f-128">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="9690f-129">Eine Verbindungs­id für jede Verbindung ist vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9690f-129">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="9690f-130">Ruft die [Benutzerbezeichner](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="9690f-130">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="9690f-131">SignalR verwendet standardmäßig die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` der Verbindung als die Benutzer-ID zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9690f-131">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="9690f-132">Ruft die `ClaimsPrincipal` mit dem aktuellen Benutzer verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="9690f-132">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="9690f-133">Ruft eine Auflistung von Schlüssel-Wert, die zum Freigeben von Daten innerhalb des Bereichs dieser Verbindung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9690f-133">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="9690f-134">Daten können in dieser Auflistung gespeichert werden, und bleiben für die Verbindung in anderen hubmethodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="9690f-134">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="9690f-135">Ruft die Auflistung der verfügbaren Funktionen für die Verbindung ab.</span><span class="sxs-lookup"><span data-stu-id="9690f-135">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="9690f-136">Vorerst ist nicht dieser Auflistung in den meisten Szenarien erforderlich, damit noch ausführlich dokumentiert ist nicht.</span><span class="sxs-lookup"><span data-stu-id="9690f-136">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="9690f-137">Ruft eine `CancellationToken` , die benachrichtigt werden, wenn die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="9690f-137">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="9690f-138">`Hub.Context` Außerdem enthält die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="9690f-138">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="9690f-139">Methode</span><span class="sxs-lookup"><span data-stu-id="9690f-139">Method</span></span> | <span data-ttu-id="9690f-140">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9690f-140">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="9690f-141">Gibt die `HttpContext` für die Verbindung oder `null` , wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="9690f-141">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="9690f-142">Für HTTP-Verbindungen können Sie diese Methode zum Abrufen von Informationen wie z. B. HTTP-Header und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="9690f-142">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="9690f-143">Bricht die Verbindung ab.</span><span class="sxs-lookup"><span data-stu-id="9690f-143">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="9690f-144">Das Objekt des Clients</span><span class="sxs-lookup"><span data-stu-id="9690f-144">The Clients object</span></span>

<span data-ttu-id="9690f-145">Die `Hub` -Klasse verfügt über eine `Clients` -Eigenschaft, die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:</span><span class="sxs-lookup"><span data-stu-id="9690f-145">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="9690f-146">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="9690f-146">Property</span></span> | <span data-ttu-id="9690f-147">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9690f-147">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="9690f-148">Ruft eine Methode für alle verbundenen clients</span><span class="sxs-lookup"><span data-stu-id="9690f-148">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="9690f-149">Ruft eine Methode auf dem Client, der die hubmethode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="9690f-149">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="9690f-150">Ruft eine Methode für alle verbundenen Clients mit Ausnahme des Clients, die die Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="9690f-150">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="9690f-151">`Hub.Clients` Außerdem enthält die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="9690f-151">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="9690f-152">Methode</span><span class="sxs-lookup"><span data-stu-id="9690f-152">Method</span></span> | <span data-ttu-id="9690f-153">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9690f-153">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="9690f-154">Ruft eine Methode für alle verbundenen Clients mit Ausnahme der angegebenen Verbindungen</span><span class="sxs-lookup"><span data-stu-id="9690f-154">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="9690f-155">Ruft eine Methode auf einem bestimmten verbundenen client</span><span class="sxs-lookup"><span data-stu-id="9690f-155">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="9690f-156">Ruft eine Methode für bestimmte verbundene clients</span><span class="sxs-lookup"><span data-stu-id="9690f-156">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="9690f-157">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe</span><span class="sxs-lookup"><span data-stu-id="9690f-157">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="9690f-158">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe an, mit Ausnahme der angegebenen Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="9690f-158">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="9690f-159">Ruft eine Methode für mehrere Gruppen von Verbindungen</span><span class="sxs-lookup"><span data-stu-id="9690f-159">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="9690f-160">Ruft eine Methode für eine Gruppe von Verbindungen mit Ausnahme des Clients, der die hubmethode aufgerufen hat</span><span class="sxs-lookup"><span data-stu-id="9690f-160">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="9690f-161">Ruft eine Methode für alle Verbindungen, die einen bestimmten Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="9690f-161">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="9690f-162">Ruft eine Methode für alle Verbindungen, die die angegebenen Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="9690f-162">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="9690f-163">Jede Eigenschaft oder Methode in den vorhergehenden Tabellen gibt ein Objekt mit einem `SendAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="9690f-163">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="9690f-164">Die `SendAsync` Methode können Sie den Namen und Parameter, der die Methode aufrufen, angeben.</span><span class="sxs-lookup"><span data-stu-id="9690f-164">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="9690f-165">Senden von Nachrichten an clients</span><span class="sxs-lookup"><span data-stu-id="9690f-165">Send messages to clients</span></span>

<span data-ttu-id="9690f-166">Um Aufrufe von bestimmten Clients zu machen, verwenden Sie die Eigenschaften der `Clients` Objekt.</span><span class="sxs-lookup"><span data-stu-id="9690f-166">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="9690f-167">Im folgenden Beispiel gibt es drei Methoden des Hubs:</span><span class="sxs-lookup"><span data-stu-id="9690f-167">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="9690f-168">`SendMessage` sendet eine Nachricht an alle verbundenen Clients, die mit `Clients.All`.</span><span class="sxs-lookup"><span data-stu-id="9690f-168">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="9690f-169">`SendMessageToCaller` sendet eine Nachricht an den Aufrufer, die mit `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="9690f-169">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="9690f-170">`SendMessageToGroups` sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.</span><span class="sxs-lookup"><span data-stu-id="9690f-170">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="9690f-171">Stark typisierte hubs</span><span class="sxs-lookup"><span data-stu-id="9690f-171">Strongly typed hubs</span></span>

<span data-ttu-id="9690f-172">Ein Nachteil der Verwendung von `SendAsync` besteht darin, dass dabei auf magische Zeichenfolge an die Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9690f-172">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="9690f-173">Dadurch wird Code öffnen, um Laufzeitfehler, wenn Sie der Methodennamen falsch geschrieben ist, oder auf dem Client fehlt.</span><span class="sxs-lookup"><span data-stu-id="9690f-173">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="9690f-174">Eine Alternative zur Verwendung `SendAsync` stark typisiert werden die `Hub` mit <xref:Microsoft.AspNetCore.SignalR.Hub`1>.</span><span class="sxs-lookup"><span data-stu-id="9690f-174">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub`1>.</span></span> <span data-ttu-id="9690f-175">Im folgenden Beispiel die `ChatHub` Clientmethoden wurden, in eine Schnittstelle namens extrahiert `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="9690f-175">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="9690f-176">Diese Schnittstelle kann verwendet werden, auf der vorherigen Umgestalten `ChatHub` Beispiel.</span><span class="sxs-lookup"><span data-stu-id="9690f-176">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="9690f-177">Mithilfe von `Hub<IChatClient>` ermöglicht während der Kompilierung der Clientmethoden überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9690f-177">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="9690f-178">Dies verhindert, dass Probleme verursacht werden, da mit der Magic-Zeichenfolgen, `Hub<T>` können nur Zugriff auf die in der Schnittstelle definierten Methoden bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9690f-178">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="9690f-179">Verwenden eines stark typisierten `Hub<T>` deaktiviert die Möglichkeit, verwenden Sie `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="9690f-179">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="9690f-180">Alle für die Schnittstelle definierten Methoden können immer noch als asynchron definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9690f-180">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="9690f-181">Jede dieser Methoden sollten in der Tat Zurückgeben einer `Task`.</span><span class="sxs-lookup"><span data-stu-id="9690f-181">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="9690f-182">Da es sich um eine Schnittstelle ist, verwenden Sie nicht die `async` Schlüsselwort.</span><span class="sxs-lookup"><span data-stu-id="9690f-182">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="9690f-183">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9690f-183">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="9690f-184">Die `Async` Suffix wird nicht aus der Name der Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="9690f-184">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="9690f-185">Wenn die Clientmethode definiert ist, mit `.on('MyMethodAsync')`, verwenden Sie nicht `MyMethodAsync` als Namen.</span><span class="sxs-lookup"><span data-stu-id="9690f-185">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="9690f-186">Ändern Sie den Namen einer Hub-Methode</span><span class="sxs-lookup"><span data-stu-id="9690f-186">Change the name of a hub method</span></span>

<span data-ttu-id="9690f-187">Standardmäßig ist ein Servername der Hub-Methode den Namen der .NET-Methode.</span><span class="sxs-lookup"><span data-stu-id="9690f-187">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="9690f-188">Sie können jedoch die [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) Attribut, um diese Standardeinstellung ändern, und geben Sie manuell einen Namen für die Methode.</span><span class="sxs-lookup"><span data-stu-id="9690f-188">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="9690f-189">Der Client sollte diesem Namen gibt, anstatt des Methodennamens von .NET verwenden, beim Aufruf der Methode.</span><span class="sxs-lookup"><span data-stu-id="9690f-189">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="9690f-190">Behandeln von Ereignissen für eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="9690f-190">Handle events for a connection</span></span>

<span data-ttu-id="9690f-191">Die SignalR-Hubs-API bietet die `OnConnectedAsync` und `OnDisconnectedAsync` virtuelle Methoden zum Verwalten und Nachverfolgen von Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="9690f-191">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="9690f-192">Überschreiben der `OnConnectedAsync` virtuelle Methode, um die Aktionen ausführen, wenn einem Client an den Hub zwischen, z. B. zu einer Gruppe hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9690f-192">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="9690f-193">Überschreiben der `OnDisconnectedAsync` virtuelle Methode, um die Aktionen ausführen, wenn ein Client die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="9690f-193">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="9690f-194">Wenn der Client die Verbindung absichtlich trennt (durch Aufrufen von `connection.stop()`, z. B.), wird die `exception` Parameter `null`.</span><span class="sxs-lookup"><span data-stu-id="9690f-194">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="9690f-195">Jedoch, wenn der Client aufgrund eines Fehlers (z. B. einem Netzwerkausfall) getrennt ist die `exception` -Parameter enthält eine Ausnahme, die den Fehler beschreibt.</span><span class="sxs-lookup"><span data-stu-id="9690f-195">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="9690f-196">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="9690f-196">Handle errors</span></span>

<span data-ttu-id="9690f-197">In den hubmethoden ausgelöste Ausnahmen werden an den Client gesendet, die die Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="9690f-197">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="9690f-198">Für JavaScript-Client die `invoke` Methode gibt eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="9690f-198">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="9690f-199">Wenn der Client einen Fehler mit einem Handler empfängt die Zusicherung mit angefügten `catch`, es wurde aufgerufen, und übergeben Sie als JavaScript- `Error` Objekt.</span><span class="sxs-lookup"><span data-stu-id="9690f-199">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="9690f-200">Wenn es sich bei Ihrem Hub eine Ausnahme auslöst, werden nicht die Verbindungen geschlossen.</span><span class="sxs-lookup"><span data-stu-id="9690f-200">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="9690f-201">Standardmäßig gibt SignalR eine generische Fehlermeldung an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="9690f-201">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="9690f-202">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9690f-202">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="9690f-203">Unerwartete Ausnahmen enthalten oft vertrauliche Informationen wie den Namen eines Datenbankservers zu einer Ausnahme wird ausgelöst, wenn die Verbindung mit der ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="9690f-203">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="9690f-204">SignalR nicht diese detaillierten Fehlermeldungen werden standardmäßig als Sicherheitsmaßnahme verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="9690f-204">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="9690f-205">Finden Sie unter den [Security Considerations Artikel](xref:signalr/security#exceptions) für Weitere Informationen darüber, warum die Details der Ausnahme unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="9690f-205">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="9690f-206">Wenn Sie haben eine außergewöhnliche Bedingung Sie *führen* an den Client weitergegeben werden soll, können Sie mit der `HubException` Klasse.</span><span class="sxs-lookup"><span data-stu-id="9690f-206">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="9690f-207">Wenn Sie Auslösen einer `HubException` aus der hubmethode, SignalR **wird** die gesamte Nachricht an den Client, unverändert gesendet.</span><span class="sxs-lookup"><span data-stu-id="9690f-207">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="9690f-208">SignalR sendet nur die `Message` -Eigenschaft der Ausnahme an den Client.</span><span class="sxs-lookup"><span data-stu-id="9690f-208">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="9690f-209">Die stapelüberwachung und andere Eigenschaften auf die Ausnahme nicht an den Client zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9690f-209">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="9690f-210">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="9690f-210">Related resources</span></span>

* [<span data-ttu-id="9690f-211">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9690f-211">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="9690f-212">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="9690f-212">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="9690f-213">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="9690f-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
