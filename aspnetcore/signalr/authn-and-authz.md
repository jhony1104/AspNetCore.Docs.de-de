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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Authentifizierung und Autorisierung in ASP.NET Core SignalR

Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Authentifizieren von Benutzern, die Verbindung mit einer SignalR-hub

SignalR kann verwendet werden, mit [ASP.NET Core-Authentifizierung](xref:security/authentication/identity) , jede Verbindung ein Benutzers zugeordnet werden soll. In einem Hub Authentifizierungsdaten über aufgerufen werden können die [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) Eigenschaft. Authentifizierung kann den Hub zum Aufrufen von Methoden für alle Verbindungen mit einem Benutzer verknüpft (finden Sie unter [Verwalten von Benutzern und Gruppen in SignalR](xref:signalr/groups) Informationen). Mehrere Verbindungen können einen einzelnen Benutzer zugeordnet werden.

Folgendes ist ein Beispiel für `Startup.Configure` die SignalR und ASP.NET Core-Authentifizierung verwendet:

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
> Die Reihenfolge, in der Sie die Middleware für die SignalR und ASP.NET Core-Authentifizierung registrieren, ist von Bedeutung. Rufen Sie immer `UseAuthentication` vor `UseSignalR` , damit SignalR ein Benutzer hat auf die `HttpContext`.

### <a name="cookie-authentication"></a>Cookie-Authentifizierung

In einer browserbasierten-app ermöglicht Cookie-Authentifizierung für Ihre vorhandenen Anmeldeinformationen des Benutzers automatisch an die SignalR-Verbindungen übermittelt. Wenn Sie den Browserclient zu verwenden, ist keine zusätzliche Konfiguration erforderlich. Wenn der Benutzer zu Ihrer app angemeldet ist, erbt die SignalR-Verbindung automatisch diese Authentifizierung.

Cookies sind spezielle Browser wie Zugriffstoken gesendet, aber nicht-Browser-Clients senden können. Bei Verwendung der [.NET Client](xref:signalr/dotnet-client), `Cookies` Eigenschaft kann konfiguriert werden, der `.WithUrl` Aufruf, um ein Cookie bereitzustellen. Verwenden der Cookieauthentifizierung vom .NET-Client erfordert jedoch die app eine API zum Austauschen von Authentifizierungsdaten für ein Cookie bereitstellen.

### <a name="bearer-token-authentication"></a>Token-Bearer-Authentifizierung

Der Client kann ein Zugriffstoken für den anstatt eines Cookies bereitstellen. Der Server überprüft das Token und wird verwendet, um den Benutzer zu identifizieren. Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird. Während der Lebensdauer der Verbindung validieren nicht der Server automatisch, um für Widerrufen von token zu überprüfen.

