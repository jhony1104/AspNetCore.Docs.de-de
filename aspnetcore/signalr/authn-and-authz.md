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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Authentifizierung und Autorisierung in ASP.net Core signalr

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(herunterladen)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Authentifizieren von Benutzern, die sich mit einem signalr-Hub verbinden

Signalr kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem Verbindung einen Benutzer zuzuordnen. In einem Hub kann über die [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) -Eigenschaft auf Authentifizierungsdaten zugegriffen werden. Die Authentifizierung ermöglicht dem Hub das Abrufen von Methoden für alle Verbindungen, die einem Benutzer zugeordnet sind (Weitere Informationen finden Sie unter [Verwalten von Benutzern und Gruppen in signalr](xref:signalr/groups) ). Mehrere Verbindungen können einem einzelnen Benutzer zugeordnet werden.

Im folgenden finden Sie ein Beispiel `Startup.Configure` für die Verwendung von signalr und ASP.net Core Authentifizierung:

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
> Die Reihenfolge, in der Sie die signalr-und ASP.net Core Authentifizierungs Middleware registrieren, ist wichtig. `UseAuthentication` Immer vor `UseSignalR` , damit signalr über einen Benutzer auf dem `HttpContext`verfügt.

### <a name="cookie-authentication"></a>Cookie-Authentifizierung

In einer browserbasierten App ermöglicht die Cookieauthentifizierung, dass Ihre vorhandenen Benutzer Anmelde Informationen automatisch an signalr-Verbindungen weitergeleitet werden. Wenn Sie den Browser Client verwenden, ist keine zusätzliche Konfiguration erforderlich. Wenn der Benutzer bei ihrer App angemeldet ist, erbt die signalr-Verbindung diese Authentifizierung automatisch.

Cookies sind eine browserspezifische Methode zum Senden von Zugriffs Token, die von nicht-Browser Clients jedoch gesendet werden können. Wenn Sie den [.NET-Client](xref:signalr/dotnet-client)verwenden `Cookies` , kann die-Eigenschaft im `.WithUrl` -Befehl konfiguriert werden, um ein Cookie bereitzustellen. Die Verwendung der Cookie-Authentifizierung über den .NET-Client erfordert jedoch, dass die APP eine API zum Austauschen von Authentifizierungsdaten für ein Cookie bereitstellt.

### <a name="bearer-token-authentication"></a>Bearertokenauthentifizierung

Der Client kann ein Zugriffs Token bereitstellen, anstatt ein Cookie zu verwenden. Der Server überprüft das Token und verwendet es, um den Benutzer zu identifizieren. Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird. Während der Lebensdauer der Verbindung wird der Server nicht automatisch neu validiert, um die tokensperrung zu überprüfen.

Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)-bearermiddleware konfiguriert.

Im JavaScript-Client kann das Token mithilfe der [accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) -Option bereitgestellt werden.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

