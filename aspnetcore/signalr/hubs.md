---
title: Verwenden von Hubs in ASP.net Core signalr
author: bradygaster
description: Erfahren Sie, wie Sie Hubs in ASP.net Core signalr verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 4922d6d780b18727d3ac181b94dbf75458d74fe6
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746514"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="474f2-103">Verwenden von Hubs in signalr für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="474f2-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="474f2-104">Von [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="474f2-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="474f2-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="474f2-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="474f2-106">Was ist ein signalr-Hub?</span><span class="sxs-lookup"><span data-stu-id="474f2-106">What is a SignalR hub</span></span>

<span data-ttu-id="474f2-107">Die signalr Hubs-API ermöglicht es Ihnen, Methoden auf verbundenen Clients vom Server aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="474f2-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="474f2-108">Im Servercode definieren Sie Methoden, die vom Client aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="474f2-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="474f2-109">Im Client Code definieren Sie Methoden, die vom Server aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="474f2-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="474f2-110">Signalr kümmert sich um alles hinter den Kulissen, das eine Echtzeitkommunikation zwischen Client und Server und Server-zu-Client-Kommunikation ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="474f2-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="474f2-111">Konfigurieren von signalr-Hubs</span><span class="sxs-lookup"><span data-stu-id="474f2-111">Configure SignalR hubs</span></span>

<span data-ttu-id="474f2-112">Die signalr-Middleware erfordert einige Dienste, die durch den Aufruf `services.AddSignalR`von konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="474f2-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="474f2-113">Beim Hinzufügen von signalr-Funktionen zu einer ASP.net Core-App richten Sie signalr `endpoint.MapHub` - `Startup.Configure` `app.UseEndpoints` Routen ein, indem Sie im Rückruf der Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="474f2-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="474f2-114">Beim Hinzufügen von signalr-Funktionen zu einer ASP.net Core-App richten Sie signalr- `Startup.Configure` Routen durch Aufrufen `app.UseSignalR` von in der-Methode ein.</span><span class="sxs-lookup"><span data-stu-id="474f2-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="474f2-115">Erstellen und Verwenden von Hubs</span><span class="sxs-lookup"><span data-stu-id="474f2-115">Create and use hubs</span></span>

<span data-ttu-id="474f2-116">Erstellen Sie einen Hub, indem Sie eine Klasse deklarieren `Hub`, die von erbt, und Ihr öffentliche Methoden hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="474f2-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="474f2-117">Clients können Methoden aufzurufen, die als `public`definiert sind.</span><span class="sxs-lookup"><span data-stu-id="474f2-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="474f2-118">Sie können einen Rückgabetyp und Parameter, einschließlich komplexer Typen und Arrays, wie in jeder beliebigen C# Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="474f2-119">Signalr verarbeitet die Serialisierung und Deserialisierung komplexer Objekte und Arrays in ihren Parametern und Rückgabe Werten.</span><span class="sxs-lookup"><span data-stu-id="474f2-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="474f2-120">Hubs sind flüchtig:</span><span class="sxs-lookup"><span data-stu-id="474f2-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="474f2-121">Speichern Sie den Zustand nicht in einer Eigenschaft der Hub-Klasse.</span><span class="sxs-lookup"><span data-stu-id="474f2-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="474f2-122">Jeder Hub-Methodenaufrufe wird auf einer neuen Hub-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="474f2-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="474f2-123">Verwenden `await` Sie, wenn Sie asynchrone Methoden aufrufen, die vom Hub abhängen, der aktiv bleibt.</span><span class="sxs-lookup"><span data-stu-id="474f2-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="474f2-124">Beispielsweise `Clients.All.SendAsync(...)` kann eine Methode wie z. b. fehlschlagen, wenn `await` Sie ohne aufgerufen wird und die `SendAsync` Hub-Methode vor Abschluss abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="474f2-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="474f2-125">Das Kontext Objekt</span><span class="sxs-lookup"><span data-stu-id="474f2-125">The Context object</span></span>

<span data-ttu-id="474f2-126">Die `Hub` -Klasse verfügt `Context` über eine-Eigenschaft, die die folgenden Eigenschaften mit Informationen über die Verbindung enthält:</span><span class="sxs-lookup"><span data-stu-id="474f2-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="474f2-127">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="474f2-127">Property</span></span> | <span data-ttu-id="474f2-128">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="474f2-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="474f2-129">Ruft die eindeutige ID für die Verbindung ab, die von signalr zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="474f2-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="474f2-130">Es gibt eine Verbindungs-ID für jede Verbindung.</span><span class="sxs-lookup"><span data-stu-id="474f2-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="474f2-131">Ruft den [Benutzer Bezeichner](xref:signalr/groups)ab.</span><span class="sxs-lookup"><span data-stu-id="474f2-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="474f2-132">Standardmäßig verwendet signalr den `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` , die der Verbindung zugeordnet ist, als Benutzer Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="474f2-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="474f2-133">Ruft den `ClaimsPrincipal` ab, der dem aktuellen Benutzer zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="474f2-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="474f2-134">Ruft eine Schlüssel-/Wert-Auflistung ab, die verwendet werden kann, um Daten innerhalb des Gültigkeits Bereichs dieser Verbindung freizugeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="474f2-135">Die Daten können in dieser Sammlung gespeichert werden, und Sie werden für die Verbindung über verschiedene hubmethoden Aufrufe beibehalten.</span><span class="sxs-lookup"><span data-stu-id="474f2-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="474f2-136">Ruft die Auflistung der Funktionen ab, die für die Verbindung verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="474f2-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="474f2-137">Diese Sammlung wird in den meisten Szenarien nicht benötigt, sodass Sie noch nicht ausführlich dokumentiert wird.</span><span class="sxs-lookup"><span data-stu-id="474f2-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="474f2-138">Ruft ein `CancellationToken` ab, das benachrichtigt, wenn die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="474f2-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="474f2-139">`Hub.Context`enthält auch die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="474f2-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="474f2-140">Methode</span><span class="sxs-lookup"><span data-stu-id="474f2-140">Method</span></span> | <span data-ttu-id="474f2-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="474f2-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="474f2-142">Gibt den `HttpContext` für die Verbindung zurück, `null` oder, wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="474f2-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="474f2-143">Für http-Verbindungen können Sie diese Methode verwenden, um Informationen wie HTTP-Header und Abfrage Zeichenfolgen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="474f2-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="474f2-144">Bricht die Verbindung ab.</span><span class="sxs-lookup"><span data-stu-id="474f2-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="474f2-145">Das Client-Objekt</span><span class="sxs-lookup"><span data-stu-id="474f2-145">The Clients object</span></span>

<span data-ttu-id="474f2-146">Die `Hub` -Klasse verfügt `Clients` über eine-Eigenschaft, die die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:</span><span class="sxs-lookup"><span data-stu-id="474f2-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="474f2-147">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="474f2-147">Property</span></span> | <span data-ttu-id="474f2-148">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="474f2-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="474f2-149">Ruft eine Methode auf allen verbundenen Clients auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="474f2-150">Ruft eine Methode auf dem Client auf, der die Hub-Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="474f2-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="474f2-151">Ruft eine Methode auf allen verbundenen Clients mit Ausnahme des Clients auf, der die Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="474f2-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="474f2-152">`Hub.Clients`enthält auch die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="474f2-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="474f2-153">Methode</span><span class="sxs-lookup"><span data-stu-id="474f2-153">Method</span></span> | <span data-ttu-id="474f2-154">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="474f2-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="474f2-155">Ruft eine Methode auf allen verbundenen Clients mit Ausnahme der angegebenen Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="474f2-156">Ruft eine Methode auf einem bestimmten verbundenen Client auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="474f2-157">Ruft eine Methode auf bestimmten verbundenen Clients auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="474f2-158">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="474f2-159">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe mit Ausnahme der angegebenen Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="474f2-160">Ruft eine Methode für mehrere Gruppen von Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="474f2-161">Ruft eine Methode für eine Gruppe von Verbindungen auf, ausgenommen des Clients, der die Hub-Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="474f2-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="474f2-162">Ruft eine Methode für alle Verbindungen auf, die einem bestimmten Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="474f2-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="474f2-163">Ruft für alle Verbindungen, die den angegebenen Benutzern zugeordnet sind, eine Methode auf.</span><span class="sxs-lookup"><span data-stu-id="474f2-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="474f2-164">Jede Eigenschaft oder Methode in den vorangehenden Tabellen gibt ein-Objekt `SendAsync` mit einer-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="474f2-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="474f2-165">Mit `SendAsync` der-Methode können Sie den Namen und die Parameter der aufzurufenden Client Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="474f2-166">Senden von Nachrichten an Clients</span><span class="sxs-lookup"><span data-stu-id="474f2-166">Send messages to clients</span></span>

<span data-ttu-id="474f2-167">Um Aufrufe an bestimmte Clients durchführen zu können, verwenden Sie `Clients` die Eigenschaften des-Objekts.</span><span class="sxs-lookup"><span data-stu-id="474f2-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="474f2-168">Im folgenden Beispiel gibt es drei hubmethoden:</span><span class="sxs-lookup"><span data-stu-id="474f2-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="474f2-169">`SendMessage`sendet mithilfe `Clients.All`von eine Nachricht an alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="474f2-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="474f2-170">`SendMessageToCaller`sendet mithilfe `Clients.Caller`von eine Nachricht an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="474f2-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="474f2-171">`SendMessageToGroups`sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.</span><span class="sxs-lookup"><span data-stu-id="474f2-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="474f2-172">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="474f2-172">Strongly typed hubs</span></span>

<span data-ttu-id="474f2-173">Ein Nachteil von der `SendAsync` Verwendung von besteht darin, dass eine magische Zeichenfolge verwendet wird, um die aufzurufende Client Methode anzugeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="474f2-174">Dadurch bleibt der Code für Laufzeitfehler geöffnet, wenn der Methodenname falsch geschrieben wurde oder auf dem Client fehlt.</span><span class="sxs-lookup"><span data-stu-id="474f2-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="474f2-175">Eine Alternative zur Verwendung `SendAsync` von ist die `Hub` starke Typverwendung <xref:Microsoft.AspNetCore.SignalR.Hub%601>von mit.</span><span class="sxs-lookup"><span data-stu-id="474f2-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="474f2-176">Im folgenden Beispiel wurden die `ChatHub` Client Methoden in eine Schnittstelle namens `IChatClient`extrahiert.</span><span class="sxs-lookup"><span data-stu-id="474f2-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="474f2-177">Diese Schnittstelle kann verwendet werden, um das vorherige `ChatHub` Beispiel zu umgestalten.</span><span class="sxs-lookup"><span data-stu-id="474f2-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="474f2-178">Die `Hub<IChatClient>` Verwendung von ermöglicht die Überprüfung der Client Methoden über die Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="474f2-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="474f2-179">Dies verhindert Probleme, die durch die Verwendung von magischen `Hub<T>` Zeichen folgen verursacht werden, da nur den Zugriff auf die in der-Schnittstelle definierten Methoden ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="474f2-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="474f2-180">Durch die Verwendung eines `Hub<T>` stark typisierten wird die Möglichkeit `SendAsync`zur Verwendung von deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="474f2-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="474f2-181">Alle für die Schnittstelle definierten Methoden können weiterhin als asynchron definiert werden.</span><span class="sxs-lookup"><span data-stu-id="474f2-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="474f2-182">Tatsächlich sollte jede dieser Methoden eine `Task`zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="474f2-183">Da es sich um eine Schnittstelle handelt, `async` verwenden Sie das Schlüsselwort nicht.</span><span class="sxs-lookup"><span data-stu-id="474f2-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="474f2-184">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="474f2-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="474f2-185">Das `Async` Suffix wird nicht aus dem Methodennamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="474f2-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="474f2-186">Wenn die Client Methode nicht mit `.on('MyMethodAsync')`definiert ist, sollten Sie nicht als Namen verwenden. `MyMethodAsync`</span><span class="sxs-lookup"><span data-stu-id="474f2-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="474f2-187">Ändern des Namens einer Hub-Methode</span><span class="sxs-lookup"><span data-stu-id="474f2-187">Change the name of a hub method</span></span>

<span data-ttu-id="474f2-188">Standardmäßig ist ein serverhub-Methodenname der Name der .NET-Methode.</span><span class="sxs-lookup"><span data-stu-id="474f2-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="474f2-189">Sie können jedoch das [hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) -Attribut verwenden, um diesen Standardwert zu ändern und manuell einen Namen für die Methode anzugeben.</span><span class="sxs-lookup"><span data-stu-id="474f2-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="474f2-190">Der Client sollte beim Aufrufen der-Methode anstelle des .NET-Methoden namens diesen Namen verwenden.</span><span class="sxs-lookup"><span data-stu-id="474f2-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="474f2-191">Behandeln von Ereignissen für eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="474f2-191">Handle events for a connection</span></span>

<span data-ttu-id="474f2-192">Die signalr Hubs-API stellt `OnConnectedAsync` die `OnDisconnectedAsync` virtuellen Methoden und zum Verwalten und Nachverfolgen von Verbindungen bereit.</span><span class="sxs-lookup"><span data-stu-id="474f2-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="474f2-193">Überschreiben `OnConnectedAsync` Sie die virtuelle Methode, um Aktionen auszuführen, wenn ein Client eine Verbindung mit dem Hub herstellt, z. b. das Hinzufügen zu einer Gruppe.</span><span class="sxs-lookup"><span data-stu-id="474f2-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="474f2-194">Überschreiben `OnDisconnectedAsync` Sie die virtuelle Methode, um Aktionen auszuführen, wenn ein Client die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="474f2-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="474f2-195">Wenn der Client die Verbindung absichtlich trennt (z `connection.stop()` `null`. b. durch Aufrufen von `exception` ), wird der-Parameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="474f2-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="474f2-196">Wenn der Client jedoch aufgrund eines Fehlers (z. b. eines Netzwerk Fehlers) getrennt wird, enthält `exception` der-Parameter eine Ausnahme, die den Fehler beschreibt.</span><span class="sxs-lookup"><span data-stu-id="474f2-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="474f2-197">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="474f2-197">Handle errors</span></span>

<span data-ttu-id="474f2-198">Ausnahmen, die in ihren hubmethoden ausgelöst werden, werden an den Client gesendet, der die Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="474f2-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="474f2-199">Auf dem JavaScript-Client gibt `invoke` die Methode eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)zurück.</span><span class="sxs-lookup"><span data-stu-id="474f2-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="474f2-200">Wenn der Client einen Fehler mit einem Handler empfängt, der mithilfe `catch`von verbunden ist, wird er aufgerufen und als JavaScript `Error` -Objekt übermittelt.</span><span class="sxs-lookup"><span data-stu-id="474f2-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="474f2-201">Wenn Ihr Hub eine Ausnahme auslöst, werden die Verbindungen nicht geschlossen.</span><span class="sxs-lookup"><span data-stu-id="474f2-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="474f2-202">Standardmäßig gibt signalr eine generische Fehlermeldung an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="474f2-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="474f2-203">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="474f2-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="474f2-204">Unerwartete Ausnahmen enthalten häufig vertrauliche Informationen, wie z. b. den Namen eines Datenbankservers in einer Ausnahme, die ausgelöst wird, wenn die Datenbankverbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="474f2-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="474f2-205">Diese detaillierten Fehlermeldungen werden von signalr nicht standardmäßig als Sicherheitsmaßnahme verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="474f2-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="474f2-206">Weitere Informationen dazu, warum Ausnahme Details unterdrückt werden, finden Sie im [Artikel Sicherheitsüberlegungen](xref:signalr/security#exceptions) .</span><span class="sxs-lookup"><span data-stu-id="474f2-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="474f2-207">Wenn Sie eine Ausnahme Bedingung haben, die Sie an den Client weiter *geben* möchten, können Sie die `HubException` -Klasse verwenden.</span><span class="sxs-lookup"><span data-stu-id="474f2-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="474f2-208">Wenn Sie einen `HubException` aus ihrer Hub-Methode auslösen **, sendet signalr** die gesamte Nachricht unverändert an den Client.</span><span class="sxs-lookup"><span data-stu-id="474f2-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="474f2-209">Signalr sendet die `Message` -Eigenschaft der Ausnahme nur an den Client.</span><span class="sxs-lookup"><span data-stu-id="474f2-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="474f2-210">Die Stapel Überwachung und andere Eigenschaften der Ausnahme sind für den Client nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="474f2-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="474f2-211">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="474f2-211">Related resources</span></span>

* [<span data-ttu-id="474f2-212">Einführung in ASP.net Core signalr</span><span class="sxs-lookup"><span data-stu-id="474f2-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="474f2-213">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="474f2-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="474f2-214">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="474f2-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
