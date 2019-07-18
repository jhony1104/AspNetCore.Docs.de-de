---
title: Authentifizierung und Autorisierung in GrpC für ASP.net Core
author: jamesnk
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in GrpC für ASP.net Core verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/07/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 49024295e4db7976924397bb24567d92d6298562
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308773"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="5e318-103">Authentifizierung und Autorisierung in GrpC für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5e318-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="5e318-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5e318-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5e318-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5e318-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="5e318-106">Authentifizieren von Benutzern, die einen GrpC-Dienst aufrufen</span><span class="sxs-lookup"><span data-stu-id="5e318-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="5e318-107">GrpC kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem-Befehl einen Benutzer zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="5e318-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="5e318-108">Im folgenden finden Sie ein Beispiel `Startup.Configure` für die Verwendung von GrpC und ASP.net Core Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="5e318-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="5e318-109">Die Reihenfolge, in der Sie die ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="5e318-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5e318-110">`UseAuthentication` Immer und `UseAuthorization` nach und`UseRouting` vor .`UseEndpoints`</span><span class="sxs-lookup"><span data-stu-id="5e318-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="5e318-111">Nachdem die Authentifizierung eingerichtet wurde, kann über das `ServerCallContext`über eine GrpC-Dienst Methode auf den Benutzer zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="5e318-111">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="5e318-112">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e318-112">Bearer token authentication</span></span>

<span data-ttu-id="5e318-113">Der Client kann ein Zugriffs Token für die Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5e318-113">The client can provide an access token for authentication.</span></span> <span data-ttu-id="5e318-114">Der Server überprüft das Token und verwendet es, um den Benutzer zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="5e318-114">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="5e318-115">Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)-bearermiddleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5e318-115">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5e318-116">Im .net-GrpC-Client kann das Token mit Aufrufen als Header gesendet werden:</span><span class="sxs-lookup"><span data-stu-id="5e318-116">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="5e318-117">Client Zertifikat Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e318-117">Client certificate authentication</span></span>

<span data-ttu-id="5e318-118">Ein Client kann alternativ ein Client Zertifikat für die Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5e318-118">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="5e318-119">Die [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf TLS-Ebene, lange vor der ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="5e318-119">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="5e318-120">Wenn die Anforderung ASP.net Core eintritt, können Sie das Zertifikat mit dem Zertifikat für die [Client Zertifikat Authentifizierung](xref:security/authentication/certauth) in `ClaimsPrincipal`eine auflösen.</span><span class="sxs-lookup"><span data-stu-id="5e318-120">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="5e318-121">Der Host muss so konfiguriert werden, dass er Client Zertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="5e318-121">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="5e318-122">Weitere Informationen zum Akzeptieren von Client Zertifikaten in Kestrel, IIS und Azure finden [Sie unter Konfigurieren des Hosts für das](xref:security/authentication/certauth#configure-your-host-to-require-certificates) anfordern von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="5e318-122">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="5e318-123">Im .net-GrpC-Client wird das Client Zertifikat zu `HttpClientHandler` hinzugefügt, das dann zum Erstellen des GrpC-Clients verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5e318-123">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="5e318-124">Andere Authentifizierungsmechanismen</span><span class="sxs-lookup"><span data-stu-id="5e318-124">Other authentication mechanisms</span></span>

<span data-ttu-id="5e318-125">Zusätzlich zur bearertoken-und Client Zertifikat Authentifizierung sollten alle ASP.net Core unterstützten Authentifizierungsmechanismen wie OAuth, OpenID und Aushandeln mit GrpC funktionieren.</span><span class="sxs-lookup"><span data-stu-id="5e318-125">In addition to bearer token and client certificate authentication, all ASP.NET Core supported authentication mechanisms such as OAuth, OpenID and Negotiate should work with gRPC.</span></span> <span data-ttu-id="5e318-126">Weitere Informationen zum Konfigurieren der Authentifizierung auf Serverseite finden Sie unter [ASP.net Core-Authentifizierung](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="5e318-126">Visit [ASP.NET Core authentication](xref:security/authentication/identity) for more information for configuring authentication on the server side.</span></span>

<span data-ttu-id="5e318-127">Die Client seitige Konfiguration hängt vom verwendeten Authentifizierungsmechanismus ab.</span><span class="sxs-lookup"><span data-stu-id="5e318-127">Client side configuration will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="5e318-128">Die vorherigen Beispiele für bearertoken und Client Zertifikat Authentifizierung zeigen verschiedene Möglichkeiten, wie der GrpC-Client zum Senden von Authentifizierungs Metadaten mit GrpC-aufrufen konfiguriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="5e318-128">The previous bearer token and client certificate authentication examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="5e318-129">Stark typisierte GrpC- `HttpClient` Clients verwenden intern.</span><span class="sxs-lookup"><span data-stu-id="5e318-129">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="5e318-130">Die [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler) AuthentifizierungkannunteroderdurchHinzufügenvonbenutzerdefiniertenInstanzenzukonfiguriertwerden.`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="5e318-130">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="5e318-131">Jeder GrpC-Befehl verfügt über `CallOptions` ein optionales Argument.</span><span class="sxs-lookup"><span data-stu-id="5e318-131">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="5e318-132">Benutzerdefinierte Header können mithilfe der Header Auflistung der Option gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="5e318-132">Custom headers can be sent using the option's headers collection.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="5e318-133">Autorisieren von Benutzern für den Zugriff auf Dienste und Dienst Methoden</span><span class="sxs-lookup"><span data-stu-id="5e318-133">Authorize users to access services and service methods</span></span>

<span data-ttu-id="5e318-134">Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5e318-134">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="5e318-135">Um eine Authentifizierung anzufordern, wenden Sie das [[autorisieren]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf den Dienst an:</span><span class="sxs-lookup"><span data-stu-id="5e318-135">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5e318-136">Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]` -Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="5e318-136">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5e318-137">Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie `MyAuthorizationPolicy`namens verfügen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Dienst zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="5e318-137">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5e318-138">Auf einzelne Dienst Methoden kann auch `[Authorize]` das-Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5e318-138">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5e318-139">Wenn der aktuelle Benutzer nicht mit den Richt **Linien für die** -Methode und die-Klasse identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="5e318-139">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="5e318-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5e318-140">Additional resources</span></span>

* [<span data-ttu-id="5e318-141">Bearertokenauthentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5e318-141">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5e318-142">Konfigurieren der Client Zertifikat Authentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5e318-142">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
