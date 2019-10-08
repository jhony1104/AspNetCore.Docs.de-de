---
title: Authentifizierung und Autorisierung in GrpC für ASP.net Core
author: jamesnk
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in GrpC für ASP.net Core verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/13/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: e8dd384ec43a66e56891925dcaa529085fa200c7
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999856"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Authentifizierung und Autorisierung in GrpC für ASP.net Core

Von [James Newton-King](https://twitter.com/jamesnk)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Authentifizieren von Benutzern, die einen GrpC-Dienst aufrufen

GrpC kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem-Befehl einen Benutzer zuzuordnen.

Im folgenden finden Sie ein Beispiel für `Startup.Configure`, das GrpC und ASP.net Core Authentifizierung verwendet:

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
> Die Reihenfolge, in der Sie die ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig. Nach `UseRouting` und vor `UseEndpoints` immer `UseAuthentication` und `UseAuthorization` aufgerufen werden.

Der Authentifizierungsmechanismus, den Ihre APP während eines Aufrufes verwendet, muss konfiguriert werden. Die Authentifizierungs Konfiguration wird in `Startup.ConfigureServices` hinzugefügt und unterscheidet sich je nach dem von Ihrer APP verwendeten Authentifizierungsmechanismus. Beispiele zum Sichern von ASP.net Core-apps finden Sie unter Beispiele für die [Authentifizierung](xref:security/authentication/samples).

Nachdem die Authentifizierung eingerichtet wurde, kann auf den Benutzer über eine GrpC-Dienst Methode über die `ServerCallContext` zugegriffen werden.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Bearertokenauthentifizierung

Der Client kann ein Zugriffs Token für die Authentifizierung bereitstellen. Der Server überprüft das Token und verwendet es, um den Benutzer zu identifizieren.

Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT-bearermiddleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)konfiguriert.

Im .net-GrpC-Client kann das Token mit Aufrufen als Header gesendet werden:

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

Das Konfigurieren von `ChannelCredentials` auf einem Kanal ist eine alternative Möglichkeit, das Token mit GrpC-Aufrufen an den Dienst zu senden. Die Anmelde Informationen werden jedes Mal ausgeführt, wenn ein GrpC-Aufruf erfolgt. Dadurch entfällt die Notwendigkeit, Code an mehreren Stellen zu schreiben, um das Token selbst zu übergeben.

Mit den Anmelde Informationen im folgenden Beispiel wird der Kanal so konfiguriert, dass das Token mit jedem GrpC-Befehl gesendet wird:

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
    // Channels that aren't using TLS should use ChannelCredentials.Insecure instead.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Client Zertifikat Authentifizierung

Ein Client kann alternativ ein Client Zertifikat für die Authentifizierung bereitstellen. Die [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf TLS-Ebene, lange vor der ASP.net Core. Wenn die Anforderung ASP.net Core eintritt, können Sie das Zertifikat mit dem Zertifikat für die [Client Zertifikat Authentifizierung](xref:security/authentication/certauth) in eine `ClaimsPrincipal` auflösen.

> [!NOTE]
> Der Host muss so konfiguriert werden, dass er Client Zertifikate akzeptiert. Weitere Informationen zum Akzeptieren von Client Zertifikaten in Kestrel, IIS und Azure finden [Sie unter Konfigurieren des Hosts für das](xref:security/authentication/certauth#configure-your-host-to-require-certificates) anfordern von Zertifikaten.

Im .net-GrpC-Client wird das Client Zertifikat zu `HttpClientHandler` hinzugefügt, das dann zum Erstellen des GrpC-Clients verwendet wird:

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
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Andere Authentifizierungsmechanismen

Viele ASP.net Core unterstützten Authentifizierungsmechanismen funktionieren mit GrpC:

* Azure Active Directory
* Clientzertifikat
* IdentityServer
* JWT-Token
* OAuth 2,0
* OpenID Connect
* WS-Federation

Weitere Informationen zum Konfigurieren der Authentifizierung auf dem Server finden Sie unter [ASP.net Core-Authentifizierung](xref:security/authentication/identity).

Das Konfigurieren des GrpC-Clients für die Verwendung der Authentifizierung hängt von dem verwendeten Authentifizierungsmechanismus ab. Das vorherige bearertoken und Client Zertifikat Beispiele zeigen verschiedene Möglichkeiten, wie der GrpC-Client zum Senden von Authentifizierungs Metadaten mit GrpC-aufrufen konfiguriert werden kann:

* Stark typisierte GrpC-Clients verwenden intern `HttpClient`. Die Authentifizierung kann auf [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)oder durch Hinzufügen von benutzerdefinierten [`HttpMessageHandler`-](/dotnet/api/system.net.http.httpmessagehandler) Instanzen zum `HttpClient` konfiguriert werden.
* Jeder GrpC-Rückruf verfügt über ein optionales `CallOptions`-Argument. Benutzerdefinierte Header können mithilfe der Header Auflistung der Option gesendet werden.

> [!NOTE]
> Die Windows-Authentifizierung (NTLM/Kerberos/Aushandlung) kann nicht mit GrpC verwendet werden. GrpC erfordert http/2, und http/2 unterstützt die Windows-Authentifizierung nicht.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorisieren von Benutzern für den Zugriff auf Dienste und Dienst Methoden

Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden. Um eine Authentifizierung anzufordern, wenden Sie das [[autorisieren]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf den Dienst an:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen. Wenn Sie z. b. über eine benutzerdefinierte Autorisierungs Richtlinie namens "`MyAuthorizationPolicy`" verfügen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, auf den Dienst zugreifen können.

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Für einzelne Dienst Methoden kann auch das `[Authorize]`-Attribut angewendet werden. Wenn der aktuelle Benutzer nicht **mit den Richtlinien für die** -Methode und die-Klasse identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bearertokenauthentifizierung in ASP.net Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurieren der Client Zertifikat Authentifizierung in ASP.net Core](xref:security/authentication/certauth)