Im .NET-Client gibt es eine ähnliche [accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die zum Konfigurieren des Tokens verwendet werden kann:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Die von Ihnen bereitgestellte zugriffstokenfunktion wird vor **jeder** http-Anforderung von signalr aufgerufen. Wenn Sie das Token erneuern müssen, um die Verbindung aktiv zu halten (da es während der Verbindung ablaufen kann), verwenden Sie dies innerhalb dieser Funktion, und geben Sie das aktualisierte Token zurück.

In Standard-Web-APIs werden bearertoken in einem HTTP-Header gesendet. Signalr kann diese Header jedoch nicht in Browsern festlegen, wenn einige Transporte verwendet werden. Wenn websockets und Server gesendete Ereignisse verwendet werden, wird das Token als Abfrage Zeichenfolgen-Parameter übertragen. Um dies auf dem Server zu unterstützen, sind zusätzliche Konfigurationsschritte erforderlich:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Cookies im Vergleich zu bearertoken 

Da Cookies für Browser spezifisch sind, wird beim Senden von anderen Arten von Clients die Komplexität im Vergleich zum Senden von bearertoken erhöht. Aus diesem Grund wird die Cookie-Authentifizierung nicht empfohlen, es sei denn, die APP muss nur Benutzer über den Browser Client authentifizieren. Die bearertokenauthentifizierung ist die empfohlene Vorgehensweise, wenn andere Clients als der Browser Client verwendet werden.

### <a name="windows-authentication"></a>Windows-Authentifizierung

Wenn die [Windows-Authentifizierung](xref:security/authentication/windowsauth) in ihrer App konfiguriert ist, kann signalr diese Identität zum Sichern von Hubs verwenden. Um Nachrichten an einzelne Benutzer zu senden, müssen Sie jedoch einen benutzerdefinierten Benutzer-ID-Anbieter hinzufügen. Dies liegt daran, dass das Windows-Authentifizierungssystem nicht den Anspruch "namens Bezeichner" bereitstellt, den signalr zum Ermitteln des Benutzernamens verwendet.

Fügen Sie eine neue Klasse hinzu `IUserIdProvider` , die implementiert und einen der Ansprüche des Benutzers abruft, der als Bezeichner verwendet werden soll. Wenn Sie z. b. den Anspruch "Name" (den Windows-Benutzernamen im Formular `[Domain]\[Username]`) verwenden möchten, erstellen Sie die folgende Klasse:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Anstelle von können Sie einen beliebigen Wert aus dem `User` (z. b. den Windows-SID-Bezeichner usw.) verwenden. `ClaimTypes.Name`

> [!NOTE]
> Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein. Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.

Registrieren Sie diese Komponente in `Startup.ConfigureServices` der-Methode.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Im .NET-Client muss die Windows-Authentifizierung aktiviert werden, indem die [usedefault-Anmelde](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Informationen-Eigenschaft festgelegt wird:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Die Windows-Authentifizierung wird nur beim Verwenden von Microsoft Internet Explorer oder Microsoft Edge vom Browser Client unterstützt.

### <a name="use-claims-to-customize-identity-handling"></a>Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung

Eine APP, die Benutzer authentifiziert, kann signalr-Benutzer-IDs von Benutzer Ansprüchen ableiten. Um anzugeben, wie signalr Benutzer-IDs erstellt `IUserIdProvider` , implementieren und registrieren Sie die Implementierung.

Der Beispielcode veranschaulicht, wie Sie Ansprüche verwenden, um die e-Mail-Adresse des Benutzers als identifizierende Eigenschaft auszuwählen. 

> [!NOTE]
> Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein. Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Die Kontoregistrierung fügt der ASP.net-Identitäts `ClaimsTypes.Email` Datenbank einen Anspruch mit dem Typ hinzu.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registrieren Sie diese Komponente in `Startup.ConfigureServices`Ihrer.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorisieren von Benutzern für den Zugriff auf Hubs und hubmethoden

Standardmäßig können alle Methoden in einem Hub von einem nicht authentifizierten Benutzer aufgerufen werden. Um eine Authentifizierung anzufordern, wenden Sie das [Autorisierungs](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut auf den Hub an:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Sie können die Konstruktorargumente und-Eigenschaften des `[Authorize]` -Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die mit bestimmten [Autorisierungs Richtlinien](xref:security/authorization/policies)übereinstimmen. Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie `MyAuthorizationPolicy` namens verfügen, können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Hub zugreifen können:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Einzelne hubmethoden können auch das `[Authorize]` -Attribut anwenden. Wenn der aktuelle Benutzer nicht mit der auf die Methode angewendeten Richtlinie identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Verwenden von Autorisierungs Handlern zum Anpassen der Hub-Methoden Autorisierung

Signalr bietet Autorisierungs Handlern eine benutzerdefinierte Ressource, wenn eine Hub-Methode eine Autorisierung erfordert. Die Ressource ist eine Instanz von `HubInvocationContext`. `HubInvocationContext` Der`HubCallerContext`enthält, den Namen der aufgerufenen Hub-Methode und die Argumente für die Hub-Methode.

Sehen Sie sich das Beispiel für einen Chatraum an, der die Anmeldung mehrerer Organisationen über Azure Active Directory ermöglicht. Jeder Benutzer mit einem Microsoft-Konto kann sich anmelden, um sich anzumelden, aber nur Mitglieder der besitzenden Organisation sollten in der Lage sein, Benutzer zu sperren oder die Chat Verläufe der Benutzer anzuzeigen. Außerdem möchten wir möglicherweise bestimmte Funktionen von bestimmten Benutzern einschränken. Wenn Sie die aktualisierten Features in ASP.net Core 3,0 verwenden, ist dies durchaus möglich. Beachten Sie, `DomainRestrictedRequirement` dass der als Benutzer `IAuthorizationRequirement`definiert fungiert. Nachdem der `HubInvocationContext` Ressourcen Parameter übergeben wurde, kann die interne Logik den Kontext untersuchen, in dem der Hub aufgerufen wird, und Entscheidungen treffen, die es dem Benutzer ermöglichen, individuelle hubmethoden auszuführen.

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

Fügen `Startup.ConfigureServices`Sie in die neue Richtlinie hinzu, und `DomainRestrictedRequirement` stellen Sie die benutzerdefinierte Anforderung als `DomainRestricted` Parameter bereit, um die Richtlinie zu erstellen.

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

Im vorherigen Beispiel ist die `DomainRestrictedRequirement` -Klasse ein `IAuthorizationRequirement` und eine eigene `AuthorizationHandler` für diese Anforderung. Es ist akzeptabel, diese beiden Komponenten in separate Klassen aufzuteilen, um Probleme zu trennen. Ein Vorteil des Beispiel Ansatzes besteht darin, dass es nicht erforderlich ist, den `AuthorizationHandler` während des Starts einzufügen, da die Anforderung und der Handler gleich sind.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bearertokenauthentifizierung in ASP.net Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Ressourcenbasierte Autorisierung](xref:security/authorization/resourcebased)
