---
title: Authentifizierung und Autorisierung in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core-SignalRverwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 091cc9b2adc1f6a8fac79519884695d1c1725d2a
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880419"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="ab249-103">Authentifizierung und Autorisierung in ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="ab249-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="ab249-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ab249-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="ab249-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ab249-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a><span data-ttu-id="ab249-106">Authentifizieren von Benutzern, die sich mit einem SignalR Hub verbinden</span><span class="sxs-lookup"><span data-stu-id="ab249-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="ab249-107"> können mit [ASP.net Core Authentifizierung](xref:security/authentication/identity) verwendet werden, um einen Benutzer jeder Verbindung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="ab249-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="ab249-108">In einem Hub kann über die [hubconnectioncontext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) -Eigenschaft auf Authentifizierungsdaten zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="ab249-109">Die Authentifizierung ermöglicht dem Hub das Abrufen von Methoden für alle Verbindungen, die einem Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="ab249-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="ab249-110">Weitere Informationen finden Sie unter [Verwalten von Benutzern und Gruppen in SignalR](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="ab249-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="ab249-111">Mehrere Verbindungen können einem einzelnen Benutzer zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="ab249-112">Im folgenden finden Sie ein Beispiel für `Startup.Configure`, die SignalR und ASP.net Core Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="ab249-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

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
> <span data-ttu-id="ab249-113">Die Reihenfolge, in der Sie die SignalR und ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="ab249-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="ab249-114">Ruft vor `UseSignalR` immer `UseAuthentication` auf, damit SignalR über einen Benutzer auf dem `HttpContext`verfügt.</span><span class="sxs-lookup"><span data-stu-id="ab249-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="ab249-115">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ab249-115">Cookie authentication</span></span>

<span data-ttu-id="ab249-116">In einer browserbasierten App ermöglicht die Cookieauthentifizierung, dass Ihre vorhandenen Benutzer Anmelde Informationen automatisch an SignalR Verbindungen weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="ab249-117">Wenn Sie den Browser Client verwenden, ist keine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ab249-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="ab249-118">Wenn der Benutzer bei ihrer App angemeldet ist, erbt die SignalR Verbindung diese Authentifizierung automatisch.</span><span class="sxs-lookup"><span data-stu-id="ab249-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="ab249-119">Cookies sind eine browserspezifische Methode zum Senden von Zugriffs Token, die von nicht-Browser Clients jedoch gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="ab249-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="ab249-120">Wenn Sie den [.NET-Client](xref:signalr/dotnet-client)verwenden, kann die `Cookies`-Eigenschaft im `.WithUrl`-Befehl zum Bereitstellen eines Cookies konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="ab249-121">Die Verwendung der Cookie-Authentifizierung über den .NET-Client erfordert jedoch, dass die APP eine API zum Austauschen von Authentifizierungsdaten für ein Cookie bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="ab249-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="ab249-122">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="ab249-122">Bearer token authentication</span></span>

