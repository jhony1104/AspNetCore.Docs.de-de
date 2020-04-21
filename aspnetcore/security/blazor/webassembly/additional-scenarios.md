---
title: ASP.NET Blazor Core WebAssembly zusätzliche Sicherheitsszenarien
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661595"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly zusätzliche Sicherheitsszenarien

Von [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Zusätzliche Zugriffstoken anfordern

Die meisten Apps benötigen nur ein Zugriffstoken, um mit den geschützten Ressourcen zu interagieren, die sie verwenden. In einigen Szenarien benötigt eine App möglicherweise mehr als ein Token, um mit zwei oder mehr Ressourcen interagieren zu können.

Im folgenden Beispiel sind zusätzliche Azure Active Directory (AAD) Microsoft Graph-API-Bereiche von einer App zum Lesen von Benutzerdaten und Senden von E-Mails erforderlich. Nach dem Hinzufügen der Microsoft Graph-API-Berechtigungen im Azure AAD-Portal werden`Program.Main`die zusätzlichen Bereiche in der Client-App ( *, Program.cs*) konfiguriert:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

Die `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Token mit einem bestimmten Satz von Bereichen zu bereitstellen, wie im folgenden Beispiel zu sehen:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`Gibt:

* `true`mit `token` der zur Verwendung.
* `false`wenn das Token nicht abgerufen wird.

## <a name="handle-token-request-errors"></a>Behandeln von Tokenanforderungsfehlern

Wenn eine Single Page Application (SPA) einen Benutzer mit Open ID Connect (OIDC) authentifiziert, wird der Authentifizierungsstatus lokal im SPA und im Identitätsanbieter (IDENTITY Provider, IP) in Form eines Sitzungscookies verwaltet, das aufgrund der Angabe der Anmeldeinformationen des Benutzers festgelegt wird.

Die Token, die die IP für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume gültig, normalerweise etwa eine Stunde, sodass die Client-App regelmäßig neue Token abrufen muss. Andernfalls wird der Benutzer abgemeldet, nachdem die gewährten Token abgelaufen sind. In den meisten Fällen können OIDC-Clients neue Token bereitstellen, ohne dass der Benutzer sich dank des Authentifizierungsstatus oder der "Sitzung", die innerhalb der IP-Adresse beibehalten wird, erneut authentifizieren muss.

Es gibt einige Fälle, in denen der Client kein Token ohne Benutzerinteraktion abrufen kann, z. B. wenn sich der Benutzer aus irgendeinem Grund explizit von der IP abmeldet. Dieses Szenario tritt auf, wenn ein Benutzer besucht `https://login.microsoftonline.com` und sich abmeldet. In diesen Szenarien weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig. Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abläuft.

Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung. Sie sind Teil der Natur der BSP. Ein SPA, das Cookies verwendet, kann auch keine Server-API aufrufen, wenn das Authentifizierungscookie entfernt wird.

Wenn eine App API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:

* Um ein neues Zugriffstoken für den Aufruf der API bereitstellen zu können, muss sich der Benutzer möglicherweise erneut authentifizieren.
* Selbst wenn der Client über ein Token verfügt, das gültig zu sein scheint, schlägt der Aufruf des Servers möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.

Wenn die App ein Token anfordert, gibt es zwei mögliche Ergebnisse:

* Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.
* Die Anforderung schlägt fehl, und die App muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.

Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie einen aktuellen Status speichern möchten, bevor Sie eine Umleitung durchführen. Es gibt mehrere Ansätze mit zunehmender Komplexität:

* Speichern Sie den aktuellen Seitenstatus im Sitzungsspeicher. Während `OnInitializeAsync`überprüfen Sie, ob der Status wiederhergestellt werden kann, bevor Sie fortfahren.
* Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand neu hydratisieren muss.
* Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Kollisionen mit anderen Elementen zu riskieren.

Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Behalten Sie den Status, bevor Sie zur Anmeldeseite umleiten.
* Stellen Sie den vorherigen Status nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters wieder her.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>App-Status vor einem Authentifizierungsvorgang speichern

Während eines Authentifizierungsvorgangs gibt es Fälle, in denen Sie den App-Status speichern möchten, bevor der Browser an die IP umgeleitet wird. Dies kann der Fall sein, wenn Sie so etwas wie einen Statuscontainer verwenden und den Status wiederherstellen möchten, nachdem die Authentifizierung erfolgreich war. Sie können ein benutzerdefiniertes Authentifizierungsstatusobjekt verwenden, um den app-spezifischen Status oder einen Verweis darauf beizubehalten und diesen Status wiederherzustellen, sobald der Authentifizierungsvorgang erfolgreich abgeschlossen wurde.

`Authentication`Komponente (*Pages/Authentication.razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a>Anpassen von App-Routen

Standardmäßig verwendet `Microsoft.AspNetCore.Components.WebAssembly.Authentication` die Bibliothek die in der folgenden Tabelle gezeigten Routen zur Darstellung verschiedener Authentifizierungszustände.

| Route                            | Zweck |
| -------------------------------- | ------- |
| `authentication/login`           | Löst einen Anmeldevorgang aus. |
| `authentication/login-callback`  | Behandelt das Ergebnis eines Anmeldevorgangs. |
| `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt. |
| `authentication/logout`          | Löst einen Abmeldevorgang aus. |
| `authentication/logout-callback` | Behandelt das Ergebnis eines Abmeldevorgangs. |
| `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt. |
| `authentication/logged-out`      | Gibt an, dass sich der Benutzer erfolgreich abgemeldet hat. |
| `authentication/profile`         | Löst einen Vorgang zum Bearbeiten des Benutzerprofils aus. |
| `authentication/register`        | Löst einen Vorgang zum Registrieren eines neuen Benutzers aus. |

Die in der obigen Tabelle angezeigten Routen sind über `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfigurierbar. Bestätigen Sie beim Festlegen von Optionen zum Bereitstellen benutzerdefinierter Routen, dass die App über eine Route verfügt, die jeden Pfad verarbeitet.

Im folgenden Beispiel werden allen Pfaden `/security`mit vorangestellt.

`Authentication`Komponente (*Pages/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Wenn die Anforderung völlig andere Pfade erfordert, legen Sie `RemoteAuthenticatorView` die Routen wie zuvor beschrieben fest, und rendern Sie den mit einem expliziten Aktionsparameter:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten aufteilen.

## <a name="customize-the-authentication-user-interface"></a>Anpassen der Authentifizierungsbenutzeroberfläche

`RemoteAuthenticatorView`enthält einen Standardsatz von UI-Stücken für jeden Authentifizierungsstatus. Jeder Zustand kann durch Übergeben `RenderFragment`einer benutzerdefinierten angepasst werden. Um den angezeigten Text während des ersten `RemoteAuthenticatorView` Anmeldevorgangs anzupassen, können Sie Folgendes ändern.

`Authentication`Komponente (*Pages/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

Der `RemoteAuthenticatorView` verfügt über ein Fragment, das pro Authentifizierungsroute verwendet werden kann, die in der folgenden Tabelle angezeigt wird.

| Route                            | Fragment                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
