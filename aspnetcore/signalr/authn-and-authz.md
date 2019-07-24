---
title: Authentifizierung und Autorisierung in ASP.net Core signalr
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core signalr verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 07/15/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e7e7a9fd537ba89b64c15594652a290357a00038
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412532"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="5c0f9-103">Authentifizierung und Autorisierung in ASP.net Core signalr</span><span class="sxs-lookup"><span data-stu-id="5c0f9-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5c0f9-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5c0f9-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5c0f9-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5c0f9-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="5c0f9-106">Authentifizieren von Benutzern, die sich mit einem signalr-Hub verbinden</span><span class="sxs-lookup"><span data-stu-id="5c0f9-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="5c0f9-107">Signalr kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem Verbindung einen Benutzer zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="5c0f9-108">In einem Hub kann über die [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) -Eigenschaft auf Authentifizierungsdaten zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="5c0f9-109">Die Authentifizierung ermöglicht dem Hub das Abrufen von Methoden für alle Verbindungen, die einem Benutzer zugeordnet sind (Weitere Informationen finden Sie unter [Verwalten von Benutzern und Gruppen in signalr](xref:signalr/groups) ).</span><span class="sxs-lookup"><span data-stu-id="5c0f9-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="5c0f9-110">Mehrere Verbindungen können einem einzelnen Benutzer zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="5c0f9-111">Im folgenden finden Sie ein Beispiel `Startup.Configure` für die Verwendung von signalr und ASP.net Core Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="5c0f9-112">Die Reihenfolge, in der Sie die signalr-und ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5c0f9-113">`UseAuthentication` Immer vor `UseSignalR` , damit signalr über einen Benutzer auf dem `HttpContext`verfügt.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="5c0f9-114">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-114">Cookie authentication</span></span>

<span data-ttu-id="5c0f9-115">In einer browserbasierten App ermöglicht die Cookieauthentifizierung, dass Ihre vorhandenen Benutzer Anmelde Informationen automatisch an signalr-Verbindungen weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="5c0f9-116">Wenn Sie den Browser Client verwenden, ist keine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="5c0f9-117">Wenn der Benutzer bei ihrer App angemeldet ist, erbt die signalr-Verbindung diese Authentifizierung automatisch.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="5c0f9-118">Cookies sind eine browserspezifische Methode zum Senden von Zugriffs Token, die von nicht-Browser Clients jedoch gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="5c0f9-119">Wenn Sie den [.NET-Client](xref:signalr/dotnet-client)verwenden `Cookies` , kann die-Eigenschaft im `.WithUrl` -Befehl konfiguriert werden, um ein Cookie bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="5c0f9-120">Die Verwendung der Cookie-Authentifizierung über den .NET-Client erfordert jedoch, dass die APP eine API zum Austauschen von Authentifizierungsdaten für ein Cookie bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="5c0f9-121">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-121">Bearer token authentication</span></span>

<span data-ttu-id="5c0f9-122">Der Client kann ein Zugriffs Token bereitstellen, anstatt ein Cookie zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="5c0f9-123">Der Server überprüft das Token und verwendet es, um den Benutzer zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="5c0f9-124">Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="5c0f9-125">Während der Lebensdauer der Verbindung wird der Server nicht automatisch neu validiert, um die tokensperrung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="5c0f9-126">Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)-bearermiddleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5c0f9-127">Im JavaScript-Client kann das Token mithilfe der [accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) -Option bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="5c0f9-128">Im .NET-Client gibt es eine ähnliche [accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die zum Konfigurieren des Tokens verwendet werden kann:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="5c0f9-129">Die von Ihnen bereitgestellte zugriffstokenfunktion wird vor **jeder** http-Anforderung von signalr aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="5c0f9-130">Wenn Sie das Token erneuern müssen, um die Verbindung aktiv zu halten (da es während der Verbindung ablaufen kann), verwenden Sie dies innerhalb dieser Funktion, und geben Sie das aktualisierte Token zurück.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="5c0f9-131">In Standard-Web-APIs werden bearertoken in einem HTTP-Header gesendet.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="5c0f9-132">Signalr kann diese Header jedoch nicht in Browsern festlegen, wenn einige Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="5c0f9-133">Wenn websockets und Server gesendete Ereignisse verwendet werden, wird das Token als Abfrage Zeichenfolgen-Parameter übertragen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="5c0f9-134">Um dies auf dem Server zu unterstützen, sind zusätzliche Konfigurationsschritte erforderlich:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="5c0f9-135">Cookies im Vergleich zu bearertoken</span><span class="sxs-lookup"><span data-stu-id="5c0f9-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="5c0f9-136">Da Cookies für Browser spezifisch sind, wird beim Senden von anderen Arten von Clients die Komplexität im Vergleich zum Senden von bearertoken erhöht.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="5c0f9-137">Aus diesem Grund wird die Cookie-Authentifizierung nicht empfohlen, es sei denn, die APP muss nur Benutzer über den Browser Client authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="5c0f9-138">Die bearertokenauthentifizierung ist die empfohlene Vorgehensweise, wenn andere Clients als der Browser Client verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="5c0f9-139">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-139">Windows authentication</span></span>