<span data-ttu-id="ab249-123">Der Client kann ein Zugriffs Token bereitstellen, anstatt ein Cookie zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ab249-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="ab249-124">Der Server überprüft das Token und verwendet es, um den Benutzer zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="ab249-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="ab249-125">Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ab249-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="ab249-126">Während der Lebensdauer der Verbindung wird der Server nicht automatisch neu validiert, um die tokensperrung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="ab249-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="ab249-127">Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT-bearermiddleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ab249-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="ab249-128">Im JavaScript-Client kann das Token mithilfe der [accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) -Option bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="ab249-129">Im .NET-Client gibt es eine ähnliche [accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die verwendet werden kann, um das Token zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ab249-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ab249-130">Die von Ihnen bereitgestellte zugriffstokenfunktion wird vor **jeder** http-Anforderung aufgerufen, die von SignalR.</span><span class="sxs-lookup"><span data-stu-id="ab249-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="ab249-131">Wenn Sie das Token erneuern müssen, um die Verbindung aktiv zu halten (da es während der Verbindung ablaufen kann), verwenden Sie dies innerhalb dieser Funktion, und geben Sie das aktualisierte Token zurück.</span><span class="sxs-lookup"><span data-stu-id="ab249-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="ab249-132">In Standard-Web-APIs werden bearertoken in einem HTTP-Header gesendet.</span><span class="sxs-lookup"><span data-stu-id="ab249-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="ab249-133">Allerdings kann SignalR diese Header nicht in Browsern festlegen, wenn einige Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="ab249-134">Wenn websockets und Server gesendete Ereignisse verwendet werden, wird das Token als Abfrage Zeichenfolgen-Parameter übertragen.</span><span class="sxs-lookup"><span data-stu-id="ab249-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="ab249-135">Um dies auf dem Server zu unterstützen, sind zusätzliche Konfigurationsschritte erforderlich:</span><span class="sxs-lookup"><span data-stu-id="ab249-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="ab249-136">Die Abfrage Zeichenfolge wird in Browsern verwendet, wenn eine Verbindung mit websockets und Server gesendeten Ereignissen aufgrund von Einschränkungen der Browser-API hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ab249-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="ab249-137">Bei Verwendung von HTTPS werden Abfrage Zeichen folgen Werte durch die TLS-Verbindung gesichert.</span><span class="sxs-lookup"><span data-stu-id="ab249-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="ab249-138">Viele Server protokollieren jedoch Abfrage Zeichen folgen Werte.</span><span class="sxs-lookup"><span data-stu-id="ab249-138">However, many servers log query string values.</span></span> <span data-ttu-id="ab249-139">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in ASP.net Core SignalR](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="ab249-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="ab249-140"> verwendet Header zum Übertragen von Token in Umgebungen, die diese unterstützen (z. b. die .net-und Java-Clients).</span><span class="sxs-lookup"><span data-stu-id="ab249-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="ab249-141">Cookies im Vergleich zu bearertoken</span><span class="sxs-lookup"><span data-stu-id="ab249-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="ab249-142">Cookies sind spezifisch für Browser.</span><span class="sxs-lookup"><span data-stu-id="ab249-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="ab249-143">Das Senden von anderen Arten von Clients erhöht die Komplexität im Vergleich zum Senden von bearertoken.</span><span class="sxs-lookup"><span data-stu-id="ab249-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="ab249-144">Folglich empfiehlt es sich nicht, die Cookie-Authentifizierung zu verwenden, es sei denn, die APP muss nur Benutzer vom Browser Client authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="ab249-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="ab249-145">Die bearertokenauthentifizierung ist die empfohlene Vorgehensweise, wenn andere Clients als der Browser Client verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="ab249-146">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ab249-146">Windows authentication</span></span>

<span data-ttu-id="ab249-147">Wenn die [Windows-Authentifizierung](xref:security/authentication/windowsauth) in ihrer App konfiguriert ist, können SignalR diese Identität zum Sichern von Hubs verwenden.</span><span class="sxs-lookup"><span data-stu-id="ab249-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="ab249-148">Zum Senden von Nachrichten an einzelne Benutzer müssen Sie jedoch einen benutzerdefinierten Benutzer-ID-Anbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ab249-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="ab249-149">Das Windows-Authentifizierungssystem stellt den Anspruch "namensbezeichneranspruch" nicht bereit.</span><span class="sxs-lookup"><span data-stu-id="ab249-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="ab249-150"> verwendet den-Anspruch, um den Benutzernamen zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="ab249-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="ab249-151">Fügen Sie eine neue Klasse hinzu, die `IUserIdProvider` implementiert, und rufen Sie einen der Ansprüche vom Benutzer ab, der als Bezeichner verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="ab249-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="ab249-152">Um z. b. den Anspruch "Name" (der Windows-Benutzername im Formular `[Domain]\[Username]`) zu verwenden, erstellen Sie die folgende Klasse:</span><span class="sxs-lookup"><span data-stu-id="ab249-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="ab249-153">Anstatt `ClaimTypes.Name`, können Sie einen beliebigen Wert aus dem `User` verwenden (z. b. den Windows-SID-Bezeichner usw.).</span><span class="sxs-lookup"><span data-stu-id="ab249-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="ab249-154">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="ab249-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ab249-155">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="ab249-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="ab249-156">Registrieren Sie diese Komponente in der `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ab249-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="ab249-157">Im .NET-Client muss die Windows-Authentifizierung aktiviert werden, indem die [usedefault-Anmelde](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Informationen-Eigenschaft festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="ab249-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="ab249-158">Die Windows-Authentifizierung wird nur beim Verwenden von Microsoft Internet Explorer oder Microsoft Edge vom Browser Client unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ab249-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="ab249-159">Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung</span><span class="sxs-lookup"><span data-stu-id="ab249-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="ab249-160">Eine APP, die Benutzer authentifiziert, kann SignalR Benutzer-IDs von Benutzer Ansprüchen ableiten.</span><span class="sxs-lookup"><span data-stu-id="ab249-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="ab249-161">Um anzugeben, wie SignalR Benutzer-IDs erstellt, implementieren Sie `IUserIdProvider`, und registrieren Sie die Implementierung.</span><span class="sxs-lookup"><span data-stu-id="ab249-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="ab249-162">Der Beispielcode veranschaulicht, wie Sie Ansprüche verwenden, um die e-Mail-Adresse des Benutzers als identifizierende Eigenschaft auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="ab249-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="ab249-163">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="ab249-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ab249-164">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="ab249-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="ab249-165">Die Kontoregistrierung fügt der ASP.net-Identitätsdatenbank einen Anspruch mit dem Typ `ClaimsTypes.Email` hinzu.</span><span class="sxs-lookup"><span data-stu-id="ab249-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="ab249-166">Registrieren Sie diese Komponente in ihrer `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ab249-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="ab249-167">Autorisieren von Benutzern für den Zugriff auf Hubs und hubmethoden</span><span class="sxs-lookup"><span data-stu-id="ab249-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="ab249-168">Standardmäßig können alle Methoden in einem Hub von einem nicht authentifizierten Benutzer aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="ab249-169">Um eine Authentifizierung anzufordern, wenden Sie das [Autorisierungs](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut auf den Hub an:</span><span class="sxs-lookup"><span data-stu-id="ab249-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="ab249-170">Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ab249-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="ab249-171">Wenn Sie z. b. eine benutzerdefinierte Autorisierungs Richtlinie namens `MyAuthorizationPolicy` haben, können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Hub zugreifen können</span><span class="sxs-lookup"><span data-stu-id="ab249-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="ab249-172">Für einzelne hubmethoden kann auch das `[Authorize]`-Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ab249-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="ab249-173">Wenn der aktuelle Benutzer nicht mit der auf die Methode angewendeten Richtlinie identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="ab249-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="ab249-174">Verwenden von Autorisierungs Handlern zum Anpassen der Hub-Methoden Autorisierung</span><span class="sxs-lookup"><span data-stu-id="ab249-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="ab249-175"> bietet Autorisierungs Handlern eine benutzerdefinierte Ressource, wenn eine Hub-Methode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="ab249-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="ab249-176">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="ab249-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="ab249-177">Die `HubInvocationContext` enthält die `HubCallerContext`, den Namen der aufgerufenen Hub-Methode und die Argumente für die Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="ab249-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="ab249-178">Sehen Sie sich das Beispiel für einen Chatraum an, der die Anmeldung mehrerer Organisationen über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="ab249-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="ab249-179">Jeder Benutzer mit einem Microsoft-Konto kann sich anmelden, um sich anzumelden, aber nur Mitglieder der besitzenden Organisation sollten in der Lage sein, Benutzer zu sperren oder die Chat Verläufe der Benutzer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ab249-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="ab249-180">Außerdem möchten wir möglicherweise bestimmte Funktionen von bestimmten Benutzern einschränken.</span><span class="sxs-lookup"><span data-stu-id="ab249-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="ab249-181">Wenn Sie die aktualisierten Features in ASP.net Core 3,0 verwenden, ist dies durchaus möglich.</span><span class="sxs-lookup"><span data-stu-id="ab249-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="ab249-182">Beachten Sie, dass die `DomainRestrictedRequirement` als benutzerdefinierte `IAuthorizationRequirement`fungiert.</span><span class="sxs-lookup"><span data-stu-id="ab249-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="ab249-183">Nachdem der `HubInvocationContext`-Ressourcen Parameter übergeben wurde, kann die interne Logik den Kontext untersuchen, in dem der Hub aufgerufen wird, und Entscheidungen treffen, die es dem Benutzer ermöglichen, individuelle hubmethoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ab249-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

<span data-ttu-id="ab249-184">Fügen Sie in `Startup.ConfigureServices`die neue Richtlinie hinzu, und stellen Sie die benutzerdefinierte `DomainRestrictedRequirement` Anforderung als Parameter zum Erstellen der `DomainRestricted` Richtlinie bereit.</span><span class="sxs-lookup"><span data-stu-id="ab249-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="ab249-185">Im vorherigen Beispiel ist die `DomainRestrictedRequirement`-Klasse sowohl ein `IAuthorizationRequirement` als auch eine eigene `AuthorizationHandler` für diese Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ab249-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="ab249-186">Es ist akzeptabel, diese beiden Komponenten in separate Klassen aufzuteilen, um Probleme zu trennen.</span><span class="sxs-lookup"><span data-stu-id="ab249-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="ab249-187">Ein Vorteil des Beispiel Ansatzes besteht darin, dass die `AuthorizationHandler` beim Start nicht eingefügt werden müssen, da die Anforderung und der Handler identisch sind.</span><span class="sxs-lookup"><span data-stu-id="ab249-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ab249-188">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ab249-188">Additional resources</span></span>

* [<span data-ttu-id="ab249-189">Bearertokenauthentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ab249-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="ab249-190">Ressourcenbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="ab249-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
