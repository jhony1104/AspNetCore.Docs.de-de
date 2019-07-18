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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Authentifizierung und Autorisierung in GrpC für ASP.net Core

Von [James Newton-King](https://twitter.com/jamesnk)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(herunterladen)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Authentifizieren von Benutzern, die einen GrpC-Dienst aufrufen

GrpC kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem-Befehl einen Benutzer zuzuordnen.

Im folgenden finden Sie ein Beispiel `Startup.Configure` für die Verwendung von GrpC und ASP.net Core Authentifizierung:

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
> Die Reihenfolge, in der Sie die ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig. `UseAuthentication` Immer und `UseAuthorization` nach und`UseRouting` vor .`UseEndpoints`

Nachdem die Authentifizierung eingerichtet wurde, kann über das `ServerCallContext`über eine GrpC-Dienst Methode auf den Benutzer zugegriffen werden.

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

Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)-bearermiddleware konfiguriert.

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

### <a name="client-certificate-authentication"></a>Client Zertifikat Authentifizierung

Ein Client kann alternativ ein Client Zertifikat für die Authentifizierung bereitstellen. Die [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf TLS-Ebene, lange vor der ASP.net Core. Wenn die Anforderung ASP.net Core eintritt, können Sie das Zertifikat mit dem Zertifikat für die [Client Zertifikat Authentifizierung](xref:security/authentication/certauth) in `ClaimsPrincipal`eine auflösen.

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

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a>Andere Authentifizierungsmechanismen

Zusätzlich zur bearertoken-und Client Zertifikat Authentifizierung sollten alle ASP.net Core unterstützten Authentifizierungsmechanismen wie OAuth, OpenID und Aushandeln mit GrpC funktionieren. Weitere Informationen zum Konfigurieren der Authentifizierung auf Serverseite finden Sie unter [ASP.net Core-Authentifizierung](xref:security/authentication/identity) .

Die Client seitige Konfiguration hängt vom verwendeten Authentifizierungsmechanismus ab. Die vorherigen Beispiele für bearertoken und Client Zertifikat Authentifizierung zeigen verschiedene Möglichkeiten, wie der GrpC-Client zum Senden von Authentifizierungs Metadaten mit GrpC-aufrufen konfiguriert werden kann:

* Stark typisierte GrpC- `HttpClient` Clients verwenden intern. Die [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler) AuthentifizierungkannunteroderdurchHinzufügenvonbenutzerdefiniertenInstanzenzukonfiguriertwerden.`HttpClient`
* Jeder GrpC-Befehl verfügt über `CallOptions` ein optionales Argument. Benutzerdefinierte Header können mithilfe der Header Auflistung der Option gesendet werden.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorisieren von Benutzern für den Zugriff auf Dienste und Dienst Methoden

Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden. Um eine Authentifizierung anzufordern, wenden Sie das [[autorisieren]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) -Attribut auf den Dienst an:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]` -Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen. Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie `MyAuthorizationPolicy`namens verfügen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Dienst zugreifen können:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Auf einzelne Dienst Methoden kann auch `[Authorize]` das-Attribut angewendet werden. Wenn der aktuelle Benutzer nicht mit den Richt **Linien für die** -Methode und die-Klasse identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:

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