<span data-ttu-id="5c0f9-140">Wenn die [Windows-Authentifizierung](xref:security/authentication/windowsauth) in ihrer App konfiguriert ist, kann signalr diese Identität zum Sichern von Hubs verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="5c0f9-141">Um Nachrichten an einzelne Benutzer zu senden, müssen Sie jedoch einen benutzerdefinierten Benutzer-ID-Anbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="5c0f9-142">Dies liegt daran, dass das Windows-Authentifizierungssystem nicht den Anspruch "namens Bezeichner" bereitstellt, den signalr zum Ermitteln des Benutzernamens verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="5c0f9-143">Fügen Sie eine neue Klasse hinzu `IUserIdProvider` , die implementiert und einen der Ansprüche des Benutzers abruft, der als Bezeichner verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="5c0f9-144">Wenn Sie z. b. den Anspruch "Name" (den Windows-Benutzernamen im Formular `[Domain]\[Username]`) verwenden möchten, erstellen Sie die folgende Klasse:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="5c0f9-145">Anstelle von können Sie einen beliebigen Wert aus dem `User` (z. b. den Windows-SID-Bezeichner usw.) verwenden. `ClaimTypes.Name`</span><span class="sxs-lookup"><span data-stu-id="5c0f9-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="5c0f9-146">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5c0f9-147">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="5c0f9-148">Registrieren Sie diese Komponente in `Startup.ConfigureServices` der-Methode.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="5c0f9-149">Im .NET-Client muss die Windows-Authentifizierung aktiviert werden, indem die [usedefault-Anmelde](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Informationen-Eigenschaft festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="5c0f9-150">Die Windows-Authentifizierung wird nur beim Verwenden von Microsoft Internet Explorer oder Microsoft Edge vom Browser Client unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="5c0f9-151">Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="5c0f9-152">Eine APP, die Benutzer authentifiziert, kann signalr-Benutzer-IDs von Benutzer Ansprüchen ableiten.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="5c0f9-153">Um anzugeben, wie signalr Benutzer-IDs erstellt `IUserIdProvider` , implementieren und registrieren Sie die Implementierung.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="5c0f9-154">Der Beispielcode veranschaulicht, wie Sie Ansprüche verwenden, um die e-Mail-Adresse des Benutzers als identifizierende Eigenschaft auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="5c0f9-155">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5c0f9-156">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="5c0f9-157">Die Kontoregistrierung fügt der ASP.net-Identitäts `ClaimsTypes.Email` Datenbank einen Anspruch mit dem Typ hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="5c0f9-158">Registrieren Sie diese Komponente in `Startup.ConfigureServices`Ihrer.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="5c0f9-159">Autorisieren von Benutzern für den Zugriff auf Hubs und hubmethoden</span><span class="sxs-lookup"><span data-stu-id="5c0f9-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="5c0f9-160">Standardmäßig können alle Methoden in einem Hub von einem nicht authentifizierten Benutzer aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="5c0f9-161">Um eine Authentifizierung anzufordern, wenden Sie das [Autorisierungs](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut auf den Hub an:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="5c0f9-162">Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]` -Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5c0f9-163">Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie `MyAuthorizationPolicy` namens verfügen, können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Hub zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="5c0f9-164">Einzelne hubmethoden können auch das `[Authorize]` -Attribut anwenden.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5c0f9-165">Wenn der aktuelle Benutzer nicht mit der auf die Methode angewendeten Richtlinie identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="5c0f9-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
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

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="5c0f9-166">Verwenden von Autorisierungs Handlern zum Anpassen der Hub-Methoden Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-166">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="5c0f9-167">Signalr bietet Autorisierungs Handlern eine benutzerdefinierte Ressource, wenn eine Hub-Methode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-167">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="5c0f9-168">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-168">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="5c0f9-169">`HubInvocationContext` Der`HubCallerContext`enthält, den Namen der aufgerufenen Hub-Methode und die Argumente für die Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-169">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="5c0f9-170">Sehen Sie sich das Beispiel für einen Chatraum an, der die Anmeldung mehrerer Organisationen über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-170">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="5c0f9-171">Jeder Benutzer mit einem Microsoft-Konto kann sich anmelden, um sich anzumelden, aber nur Mitglieder der besitzenden Organisation sollten in der Lage sein, Benutzer zu sperren oder die Chat Verläufe der Benutzer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-171">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="5c0f9-172">Außerdem möchten wir möglicherweise bestimmte Funktionen von bestimmten Benutzern einschränken.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-172">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="5c0f9-173">Wenn Sie die aktualisierten Features in ASP.net Core 3,0 verwenden, ist dies durchaus möglich.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-173">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="5c0f9-174">Beachten Sie, `DomainRestrictedRequirement` dass der als Benutzer `IAuthorizationRequirement`definiert fungiert.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-174">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="5c0f9-175">Nachdem der `HubInvocationContext` Ressourcen Parameter übergeben wurde, kann die interne Logik den Kontext untersuchen, in dem der Hub aufgerufen wird, und Entscheidungen treffen, die es dem Benutzer ermöglichen, individuelle hubmethoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-175">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="5c0f9-176">Fügen `Startup.ConfigureServices`Sie in die neue Richtlinie hinzu, und `DomainRestrictedRequirement` stellen Sie die benutzerdefinierte Anforderung als `DomainRestricted` Parameter bereit, um die Richtlinie zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-176">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="5c0f9-177">Im vorherigen Beispiel ist die `DomainRestrictedRequirement` -Klasse ein `IAuthorizationRequirement` und eine eigene `AuthorizationHandler` für diese Anforderung.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-177">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="5c0f9-178">Es ist akzeptabel, diese beiden Komponenten in separate Klassen aufzuteilen, um Probleme zu trennen.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-178">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="5c0f9-179">Ein Vorteil des Beispiel Ansatzes besteht darin, dass es nicht erforderlich ist, den `AuthorizationHandler` während des Starts einzufügen, da die Anforderung und der Handler gleich sind.</span><span class="sxs-lookup"><span data-stu-id="5c0f9-179">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5c0f9-180">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5c0f9-180">Additional resources</span></span>

* [<span data-ttu-id="5c0f9-181">Bearertokenauthentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5c0f9-181">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5c0f9-182">Ressourcenbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5c0f9-182">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
