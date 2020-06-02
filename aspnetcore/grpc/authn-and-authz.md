---
<span data-ttu-id="c6f34-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c6f34-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6f34-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6f34-102">'Blazor'</span></span>
- <span data-ttu-id="c6f34-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6f34-103">'Identity'</span></span>
- <span data-ttu-id="c6f34-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6f34-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6f34-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6f34-105">'Razor'</span></span>
- <span data-ttu-id="c6f34-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6f34-106">'SignalR' uid:</span></span> 

---

# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="c6f34-107">Authentifizierung und Autorisierung in gRPC für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f34-107">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="c6f34-108">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c6f34-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c6f34-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c6f34-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="c6f34-110">Authentifizieren von Benutzern, die einen gRPC-Dienst aufrufen</span><span class="sxs-lookup"><span data-stu-id="c6f34-110">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="c6f34-111">gRPC kann mit der [ASP.NET Core-Authentifizierung](xref:security/authentication/identity) verwendet werden, um einen Benutzer mit jedem Aufruf zu verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="c6f34-111">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="c6f34-112">Nachfolgend finden Sie ein Beispiel für `Startup.Configure`, das gRPC- und ASP.NET Core-Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="c6f34-112">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="c6f34-113">Die Reihenfolge, in der Sie die ASP.NET Core-Middleware für die Authentifizierung registrieren, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="c6f34-113">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="c6f34-114">Rufen Sie `UseAuthentication` und `UseAuthorization` immer nach `UseRouting` und vor `UseEndpoints` auf.</span><span class="sxs-lookup"><span data-stu-id="c6f34-114">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="c6f34-115">Der Authentifizierungsmechanismus, den Ihre App während eines Aufrufs verwendet, muss konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-115">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="c6f34-116">Die Authentifizierungskonfiguration wird in `Startup.ConfigureServices` hinzugefügt und wird je nach dem von Ihrer App verwendeten Authentifizierungsmechanismus unterschiedlich sein.</span><span class="sxs-lookup"><span data-stu-id="c6f34-116">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="c6f34-117">Beispiele für die Sicherung von ASP.NET Core-Apps finden Sie unter [Authentifizierungsbeispiele](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="c6f34-117">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="c6f34-118">Nachdem die Authentifizierung eingerichtet wurde, kann auf den Benutzer in einer Methode des gRPC-Diensts über `ServerCallContext` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-118">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="c6f34-119">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="c6f34-119">Bearer token authentication</span></span>

<span data-ttu-id="c6f34-120">Der Client kann ein Zugriffstoken zur Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c6f34-120">The client can provide an access token for authentication.</span></span> <span data-ttu-id="c6f34-121">Der Server überprüft das Token und verwendet es zum Identifizieren des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="c6f34-121">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="c6f34-122">Auf dem Server wird die Bearertokenauthentifizierung unter Verwendung der [JWT Bearer-Middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c6f34-122">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="c6f34-123">Im .NET gRPC-Client kann das Token mit Aufrufen als Header gesendet werden:</span><span class="sxs-lookup"><span data-stu-id="c6f34-123">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="c6f34-124">Das Konfigurieren von `ChannelCredentials` für einen Kanal ist eine alternative Möglichkeit, das Token mit gRPC-Aufrufen an den Dienst zu senden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-124">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="c6f34-125">Die Anmeldeinformation wird bei jedem gRPC-Aufruf ausgeführt, wodurch vermieden wird, dass an mehreren Stellen Code geschrieben werden muss, um das Token selbst zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c6f34-125">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="c6f34-126">Die Anmeldeinformationen im folgenden Beispiel konfigurieren den Kanal derart, dass das Token bei jedem gRPC-Aufruf gesendet wird:</span><span class="sxs-lookup"><span data-stu-id="c6f34-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="c6f34-127">Clientzertifikatsauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="c6f34-127">Client certificate authentication</span></span>

