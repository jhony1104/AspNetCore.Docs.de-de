---
title: Verwenden von Hubs in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Hubs in ASP.net Core SignalRverwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963804"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="11e77-103">Verwenden von Hubs in SignalR für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="11e77-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="11e77-104">Von [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="11e77-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="11e77-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="11e77-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-opno-locsignalr-hub"></a><span data-ttu-id="11e77-106">Was ist ein SignalR Hub?</span><span class="sxs-lookup"><span data-stu-id="11e77-106">What is a SignalR hub</span></span>

<span data-ttu-id="11e77-107">Mit der SignalR Hubs-API können Sie Methoden auf verbundenen Clients vom Server aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="11e77-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="11e77-108">Im Servercode definieren Sie Methoden, die vom Client aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="11e77-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="11e77-109">Im Client Code definieren Sie Methoden, die vom Server aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="11e77-109">In the client code, you define methods that are called from the server.</span></span> SignalR<span data-ttu-id="11e77-110"> kümmert sich um alles hinter den Kulissen, das die Kommunikation zwischen Client und Server und Server-zu-Client-Kommunikation ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="11e77-110"> takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-opno-locsignalr-hubs"></a><span data-ttu-id="11e77-111">Konfigurieren von SignalR Hubs</span><span class="sxs-lookup"><span data-stu-id="11e77-111">Configure SignalR hubs</span></span>

<span data-ttu-id="11e77-112">Die SignalR Middleware erfordert einige Dienste, die durch Aufrufen von `services.AddSignalR`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="11e77-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11e77-113">Wenn Sie einer ASP.net Core-App SignalR-Funktionalität hinzufügen, richten Sie die Routen ein SignalR, indem Sie `endpoint.MapHub` im `Startup.Configure` Rückruf der `app.UseEndpoints` Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="11e77-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="11e77-114">Wenn Sie einer ASP.net Core-App SignalR-Funktionalität hinzufügen, richten Sie SignalR Routen durch Aufrufen von `app.UseSignalR` in der `Startup.Configure`-Methode ein.</span><span class="sxs-lookup"><span data-stu-id="11e77-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="11e77-115">Erstellen und Verwenden von Hubs</span><span class="sxs-lookup"><span data-stu-id="11e77-115">Create and use hubs</span></span>

<span data-ttu-id="11e77-116">Erstellen Sie einen Hub, indem Sie eine Klasse deklarieren, die von `Hub`erbt, und fügen Sie Ihr öffentliche Methoden hinzu.</span><span class="sxs-lookup"><span data-stu-id="11e77-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="11e77-117">Clients können Methoden aufzurufen, die als `public`definiert sind.</span><span class="sxs-lookup"><span data-stu-id="11e77-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="11e77-118">Sie können einen Rückgabetyp und Parameter, einschließlich komplexer Typen und Arrays, wie in jeder beliebigen C# Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> SignalR<span data-ttu-id="11e77-119"> behandelt die Serialisierung und Deserialisierung komplexer Objekte und Arrays in ihren Parametern und Rückgabe Werten.</span><span class="sxs-lookup"><span data-stu-id="11e77-119"> handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="11e77-120">Hubs sind flüchtig:</span><span class="sxs-lookup"><span data-stu-id="11e77-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="11e77-121">Speichern Sie den Zustand nicht in einer Eigenschaft der Hub-Klasse.</span><span class="sxs-lookup"><span data-stu-id="11e77-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="11e77-122">Jeder Hub-Methodenaufrufe wird auf einer neuen Hub-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="11e77-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="11e77-123">Verwenden Sie `await`, wenn Sie asynchrone Methoden aufrufen, die vom Hub abhängen, der aktiv bleibt.</span><span class="sxs-lookup"><span data-stu-id="11e77-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="11e77-124">Beispielsweise kann eine Methode wie `Clients.All.SendAsync(...)` fehlschlagen, wenn Sie ohne `await` aufgerufen wird und die Hub-Methode abgeschlossen ist, bevor `SendAsync` abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="11e77-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="11e77-125">Das Kontext Objekt</span><span class="sxs-lookup"><span data-stu-id="11e77-125">The Context object</span></span>

<span data-ttu-id="11e77-126">Die `Hub`-Klasse verfügt über eine `Context`-Eigenschaft mit den folgenden Eigenschaften, die Informationen über die Verbindung enthalten:</span><span class="sxs-lookup"><span data-stu-id="11e77-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="11e77-127">property</span><span class="sxs-lookup"><span data-stu-id="11e77-127">Property</span></span> | <span data-ttu-id="11e77-128">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="11e77-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="11e77-129">Ruft die eindeutige ID für die Verbindung ab, die von SignalRzugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="11e77-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="11e77-130">Es gibt eine Verbindungs-ID für jede Verbindung.</span><span class="sxs-lookup"><span data-stu-id="11e77-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="11e77-131">Ruft den [Benutzer Bezeichner](xref:signalr/groups)ab.</span><span class="sxs-lookup"><span data-stu-id="11e77-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="11e77-132">Standardmäßig verwendet SignalR die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal`, die der Verbindung zugeordnet ist, als Benutzer Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="11e77-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="11e77-133">Ruft den `ClaimsPrincipal` ab, der dem aktuellen Benutzer zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="11e77-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="11e77-134">Ruft eine Schlüssel-/Wert-Auflistung ab, die verwendet werden kann, um Daten innerhalb des Gültigkeits Bereichs dieser Verbindung freizugeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="11e77-135">Die Daten können in dieser Sammlung gespeichert werden, und Sie werden für die Verbindung über verschiedene hubmethoden Aufrufe beibehalten.</span><span class="sxs-lookup"><span data-stu-id="11e77-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="11e77-136">Ruft die Auflistung der Funktionen ab, die für die Verbindung verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="11e77-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="11e77-137">Diese Sammlung wird in den meisten Szenarien nicht benötigt, sodass Sie noch nicht ausführlich dokumentiert wird.</span><span class="sxs-lookup"><span data-stu-id="11e77-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="11e77-138">Ruft ein `CancellationToken` ab, das benachrichtigt, wenn die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="11e77-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="11e77-139">`Hub.Context` enthält auch die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="11e77-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="11e77-140">Methode</span><span class="sxs-lookup"><span data-stu-id="11e77-140">Method</span></span> | <span data-ttu-id="11e77-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="11e77-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="11e77-142">Gibt die `HttpContext` für die Verbindung zurück, oder `null`, wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="11e77-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="11e77-143">Für http-Verbindungen können Sie diese Methode verwenden, um Informationen wie HTTP-Header und Abfrage Zeichenfolgen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="11e77-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="11e77-144">Bricht die Verbindung ab.</span><span class="sxs-lookup"><span data-stu-id="11e77-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="11e77-145">Das Client-Objekt</span><span class="sxs-lookup"><span data-stu-id="11e77-145">The Clients object</span></span>

<span data-ttu-id="11e77-146">Die `Hub`-Klasse verfügt über eine `Clients`-Eigenschaft, die die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:</span><span class="sxs-lookup"><span data-stu-id="11e77-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="11e77-147">property</span><span class="sxs-lookup"><span data-stu-id="11e77-147">Property</span></span> | <span data-ttu-id="11e77-148">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="11e77-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="11e77-149">Ruft eine Methode auf allen verbundenen Clients auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="11e77-150">Ruft eine Methode auf dem Client auf, der die Hub-Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="11e77-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="11e77-151">Ruft eine Methode auf allen verbundenen Clients mit Ausnahme des Clients auf, der die Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="11e77-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="11e77-152">`Hub.Clients` enthält auch die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="11e77-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="11e77-153">Methode</span><span class="sxs-lookup"><span data-stu-id="11e77-153">Method</span></span> | <span data-ttu-id="11e77-154">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="11e77-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="11e77-155">Ruft eine Methode auf allen verbundenen Clients mit Ausnahme der angegebenen Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="11e77-156">Ruft eine Methode auf einem bestimmten verbundenen Client auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="11e77-157">Ruft eine Methode auf bestimmten verbundenen Clients auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="11e77-158">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="11e77-159">Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe mit Ausnahme der angegebenen Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="11e77-160">Ruft eine Methode für mehrere Gruppen von Verbindungen auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="11e77-161">Ruft eine Methode für eine Gruppe von Verbindungen auf, ausgenommen des Clients, der die Hub-Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="11e77-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="11e77-162">Ruft eine Methode für alle Verbindungen auf, die einem bestimmten Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="11e77-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="11e77-163">Ruft für alle Verbindungen, die den angegebenen Benutzern zugeordnet sind, eine Methode auf.</span><span class="sxs-lookup"><span data-stu-id="11e77-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="11e77-164">Jede Eigenschaft oder Methode in den vorangehenden Tabellen gibt ein-Objekt mit einer `SendAsync`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="11e77-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="11e77-165">Mit der `SendAsync`-Methode können Sie den Namen und die Parameter der aufzurufenden Client Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="11e77-166">Senden von Nachrichten an Clients</span><span class="sxs-lookup"><span data-stu-id="11e77-166">Send messages to clients</span></span>

<span data-ttu-id="11e77-167">Um Aufrufe an bestimmte Clients durchführen zu können, verwenden Sie die Eigenschaften des `Clients` Objekts.</span><span class="sxs-lookup"><span data-stu-id="11e77-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="11e77-168">Im folgenden Beispiel gibt es drei hubmethoden:</span><span class="sxs-lookup"><span data-stu-id="11e77-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="11e77-169">`SendMessage` sendet mithilfe `Clients.All`eine Nachricht an alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="11e77-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="11e77-170">`SendMessageToCaller` sendet eine Nachricht mithilfe `Clients.Caller`an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="11e77-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="11e77-171">`SendMessageToGroups` sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.</span><span class="sxs-lookup"><span data-stu-id="11e77-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="11e77-172">Stark typisierte Hubs</span><span class="sxs-lookup"><span data-stu-id="11e77-172">Strongly typed hubs</span></span>

<span data-ttu-id="11e77-173">Ein Nachteil der Verwendung von `SendAsync` besteht darin, dass eine magische Zeichenfolge verwendet wird, um die aufzurufende Client Methode anzugeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="11e77-174">Dadurch bleibt der Code für Laufzeitfehler geöffnet, wenn der Methodenname falsch geschrieben wurde oder auf dem Client fehlt.</span><span class="sxs-lookup"><span data-stu-id="11e77-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="11e77-175">Eine Alternative zur Verwendung von `SendAsync` besteht darin, die `Hub` mit <xref:Microsoft.AspNetCore.SignalR.Hub%601>stark einzugeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="11e77-176">Im folgenden Beispiel wurden die `ChatHub` Client Methoden in eine Schnittstelle mit dem Namen `IChatClient`extrahiert.</span><span class="sxs-lookup"><span data-stu-id="11e77-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="11e77-177">Diese Schnittstelle kann verwendet werden, um das vorangehende `ChatHub` Beispiel zu umgestalten.</span><span class="sxs-lookup"><span data-stu-id="11e77-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="11e77-178">Die Verwendung von `Hub<IChatClient>` ermöglicht die Überprüfung der Client Methoden über die Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="11e77-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="11e77-179">Dies verhindert Probleme, die durch die Verwendung von magischen Zeichen folgen verursacht werden, da `Hub<T>` nur den Zugriff auf die in der Schnittstelle definierten Methoden bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="11e77-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="11e77-180">Durch die Verwendung eines stark typisierten `Hub<T>` wird die Verwendung `SendAsync`deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="11e77-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="11e77-181">Alle für die Schnittstelle definierten Methoden können weiterhin als asynchron definiert werden.</span><span class="sxs-lookup"><span data-stu-id="11e77-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="11e77-182">Tatsächlich sollte jede dieser Methoden eine `Task`zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="11e77-183">Da es sich um eine Schnittstelle handelt, verwenden Sie das `async`-Schlüsselwort nicht.</span><span class="sxs-lookup"><span data-stu-id="11e77-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="11e77-184">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11e77-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="11e77-185">Das `Async` Suffix wird nicht aus dem Methodennamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="11e77-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="11e77-186">Wenn die Client Methode nicht mit `.on('MyMethodAsync')`definiert ist, sollten Sie `MyMethodAsync` nicht als Namen verwenden.</span><span class="sxs-lookup"><span data-stu-id="11e77-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="11e77-187">Ändern des Namens einer Hub-Methode</span><span class="sxs-lookup"><span data-stu-id="11e77-187">Change the name of a hub method</span></span>

<span data-ttu-id="11e77-188">Standardmäßig ist ein serverhub-Methodenname der Name der .NET-Methode.</span><span class="sxs-lookup"><span data-stu-id="11e77-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="11e77-189">Sie können jedoch das [hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) -Attribut verwenden, um diesen Standardwert zu ändern und manuell einen Namen für die Methode anzugeben.</span><span class="sxs-lookup"><span data-stu-id="11e77-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="11e77-190">Der Client sollte beim Aufrufen der-Methode anstelle des .NET-Methoden namens diesen Namen verwenden.</span><span class="sxs-lookup"><span data-stu-id="11e77-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="11e77-191">Behandeln von Ereignissen für eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="11e77-191">Handle events for a connection</span></span>

<span data-ttu-id="11e77-192">Die SignalR Hubs-API stellt die `OnConnectedAsync` und `OnDisconnectedAsync` virtuellen Methoden zum Verwalten und Nachverfolgen von Verbindungen bereit.</span><span class="sxs-lookup"><span data-stu-id="11e77-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="11e77-193">Überschreiben Sie die virtuelle `OnConnectedAsync`-Methode, um Aktionen auszuführen, wenn ein Client eine Verbindung mit dem Hub herstellt, z. b. das Hinzufügen zu einer Gruppe</span><span class="sxs-lookup"><span data-stu-id="11e77-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="11e77-194">Überschreiben Sie die virtuelle `OnDisconnectedAsync`-Methode, um Aktionen auszuführen, wenn ein Client die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="11e77-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="11e77-195">Wenn der Client die Verbindung absichtlich trennt (z. b. durch Aufrufen von `connection.stop()`), wird der `exception` Parameter `null`.</span><span class="sxs-lookup"><span data-stu-id="11e77-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="11e77-196">Wenn der Client jedoch aufgrund eines Fehlers (z. b. eines Netzwerk Fehlers) getrennt wird, enthält der `exception` Parameter eine Ausnahme, die den Fehler beschreibt.</span><span class="sxs-lookup"><span data-stu-id="11e77-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="11e77-197">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="11e77-197">Handle errors</span></span>

<span data-ttu-id="11e77-198">Ausnahmen, die in ihren hubmethoden ausgelöst werden, werden an den Client gesendet, der die Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="11e77-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="11e77-199">Auf dem JavaScript-Client gibt die `invoke`-Methode eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)zurück.</span><span class="sxs-lookup"><span data-stu-id="11e77-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="11e77-200">Wenn der Client einen Fehler mit einem Handler empfängt, der mithilfe von `catch`an die Zusage angefügt ist, wird er aufgerufen und als JavaScript-`Error` Objekt übermittelt.</span><span class="sxs-lookup"><span data-stu-id="11e77-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="11e77-201">Wenn Ihr Hub eine Ausnahme auslöst, werden die Verbindungen nicht geschlossen.</span><span class="sxs-lookup"><span data-stu-id="11e77-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="11e77-202">Standardmäßig gibt SignalR eine generische Fehlermeldung an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="11e77-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="11e77-203">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11e77-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="11e77-204">Unerwartete Ausnahmen enthalten häufig vertrauliche Informationen, wie z. b. den Namen eines Datenbankservers in einer Ausnahme, die ausgelöst wird, wenn die Datenbankverbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="11e77-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> SignalR<span data-ttu-id="11e77-205"> diese detaillierten Fehlermeldungen nicht standardmäßig als Sicherheitsmaßnahme verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="11e77-205"> doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="11e77-206">Weitere Informationen dazu, warum Ausnahme Details unterdrückt werden, finden Sie im [Artikel Sicherheitsüberlegungen](xref:signalr/security#exceptions) .</span><span class="sxs-lookup"><span data-stu-id="11e77-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="11e77-207">Wenn Sie eine Ausnahme Bedingung haben, die Sie an den Client weiter *geben* möchten, können Sie die `HubException`-Klasse verwenden.</span><span class="sxs-lookup"><span data-stu-id="11e77-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="11e77-208">Wenn Sie eine `HubException` aus der Hub-Methode auslösen, **sendet SignalR die** gesamte Nachricht unverändert an den Client.</span><span class="sxs-lookup"><span data-stu-id="11e77-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR<span data-ttu-id="11e77-209"> sendet nur die `Message`-Eigenschaft der Ausnahme an den Client.</span><span class="sxs-lookup"><span data-stu-id="11e77-209"> only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="11e77-210">Die Stapel Überwachung und andere Eigenschaften der Ausnahme sind für den Client nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="11e77-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="11e77-211">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="11e77-211">Related resources</span></span>

* <span data-ttu-id="11e77-212">[Einführung in ASP.net Core SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="11e77-212">[Intro to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>
* [<span data-ttu-id="11e77-213">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="11e77-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="11e77-214">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="11e77-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