Auf dem Server Bearer-token-Authentifizierung erfolgt über die [JWT-Bearer-Middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

In JavaScript-Client das Token kann bereitgestellt werden, mithilfe der [AccessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) Option.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

In der .NET Client besteht ein ähnliches [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die zum Konfigurieren des Tokens verwendet werden kann:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Die token Access-Funktion, die Sie angeben, wird aufgerufen, bevor **jeder** HTTP-Anforderung, die von SignalR. Wenn Sie das Token zu erneuern, um die Verbindung aktiv bleibt, (da es beim Herstellen der Verbindung ablaufen kann) müssen, tun Sie dies innerhalb dieser Funktion und das aktualisierte Token zurückgegeben.

Bearertoken werden in standard-Web-APIs einen HTTP-Header gesendet. SignalR ist jedoch nicht möglich, diese Header in Browsern festlegen, wenn Sie einige Transporte verwenden. Bei Verwendung von WebSockets "und" Server-Sent Ereignisse wird das Token als ein Abfragezeichenfolgen-Parameter übertragen. Um dies auf dem Server zu unterstützen, sind zusätzliche Konfigurationsschritte erforderlich:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Cookies im Vergleich zu Bearer-tokens 

Da Cookies für Browser spezifisch sind, fügt das Senden von anderen Arten von Clients Komplexität, die im Vergleich zum Senden von Bearer-Tokens. Aus diesem Grund nicht die Cookie-Authentifizierung empfohlen, es sei denn, der app lediglich zum Authentifizieren von Benutzern aus dem Webclient benötigt. Token-Bearer-Authentifizierung wird empfohlen, wenn Clients als Browser-Clients verwenden.

### <a name="windows-authentication"></a>Windows-Authentifizierung

Wenn [Windows-Authentifizierung](xref:security/authentication/windowsauth) erfolgt in Ihrer app kann SignalR, die diese Identität zum Sichern von Hubs verwenden. Um Nachrichten an einzelne Benutzer zu senden, müssen Sie jedoch einen benutzerdefinierten Anbieter für die Benutzer-ID hinzufügen. Dies ist, da das System der Windows-Authentifizierung nicht den Anspruch "Name Identifier", den SignalR verwendet bereitstellt, um zu bestimmen, den Benutzernamen ein.

Fügen Sie eine neue Klasse, die implementiert `IUserIdProvider` und Abrufen von einer der Ansprüche aus dem Benutzer, die als Bezeichner zu verwenden. Um beispielsweise den Anspruch "Name" verwenden (Dies ist die Windows-Benutzernamen im Format `[Domain]\[Username]`), erstellen Sie die folgende Klasse:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Statt `ClaimTypes.Name`, können Sie einen beliebigen Wert aus der `User` (z. B. die Windows-SID-ID, usw.).

> [!NOTE]
> Die von Ihnen ausgewählte Wert muss zwischen allen Benutzern in Ihrem System eindeutig. Andernfalls konnte eine Nachricht für einen einzelnen Benutzer vorgesehen am Ende zu einem anderen Benutzer.

Registrieren Sie diese Komponente in Ihre `Startup.ConfigureServices` Methode.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Im .NET Client Windows-Authentifizierung aktiviert werden muss, durch Festlegen der [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Eigenschaft:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows-Authentifizierung wird nur von Browser-Clients unterstützt, wenn Sie Microsoft Internet Explorer oder Microsoft Edge verwenden.

### <a name="use-claims-to-customize-identity-handling"></a>Verwenden von Ansprüchen Behandlung anpassen

Eine app, die Benutzer authentifiziert kann SignalR-Benutzer-IDs von Ansprüchen des Benutzers abgeleitet werden. Um anzugeben, wie SignalR für Benutzer-IDs erstellt, das Sie verwenden, implementieren `IUserIdProvider` und registrieren Sie die Implementierung.

Im Beispielcode wird veranschaulicht, wie Sie Ansprüche verwenden würden, um die e-Mail-Adresse des Benutzers als die identifizierende Eigenschaft auswählen. 

> [!NOTE]
> Die von Ihnen ausgewählte Wert muss zwischen allen Benutzern in Ihrem System eindeutig. Andernfalls konnte eine Nachricht für einen einzelnen Benutzer vorgesehen am Ende zu einem anderen Benutzer.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Die kontoregistrierung Fügt einen Anspruch mit dem Typ `ClaimsTypes.Email` mit der ASP.NET Identity-Datenbank.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registrieren Sie diese Komponente in Ihre `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorisieren von Benutzern für den Zugriff Hubs und hubmethoden

Standardmäßig können alle Methoden in einem Hub nicht authentifizierter Benutzer aufgerufen werden. Um die Authentifizierung erforderlich ist, gelten die [autorisieren](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut mit dem Hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Können Sie die Konstruktorargumente und die Eigenschaften der `[Authorize]` Attribut, um den Zugriff auf nur übereinstimmende bestimmte Benutzer beschränken [Autorisierungsrichtlinien](xref:security/authorization/policies). Wenn Sie eine benutzerdefinierte Autorisierungsrichtlinie aufgerufen haben z. B. `MyAuthorizationPolicy` können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen den Hub, mit dem folgenden Code zugreifen können:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

Einzelne hubmethoden haben die `[Authorize]` ebenfalls angewendet. Wenn der aktuelle Benutzer die Richtlinie angewendet wird, an die Methode nicht übereinstimmt, wird ein Fehler an den Aufrufer zurückgegeben:

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bearer-Token-Authentifizierung in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
