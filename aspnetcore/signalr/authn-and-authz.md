---
title: Authentifizierung und Autorisierung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Authentifizierung und Autorisierung in ASP.NET Core SignalR verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/09/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e8f9dc48be780fb91bdec6ea4d579f5e4f16197b
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516940"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="6376d-103">Authentifizierung und Autorisierung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="6376d-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="6376d-104">Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6376d-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="6376d-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6376d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="6376d-106">Authentifizieren von Benutzern, die Verbindung mit einer SignalR-hub</span><span class="sxs-lookup"><span data-stu-id="6376d-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="6376d-107">SignalR kann verwendet werden, mit [ASP.NET Core-Authentifizierung](xref:security/authentication/identity) , jede Verbindung ein Benutzers zugeordnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="6376d-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="6376d-108">In einem Hub Authentifizierungsdaten über aufgerufen werden können die [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6376d-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="6376d-109">Authentifizierung kann den Hub zum Aufrufen von Methoden für alle Verbindungen mit einem Benutzer verknüpft (finden Sie unter [Verwalten von Benutzern und Gruppen in SignalR](xref:signalr/groups) Informationen).</span><span class="sxs-lookup"><span data-stu-id="6376d-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="6376d-110">Mehrere Verbindungen können einen einzelnen Benutzer zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="6376d-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="6376d-111">Folgendes ist ein Beispiel für `Startup.Configure` die SignalR und ASP.NET Core-Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="6376d-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();
    
    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="6376d-112">Die Reihenfolge, in der Sie die Middleware für die SignalR und ASP.NET Core-Authentifizierung registrieren, ist von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="6376d-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="6376d-113">Rufen Sie immer `UseAuthentication` vor `UseSignalR` , damit SignalR ein Benutzer hat auf die `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6376d-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="6376d-114">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6376d-114">Cookie authentication</span></span>

<span data-ttu-id="6376d-115">In einer browserbasierten-app ermöglicht Cookie-Authentifizierung für Ihre vorhandenen Anmeldeinformationen des Benutzers automatisch an die SignalR-Verbindungen übermittelt.</span><span class="sxs-lookup"><span data-stu-id="6376d-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="6376d-116">Wenn Sie den Browserclient zu verwenden, ist keine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6376d-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="6376d-117">Wenn der Benutzer zu Ihrer app angemeldet ist, erbt die SignalR-Verbindung automatisch diese Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="6376d-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="6376d-118">Cookies sind spezielle Browser wie Zugriffstoken gesendet, aber nicht-Browser-Clients senden können.</span><span class="sxs-lookup"><span data-stu-id="6376d-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="6376d-119">Bei Verwendung der [.NET Client](xref:signalr/dotnet-client), `Cookies` Eigenschaft kann konfiguriert werden, der `.WithUrl` Aufruf, um ein Cookie bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6376d-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="6376d-120">Verwenden der Cookieauthentifizierung vom .NET-Client erfordert jedoch die app eine API zum Austauschen von Authentifizierungsdaten für ein Cookie bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="6376d-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="6376d-121">Token-Bearer-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6376d-121">Bearer token authentication</span></span>