<span data-ttu-id="c6f34-128">Ein Client könnte alternativ ein Clientzertifikat zur Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c6f34-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="c6f34-129">Die [Zertifikatsauthentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf der TLS-Ebene, lange bevor sie überhaupt zum ASP.NET Core gelangt.</span><span class="sxs-lookup"><span data-stu-id="c6f34-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="c6f34-130">Wenn die Anforderung in ASP.NET Core eingeht, können Sie mit dem [Clientzertifikat-Authentifizierungspaket](xref:security/authentication/certauth) das Zertifikat in ein `ClaimsPrincipal` auflösen.</span><span class="sxs-lookup"><span data-stu-id="c6f34-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="c6f34-131">Der Host muss so konfiguriert werden, dass er Clientzertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="c6f34-131">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="c6f34-132">Informationen zum Akzeptieren von Clientzertifikaten in Kestrel, IIS und Azure finden Sie unter [Konfigurieren des Hosts für die Anforderung von Zertifikaten](xref:security/authentication/certauth#configure-your-host-to-require-certificates).</span><span class="sxs-lookup"><span data-stu-id="c6f34-132">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="c6f34-133">Im .NET gRPC-Client wird das Clientzertifikat zu `HttpClientHandler` hinzugefügt, das dann zur Erstellung des gRPC-Clients verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c6f34-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="c6f34-134">Andere Authentifizierungsmechanismen</span><span class="sxs-lookup"><span data-stu-id="c6f34-134">Other authentication mechanisms</span></span>

<span data-ttu-id="c6f34-135">Viele von ASP.NET Core unterstützte Authentifizierungsmechanismen arbeiten mit gRPC:</span><span class="sxs-lookup"><span data-stu-id="c6f34-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="c6f34-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c6f34-136">Azure Active Directory</span></span>
* <span data-ttu-id="c6f34-137">Clientzertifikat</span><span class="sxs-lookup"><span data-stu-id="c6f34-137">Client Certificate</span></span>
* <span data-ttu-id="c6f34-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="c6f34-138">IdentityServer</span></span>
* <span data-ttu-id="c6f34-139">JWT-Token</span><span class="sxs-lookup"><span data-stu-id="c6f34-139">JWT Token</span></span>
* <span data-ttu-id="c6f34-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="c6f34-140">OAuth 2.0</span></span>
* <span data-ttu-id="c6f34-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="c6f34-141">OpenID Connect</span></span>
* <span data-ttu-id="c6f34-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="c6f34-142">WS-Federation</span></span>

<span data-ttu-id="c6f34-143">Weitere Informationen zum Konfigurieren der Authentifizierung auf dem Server finden Sie unter [ASP.NET Core-Authentifizierung](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="c6f34-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="c6f34-144">Die Konfiguration des gRPC-Clients für die Verwendung der Authentifizierung hängt von dem von Ihnen verwendeten Authentifizierungsmechanismus ab.</span><span class="sxs-lookup"><span data-stu-id="c6f34-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="c6f34-145">Die vorherigen Beispiele für Bearertoken und Clientzertifikate zeigen eine Reihe von Möglichkeiten, wie der gRPC-Client so konfiguriert werden kann, dass er Authentifizierungsmetadaten mit gRPC-Aufrufen sendet:</span><span class="sxs-lookup"><span data-stu-id="c6f34-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="c6f34-146">Stark typisierte gRPC-Clients verwenden intern `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c6f34-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="c6f34-147">Die Authentifizierung kann für [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) oder durch Hinzufügen von benutzerdefinierten [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler)-Instanzen zu `HttpClient` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="c6f34-148">Jeder gRPC-Aufruf hat ein optionales `CallOptions`-Argument.</span><span class="sxs-lookup"><span data-stu-id="c6f34-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="c6f34-149">Benutzerdefinierte Header können über die Headersammlung der Option gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="c6f34-150">Die Windows-Authentifizierung (NTLM/Kerberos/Negotiate) kann nicht mit gRPC verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="c6f34-151">gRPC erfordert HTTP/2, und HTTP/2 unterstützt keine Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c6f34-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="c6f34-152">Autorisieren von Benutzern zum Zugriff auf Dienste und Dienstmethoden</span><span class="sxs-lookup"><span data-stu-id="c6f34-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="c6f34-153">Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="c6f34-154">Wenden Sie das Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) auf den Dienst an, um eine Authentifizierung zu erfordern:</span><span class="sxs-lookup"><span data-stu-id="c6f34-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="c6f34-155">Sie können die Konstruktorargumente und -eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die bestimmten [Autorisierungsrichtlinien](xref:security/authorization/policies) entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c6f34-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="c6f34-156">Wenn Sie z. B. eine benutzerdefinierte Autorisierungsrichtlinie namens `MyAuthorizationPolicy` besitzen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, mit dem folgenden Code auf den Dienst zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="c6f34-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="c6f34-157">Auf einzelne Dienstmethoden kann auch das Attribut `[Authorize]` angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c6f34-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="c6f34-158">Wenn der aktuelle Benutzer nicht mit den Richtlinien übereinstimmt, die **sowohl** auf die Methode als auch auf die Klasse angewendet werden, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="c6f34-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c6f34-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c6f34-159">Additional resources</span></span>

* [<span data-ttu-id="c6f34-160">Bearertokenauthentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f34-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="c6f34-161">Konfigurieren der Clientzertifikatsauthentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f34-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
