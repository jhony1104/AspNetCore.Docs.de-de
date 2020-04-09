---
title: Authentifizierung und Autorisierung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie die Szenarien für die Authentifizierung und Autorisierung in Blazor kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501316"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>Authentifizierung und Autorisierung in ASP.NET Core Blazor

Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Für alle Schritte in diesem Artikel, die für Blazor WebAssembly gelten, ist Blazor WebAssembly von ASP.NET Core in der Vorlagenversion 3.2 oder höher erforderlich. Wenn Sie nicht Visual Studio, Version 16.6 Preview 2 oder höher verwenden, müssen Sie die neueste Blazor WebAssembly-Vorlage entsprechend der Anleitung unter <xref:blazor/get-started> abrufen.

ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.

Es gibt jeweils unterschiedliche Sicherheitsszenarien für Blazor Server- und Blazor WebAssembly-Apps. Da Blazor Server-Apps auf dem Server ausgeführt werden, kann durch Autorisierungsprüfungen Folgendes bestimmt werden:

* Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).
* Zugriffsregeln für Bereiche und Komponenten der App.

Blazor WebAssembly-Apps werden auf dem Client ausgeführt. Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden. Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine Blazor WebAssembly-App keine Autorisierungszugriffsregeln durchsetzen.

[Autorisierungskonventionen von Razor Pages](xref:security/authorization/razor-pages-authorization) gelten nicht für routingfähige Razor-Komponenten. Wenn eine nicht routingfähige Razor-Komponente [auf einer Seite eingebettet](xref:blazor/integrate-components#render-components-from-a-page-or-view) wird, wirken sich die Autorisierungskonventionen der Seite indirekt auf die Razor-Komponenten sowie auf den Rest des Seiteninhalts aus.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> und <xref:Microsoft.AspNetCore.Identity.UserManager%601> werden in Razor-Komponenten nicht unterstützt.

## <a name="authentication"></a>Authentifizierung

Blazor verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen. Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird: über Blazor WebAssembly oder Blazor Server.

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor WebAssembly-Authentifizierung

In den Blazor WebAssembly-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann. Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.

Fügen Sie Folgendes hinzu:

* Einen Paketverweis auf [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) in der Projektdatei der App
* Den Namespace `Microsoft.AspNetCore.Components.Authorization` für die Datei *_Imports.razor* der App

Für die Verarbeitung der Authentifizierung wird die Implementierung eines integrierten oder benutzerdefinierten Diensts `AuthenticationStateProvider` in den folgenden Abschnitten behandelt.

Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:security/blazor/webassembly/index>.

### <a name="opno-locblazor-server-authentication"></a>Blazor-Serverauthentifizierung

Blazor Server-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde. Die [Authentifizierung in SignalR-basierten Apps wird verarbeitet](xref:signalr/authn-and-authz), wenn die Verbindung hergestellt wird. Die Authentifizierung kann auf einem Cookie oder einem anderen Bearertoken basieren.

Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:security/blazor/server>.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider-Dienst

Der integrierte Dienst `AuthenticationStateProvider` ruft die Daten zum Authentifizierungsstatus von `HttpContext.User` von ASP. NET Core ab. Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende Authentifizierungsmechanismen von ASP.NET Core integrieren.

`AuthenticationStateProvider` ist der zugrunde liegende Dienst, der von der `AuthorizeView`- und der `CascadingAuthenticationState`-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.

`AuthenticationStateProvider` wird in der Regel nicht direkt verwendet. Verwenden Sie einen der später in diesem Artikel beschriebenen Ansätze ([AuthorizeView-Komponente](#authorizeview-component) oder [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter)). Der Hauptnachteil bei der direkten Verwendung von `AuthenticationStateProvider` ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.

Der Dienst `AuthenticationStateProvider` kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/dependency-injection> und <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementieren eines benutzerdefinierten AuthenticationStateProvider

Wenn die App einen benutzerdefinierten Anbieter erfordert, implementieren Sie `AuthenticationStateProvider`, und überschreiben Sie `GetAuthenticationStateAsync`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

In einer Blazor-WebAssembly-App wird der `CustomAuthStateProvider`-Dienst in `Main` von *Program.cs* registriert:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

In einer Blazor Server-App ist der Dienst `CustomAuthStateProvider` in `Startup.ConfigureServices` registriert:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bei der Verwendung von `CustomAuthStateProvider` im vorherigen Beispiel werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter

Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<AuthenticationState>` definieren:

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Richten Sie den kaskadierenden Parameter `Task<AuthenticationState>` mit den Komponenten `AuthorizeRouteView` und `CascadingAuthenticationState` in der Komponente `App`(*App.razor*) ein:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Fügen Sie `Program.Main` in einer Blazor WebAssembly-App Dienste für Optionen und die Autorisierung hinzu:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

In einer Blazor Server-App sind Dienste für Optionen und die Autorisierung bereits vorhanden. Es ist also keine weitere Aktion erforderlich.

## <a name="authorization"></a>Autorisierung

Nachdem ein Benutzer authentifiziert wurde, werden *Autorisierungsregeln* angewendet, um zu steuern, welche Funktionen der Benutzer ausführen kann.

In der Regel wird der Zugriff in Abhängigkeit von folgenden Punkten gewährt oder verweigert:

* Ein Benutzer ist authentifiziert (angemeldet).
* Ein Benutzer hat eine *Rolle*.
* Ein Benutzer hat einen *Anspruch*.
* Ein *Richtlinie* wird erfüllt.

Jedes dieser Konzepte entspricht dem einer ASP.NET Core MVC- oder Razor Pages-App. Weitere Informationen zu ASP.NET Core-Sicherheit finden Sie im Artikel unter [ASP.NET Core-Sicherheit und -Identität](xref:security/index).

## <a name="authorizeview-component"></a>AuthorizeView-Komponente

Die `AuthorizeView`-Komponente zeigt selektiv die Benutzeroberfläche an, abhängig davon, ob der Benutzer berechtigt ist, diese zu sehen. Dieser Ansatz ist nützlich, wenn Sie nur Daten für den Benutzer *anzeigen* müssen und nicht die Identität des Benutzers in der prozeduralen Logik verwenden müssen.

Die Komponente macht eine Variable `context` vom Typ `AuthenticationState` verfügbar, mit der Sie auf Informationen über den angemeldeten Benutzer zugreifen können:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht authentifiziert ist:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

Die Komponente `AuthorizeView` kann in der Komponente `NavMenu` (*Shared/NavMenu.razor*) verwendet werden, um ein Listenelement (`<li>...</li>`) für `NavLink` anzuzeigen. Beachten Sie jedoch, dass bei diesem Ansatz nur das Listenelement aus der gerenderten Ausgabe entfernt wird. Benutzer werden nicht daran gehindert, zur Komponente zu navigieren.

Der Inhalt der Tags `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.

Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.

Wenn keine Autorisierungsbedingungen angegeben sind, verwendet `AuthorizeView` eine Standardrichtlinie und behandelt:

* Authentifizierte (angemeldete) Benutzer als autorisiert.
* Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.

### <a name="role-based-and-policy-based-authorization"></a>Rollenbasierte und richtlinienbasierte Autorisierung

Die `AuthorizeView`-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.

Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Weitere Informationen finden Sie unter <xref:security/authorization/roles>.

Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Die anspruchsbasierte Autorisierung ist ein Sonderfall der richtlinienbasierten Autorisierung. Beispielsweise können Sie eine Richtlinie definieren, die vom Benutzer einen bestimmten Anspruch erfordert. Weitere Informationen finden Sie unter <xref:security/authorization/policies>.

Diese APIs können entweder in Blazor Server- oder in Blazor WebAssembly-Apps verwendet werden.

Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `AuthorizeView` die Standardrichtlinie.

### <a name="content-displayed-during-asynchronous-authentication"></a>Während der asynchronen Authentifizierung angezeigter Inhalt

Bei Blazor kann der Authentifizierungsstatus *asynchron* bestimmt werden. Das primäre Szenario für diesen Ansatz sind Blazor WebAssembly-Apps, die eine Anforderung zur Authentifizierung an einen externen Endpunkt stellen.

Während der Authentifizierung zeigt `AuthorizeView` standardmäßig keine Inhalte an. Um während der Authentifizierung Inhalte anzuzeigen, wenden Sie das `<Authorizing>`-Element:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Dieser Ansatz gilt in der Regel nicht für Blazor Server-Apps. Blazor Server-Apps kennen den Authentifizierungsstatus, sobald der Status hergestellt ist. `Authorizing`-Inhalte können zwar in der `AuthorizeView`-Komponente einer Blazor Server-App bereitgestellt werden, aber der Inhalt wird nie angezeigt.

## <a name="authorize-attribute"></a>[Authorize]-Attribut

Das Attribut `[Authorize]` kann in Razor-Komponenten verwendet werden:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Verwenden Sie nur `[Authorize]` für `@page`-Komponenten, die über den Blazor-Router erreicht werden. Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden. Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen `AuthorizeView`.

Das `[Authorize]`-Attribut unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung. Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `[Authorize]` die Standardrichtlinie und behandelt:

* Authentifizierte (angemeldete) Benutzer als autorisiert.
* Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Anpassen von unautorisierten Inhalten mit der Router-Komponente

Gemeinsam mit der `AuthorizeRouteView`-Komponente ermöglicht die `Router`-Komponente der App, benutzerdefinierten Inhalt anzugeben, wenn:

* Inhalt nicht gefunden wird.
* Der Benutzer eine `[Authorize]`-Bedingung nicht erfüllt, die für die Komponente angewendet wird. Das `[Authorize]`-Attribut wird im Abschnitt [`[Authorize]`-Attribut](#authorize-attribute) behandelt.
* Die Asynchrone Authentifizierung ausgeführt wird.

In der Standardprojektvorlage für Blazor Server zeigt die Komponente `App` (*App.razor*), wie benutzerdefinierte Inhalte festgelegt werden können:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Der Inhalt der Tags `<NotFound>`, `<NotAuthorized>` und `<Authorizing>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.

Wenn das Element `<NotAuthorized>` nicht angegeben ist, verwendet `AuthorizeRouteView` die folgende alternative Meldung:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Benachrichtigung über Änderungen des Authentifizierungsstatus

Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z. B. weil sich der Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein [benutzerdefinierter AuthenticationStateProvider-Dienst](#implement-a-custom-authenticationstateprovider) optional die Methode `NotifyAuthenticationStateChanged` in der Basisklasse `AuthenticationStateProvider` aufrufen. Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. `AuthorizeView`) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.

## <a name="procedural-logic"></a>Prozedurale Logik

Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<AuthenticationState>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen. `Task<AuthenticationState>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z.B. `IAuthorizationService`.

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Fügen Sie in einer Komponente einer `Microsoft.AspNetCore.Components.Authorization` WebAssembly-App die Namespaces Blazor und `Microsoft.AspNetCore.Authorization` hinzu:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Diese Namespaces können global bereitgestellt werden, indem Sie sie der Datei *_Imports.razor* der App hinzufügen.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorisierung in Blazor WebAssembly-Apps

In Blazor WebAssembly-Apps können Autorisierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann. Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.

**Führen Sie Autorisierungsprüfungen auf dem Server immer innerhalb aller API-Endpunkte durch, auf die Ihre clientseitige App zugreift.**

Weitere Informationen finden Sie in den Artikeln unter <xref:security/blazor/webassembly/index>.

## <a name="troubleshoot-errors"></a>Fehlerbehandlung

Häufige Fehler:

* **Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ „Task\<AuthenticationState >“. Verwenden Sie für die Bereitstellung „CascadingAuthenticationState“.**

* **Für `authenticationStateTask` wird ein `null`** -Wert empfangen.

Wahrscheinlich wurde das Projekt nicht mit einer Blazor Server-Vorlage mit aktivierter Authentifizierung erstellt. Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z. B. in der Komponente `App` (*App.razor*):

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` stellt den kaskadierenden Parameter `Task<AuthenticationState>` zur Verfügung, den er wiederum vom zugrunde liegenden DI-Dienst `AuthenticationStateProvider` empfängt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Nützliche Ressourcen für Blazor: Authentifizierung](https://github.com/AdrienTorris/awesome-blazor#authentication) – Beispiellinks für die Community