<span data-ttu-id="6376d-122">Der Client kann ein Zugriffstoken für den anstatt eines Cookies bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="6376d-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="6376d-123">Der Server überprüft das Token und wird verwendet, um den Benutzer zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="6376d-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="6376d-124">Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="6376d-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="6376d-125">Während der Lebensdauer der Verbindung validieren nicht der Server automatisch, um für Widerrufen von token zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="6376d-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="6376d-126">Auf dem Server Bearer-token-Authentifizierung erfolgt über die [JWT-Bearer-Middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="6376d-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="6376d-127">In JavaScript-Client das Token kann bereitgestellt werden, mithilfe der [AccessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) Option.</span><span class="sxs-lookup"><span data-stu-id="6376d-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="6376d-128">In der .NET Client besteht ein ähnliches [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die zum Konfigurieren des Tokens verwendet werden kann:</span><span class="sxs-lookup"><span data-stu-id="6376d-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="6376d-129">Die token Access-Funktion, die Sie angeben, wird aufgerufen, bevor **jeder** HTTP-Anforderung, die von SignalR.</span><span class="sxs-lookup"><span data-stu-id="6376d-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="6376d-130">Wenn Sie das Token zu erneuern, um die Verbindung aktiv bleibt, (da es beim Herstellen der Verbindung ablaufen kann) müssen, tun Sie dies innerhalb dieser Funktion und das aktualisierte Token zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6376d-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="6376d-131">Bearertoken werden in standard-Web-APIs einen HTTP-Header gesendet.</span><span class="sxs-lookup"><span data-stu-id="6376d-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="6376d-132">SignalR ist jedoch nicht möglich, diese Header in Browsern festlegen, wenn Sie einige Transporte verwenden.</span><span class="sxs-lookup"><span data-stu-id="6376d-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="6376d-133">Bei Verwendung von WebSockets "und" Server-Sent Ereignisse wird das Token als ein Abfragezeichenfolgen-Parameter übertragen.</span><span class="sxs-lookup"><span data-stu-id="6376d-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="6376d-134">Um dies auf dem Server zu unterstützen, sind zusätzliche Konfigurationsschritte erforderlich:</span><span class="sxs-lookup"><span data-stu-id="6376d-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="6376d-135">Cookies im Vergleich zu Bearer-tokens</span><span class="sxs-lookup"><span data-stu-id="6376d-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="6376d-136">Da Cookies für Browser spezifisch sind, fügt das Senden von anderen Arten von Clients Komplexität, die im Vergleich zum Senden von Bearer-Tokens.</span><span class="sxs-lookup"><span data-stu-id="6376d-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="6376d-137">Aus diesem Grund nicht die Cookie-Authentifizierung empfohlen, es sei denn, der app lediglich zum Authentifizieren von Benutzern aus dem Webclient benötigt.</span><span class="sxs-lookup"><span data-stu-id="6376d-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="6376d-138">Token-Bearer-Authentifizierung wird empfohlen, wenn Clients als Browser-Clients verwenden.</span><span class="sxs-lookup"><span data-stu-id="6376d-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="6376d-139">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6376d-139">Windows authentication</span></span>

<span data-ttu-id="6376d-140">Wenn [Windows-Authentifizierung](xref:security/authentication/windowsauth) erfolgt in Ihrer app kann SignalR, die diese Identität zum Sichern von Hubs verwenden.</span><span class="sxs-lookup"><span data-stu-id="6376d-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="6376d-141">Um Nachrichten an einzelne Benutzer zu senden, müssen Sie jedoch einen benutzerdefinierten Anbieter für die Benutzer-ID hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="6376d-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="6376d-142">Dies ist, da das System der Windows-Authentifizierung nicht den Anspruch "Name Identifier", den SignalR verwendet bereitstellt, um zu bestimmen, den Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="6376d-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="6376d-143">Fügen Sie eine neue Klasse, die implementiert `IUserIdProvider` und Abrufen von einer der Ansprüche aus dem Benutzer, die als Bezeichner zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6376d-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="6376d-144">Um beispielsweise den Anspruch "Name" verwenden (Dies ist die Windows-Benutzernamen im Format `[Domain]\[Username]`), erstellen Sie die folgende Klasse:</span><span class="sxs-lookup"><span data-stu-id="6376d-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="6376d-145">Statt `ClaimTypes.Name`, können Sie einen beliebigen Wert aus der `User` (z. B. die Windows-SID-ID, usw.).</span><span class="sxs-lookup"><span data-stu-id="6376d-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="6376d-146">Die von Ihnen ausgewählte Wert muss zwischen allen Benutzern in Ihrem System eindeutig.</span><span class="sxs-lookup"><span data-stu-id="6376d-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="6376d-147">Andernfalls konnte eine Nachricht für einen einzelnen Benutzer vorgesehen am Ende zu einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="6376d-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="6376d-148">Registrieren Sie diese Komponente in Ihre `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="6376d-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="6376d-149">Im .NET Client Windows-Authentifizierung aktiviert werden muss, durch Festlegen der [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="6376d-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="6376d-150">Windows-Authentifizierung wird nur von Browser-Clients unterstützt, wenn Sie Microsoft Internet Explorer oder Microsoft Edge verwenden.</span><span class="sxs-lookup"><span data-stu-id="6376d-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="6376d-151">Verwenden von Ansprüchen Behandlung anpassen</span><span class="sxs-lookup"><span data-stu-id="6376d-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="6376d-152">Eine app, die Benutzer authentifiziert kann SignalR-Benutzer-IDs von Ansprüchen des Benutzers abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="6376d-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="6376d-153">Um anzugeben, wie SignalR für Benutzer-IDs erstellt, das Sie verwenden, implementieren `IUserIdProvider` und registrieren Sie die Implementierung.</span><span class="sxs-lookup"><span data-stu-id="6376d-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="6376d-154">Im Beispielcode wird veranschaulicht, wie Sie Ansprüche verwenden würden, um die e-Mail-Adresse des Benutzers als die identifizierende Eigenschaft auswählen.</span><span class="sxs-lookup"><span data-stu-id="6376d-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="6376d-155">Die von Ihnen ausgewählte Wert muss zwischen allen Benutzern in Ihrem System eindeutig.</span><span class="sxs-lookup"><span data-stu-id="6376d-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="6376d-156">Andernfalls konnte eine Nachricht für einen einzelnen Benutzer vorgesehen am Ende zu einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="6376d-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="6376d-157">Die kontoregistrierung Fügt einen Anspruch mit dem Typ `ClaimsTypes.Email` mit der ASP.NET Identity-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="6376d-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="6376d-158">Registrieren Sie diese Komponente in Ihre `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6376d-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="6376d-159">Autorisieren von Benutzern für den Zugriff Hubs und hubmethoden</span><span class="sxs-lookup"><span data-stu-id="6376d-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="6376d-160">Standardmäßig können alle Methoden in einem Hub nicht authentifizierter Benutzer aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="6376d-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="6376d-161">Um die Authentifizierung erforderlich ist, gelten die [autorisieren](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut mit dem Hub:</span><span class="sxs-lookup"><span data-stu-id="6376d-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="6376d-162">Können Sie die Konstruktorargumente und die Eigenschaften der `[Authorize]` Attribut, um den Zugriff auf nur übereinstimmende bestimmte Benutzer beschränken [Autorisierungsrichtlinien](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="6376d-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="6376d-163">Wenn Sie eine benutzerdefinierte Autorisierungsrichtlinie aufgerufen haben z. B. `MyAuthorizationPolicy` können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen den Hub, mit dem folgenden Code zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="6376d-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="6376d-164">Einzelne hubmethoden haben die `[Authorize]` ebenfalls angewendet.</span><span class="sxs-lookup"><span data-stu-id="6376d-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="6376d-165">Wenn der aktuelle Benutzer die Richtlinie angewendet wird, an die Methode nicht übereinstimmt, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="6376d-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="6376d-166">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6376d-166">Additional resources</span></span>

* [<span data-ttu-id="6376d-167">Bearer-Token-Authentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6376d-167">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
