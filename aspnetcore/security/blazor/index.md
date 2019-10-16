---
title: Authentifizierung und Autorisierung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie die Szenarien für die Authentifizierung und Autorisierung in Blazor kennen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/blazor/index
ms.openlocfilehash: 85a6a32ea068e6cd00ebb71bdf7fe0bd06b77618
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391312"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>Authentifizierung und Autorisierung in ASP.NET Core Blazor

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.

Es gibt jeweils unterschiedliche Sicherheitsszenarien für Blazor Server- und Blazor WebAssembly-Apps. Da Blazor Server-Apps auf dem Server ausgeführt werden, kann durch Autorisierungsprüfungen Folgendes bestimmt werden:

* Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).
* Zugriffsregeln für Bereiche und Komponenten der App.

Blazor WebAssembly-Apps werden auf dem Client ausgeführt. Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden. Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine Blazor WebAssembly-App keine Autorisierungszugriffsregeln durchsetzen.

## <a name="authentication"></a>Authentifizierung

Blazor verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen. Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird – über Blazor Server oder Blazor WebAssembly.

### <a name="blazor-server-authentication"></a>Blazor Server-Authentifizierung

Blazor Server-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde. Die [Authentifizierung in SignalR-basierten Apps](xref:signalr/authn-and-authz) wird verarbeitet, wenn die Verbindung hergestellt wird. Die Authentifizierung kann auf einem Cookie oder einem anderen Bearertoken basieren.

Die Blazor Server-Projektvorlage kann die Authentifizierung für Sie einrichten, wenn das Projekt erstellt wird.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.

Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.

Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:

* **Keine Authentifizierung**
* **Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:
  * Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.
  * Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Geschäfts-, Schul- oder Unikonten**
* **Windows-Authentifizierung**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.

| Authentifizierungsmechanismus                                                                 | `{AUTHENTICATION}`-Wert |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Keine Authentifizierung                                                                        | `None`                   |
| Einzelkonto<br>In der App mit ASP.NET Core-Identität gespeicherte Benutzer                        | `Individual`             |
| Einzelkonto<br>In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer | `IndividualB2C`          |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für einzelne Mandanten            | `SingleOrg`              |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für mehrere Mandanten           | `MultiOrg`               |
| Windows-Authentifizierung                                                                   | `Windows`                |

Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App. Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly-Authentifizierung

In den Blazor WebAssembly-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann. Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.

Fügen Sie der Projektdatei der App einen Paketverweis auf [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) hinzu.

Die Implementierung eines benutzerdefinierten `AuthenticationStateProvider`-Diensts für Blazor WebAssembly-Apps wird in den folgenden Abschnitten behandelt.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider-Dienst

Die Blazor Server-Apps beinhalten einen integrierten `AuthenticationStateProvider`-Dienst, der die Authentifizierungsstatusdaten von `HttpContext.User` von ASP. NET Core abruft. Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende serverseitige Authentifizierungsmechanismen von ASP.NET Core integrieren.

`AuthenticationStateProvider` ist der zugrunde liegende Dienst, der von der `AuthorizeView`- und der `CascadingAuthenticationState`-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.

`AuthenticationStateProvider` wird in der Regel nicht direkt verwendet. Verwenden Sie die später in diesem Artikel beschriebenen Ansätze [AuthorizeView-Komponente](#authorizeview-component) oder [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter). Der Hauptnachteil bei der direkten Verwendung von `AuthenticationStateProvider` ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.

Der Dienst `AuthenticationStateProvider` kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:

```cshtml
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/dependency-injection> und <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementieren eines benutzerdefinierten AuthenticationStateProvider

Wenn Sie eine Blazor WebAssembly-App erstellen oder wenn die Spezifikation Ihrer App unbedingt einen benutzerdefinierten Anbieter erfordert, implementieren Sie einen Anbieter, und überschreiben Sie `GetAuthenticationStateAsync`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
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
}
```

Der `CustomAuthStateProvider`-Dienst ist in `Startup.ConfigureServices` registriert:

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bei der Verwendung von `CustomAuthStateProvider` werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter

Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<AuthenticationState>` definieren:

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> Fügen Sie in einer Komponente einer Blazor WebAssembly-App den Namespace `Microsoft.AspNetCore.Components.Authorization` hinzu (`@using Microsoft.AspNetCore.Components.Authorization`).

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Richten Sie den kaskadierenden Parameter `Task<AuthenticationState>` mit den Komponenten `AuthorizeRouteView` und `CascadingAuthenticationState` ein:

```cshtml
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

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht authentifiziert ist:

```cshtml
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

Der Inhalt der Tags `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.

Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.

Wenn keine Autorisierungsbedingungen angegeben sind, verwendet `AuthorizeView` eine Standardrichtlinie und behandelt:

* Authentifizierte (angemeldete) Benutzer als autorisiert.
* Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.

### <a name="role-based-and-policy-based-authorization"></a>Rollenbasierte und richtlinienbasierte Autorisierung

Die `AuthorizeView`-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.

Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Weitere Informationen finden Sie unter <xref:security/authorization/roles>.

Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:

```cshtml
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

```cshtml
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

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> Fügen Sie in einer Komponente einer Blazor WebAssembly-App den Namespace `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`) den Beispielen in diesem Abschnitt hinzu.

> [!IMPORTANT]
> Verwenden Sie nur `[Authorize]` auf `@page`-Komponenten, die über den Blazor-Router erreicht werden. Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden. Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen `AuthorizeView`.

Das `[Authorize]`-Attribut unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung. Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:

```cshtml
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
* Der Benutzer eine `[Authorize]`-Bedingung nicht erfüllt, die für die Komponente angewendet wird. Das `[Authorize]`-Attribut wird im Abschnitt [[Authorize]-Attribut](#authorize-attribute) behandelt.
* Die Asynchrone Authentifizierung ausgeführt wird.

In der standardmäßigen Blazor Server-Projektvorlage zeigt die Datei *App.razor*, wie benutzerdefinierte Inhalte eingestellt werden können:

```cshtml
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

Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z.B. weil sich der Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein benutzerdefinierter `AuthenticationStateProvider` optional die Methode `NotifyAuthenticationStateChanged` in der Basisklasse `AuthenticationStateProvider` aufrufen. Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. `AuthorizeView`) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.

## <a name="procedural-logic"></a>Prozedurale Logik

Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<AuthenticationState>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen. `Task<AuthenticationState>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z.B. `IAuthorizationService`.

```cshtml
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
> Fügen Sie in einer Komponente einer Blazor WebAssembly-App die Namespaces `Microsoft.AspNetCore.Authorization` und `Microsoft.AspNetCore.Components.Authorization` hinzu:
>
> ```cshtml
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-blazor-webassembly-apps"></a>Autorisierung in Blazor WebAssembly-Apps

In den Blazor WebAssembly-Apps können Autorisierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann. Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.

**Führen Sie Autorisierungsprüfungen auf dem Server immer innerhalb aller API-Endpunkte durch, auf die Ihre clientseitige App zugreift.**

## <a name="troubleshoot-errors"></a>Beheben von Fehlern

Häufige Fehler:

* **Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ „Task\<AuthenticationState >“. Verwenden Sie für die Bereitstellung „CascadingAuthenticationState“.**

* **Für `authenticationStateTask` wird ein `null`** -Wert empfangen.

Wahrscheinlich wurde das Projekt nicht mit einer Blazor Server-Vorlage mit aktivierter Authentifizierung erstellt. Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z.B. in *App.razor*:

```cshtml
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
