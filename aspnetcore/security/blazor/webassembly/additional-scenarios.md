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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="db8ea-102">ASP.NET Core Blazor WebAssembly zusätzliche Sicherheitsszenarien</span><span class="sxs-lookup"><span data-stu-id="db8ea-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="db8ea-103">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="db8ea-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="db8ea-104">Zusätzliche Zugriffstoken anfordern</span><span class="sxs-lookup"><span data-stu-id="db8ea-104">Request additional access tokens</span></span>

<span data-ttu-id="db8ea-105">Die meisten Apps benötigen nur ein Zugriffstoken, um mit den geschützten Ressourcen zu interagieren, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="db8ea-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="db8ea-106">In einigen Szenarien benötigt eine App möglicherweise mehr als ein Token, um mit zwei oder mehr Ressourcen interagieren zu können.</span><span class="sxs-lookup"><span data-stu-id="db8ea-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="db8ea-107">Im folgenden Beispiel sind zusätzliche Azure Active Directory (AAD) Microsoft Graph-API-Bereiche von einer App zum Lesen von Benutzerdaten und Senden von E-Mails erforderlich.</span><span class="sxs-lookup"><span data-stu-id="db8ea-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="db8ea-108">Nach dem Hinzufügen der Microsoft Graph-API-Berechtigungen im Azure AAD-Portal werden`Program.Main`die zusätzlichen Bereiche in der Client-App ( *, Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="db8ea-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="db8ea-109">Die `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Token mit einem bestimmten Satz von Bereichen zu bereitstellen, wie im folgenden Beispiel zu sehen:</span><span class="sxs-lookup"><span data-stu-id="db8ea-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="db8ea-110">`TryGetToken`Gibt:</span><span class="sxs-lookup"><span data-stu-id="db8ea-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="db8ea-111">`true`mit `token` der zur Verwendung.</span><span class="sxs-lookup"><span data-stu-id="db8ea-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="db8ea-112">`false`wenn das Token nicht abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="db8ea-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="db8ea-113">Behandeln von Tokenanforderungsfehlern</span><span class="sxs-lookup"><span data-stu-id="db8ea-113">Handle token request errors</span></span>

<span data-ttu-id="db8ea-114">Wenn eine Single Page Application (SPA) einen Benutzer mit Open ID Connect (OIDC) authentifiziert, wird der Authentifizierungsstatus lokal im SPA und im Identitätsanbieter (IDENTITY Provider, IP) in Form eines Sitzungscookies verwaltet, das aufgrund der Angabe der Anmeldeinformationen des Benutzers festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="db8ea-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="db8ea-115">Die Token, die die IP für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume gültig, normalerweise etwa eine Stunde, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="db8ea-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="db8ea-116">Andernfalls wird der Benutzer abgemeldet, nachdem die gewährten Token abgelaufen sind.</span><span class="sxs-lookup"><span data-stu-id="db8ea-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="db8ea-117">In den meisten Fällen können OIDC-Clients neue Token bereitstellen, ohne dass der Benutzer sich dank des Authentifizierungsstatus oder der "Sitzung", die innerhalb der IP-Adresse beibehalten wird, erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="db8ea-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="db8ea-118">Es gibt einige Fälle, in denen der Client kein Token ohne Benutzerinteraktion abrufen kann, z. B. wenn sich der Benutzer aus irgendeinem Grund explizit von der IP abmeldet.</span><span class="sxs-lookup"><span data-stu-id="db8ea-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="db8ea-119">Dieses Szenario tritt auf, wenn ein Benutzer besucht `https://login.microsoftonline.com` und sich abmeldet. In diesen Szenarien weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="db8ea-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="db8ea-120">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abläuft.</span><span class="sxs-lookup"><span data-stu-id="db8ea-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="db8ea-121">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="db8ea-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="db8ea-122">Sie sind Teil der Natur der BSP.</span><span class="sxs-lookup"><span data-stu-id="db8ea-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="db8ea-123">Ein SPA, das Cookies verwendet, kann auch keine Server-API aufrufen, wenn das Authentifizierungscookie entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="db8ea-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="db8ea-124">Wenn eine App API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="db8ea-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="db8ea-125">Um ein neues Zugriffstoken für den Aufruf der API bereitstellen zu können, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="db8ea-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="db8ea-126">Selbst wenn der Client über ein Token verfügt, das gültig zu sein scheint, schlägt der Aufruf des Servers möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="db8ea-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="db8ea-127">Wenn die App ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="db8ea-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="db8ea-128">Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="db8ea-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="db8ea-129">Die Anforderung schlägt fehl, und die App muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="db8ea-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="db8ea-130">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie einen aktuellen Status speichern möchten, bevor Sie eine Umleitung durchführen.</span><span class="sxs-lookup"><span data-stu-id="db8ea-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="db8ea-131">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="db8ea-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="db8ea-132">Speichern Sie den aktuellen Seitenstatus im Sitzungsspeicher.</span><span class="sxs-lookup"><span data-stu-id="db8ea-132">Store the current page state in session storage.</span></span> <span data-ttu-id="db8ea-133">Während `OnInitializeAsync`überprüfen Sie, ob der Status wiederhergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="db8ea-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="db8ea-134">Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand neu hydratisieren muss.</span><span class="sxs-lookup"><span data-stu-id="db8ea-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="db8ea-135">Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Kollisionen mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="db8ea-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="db8ea-136">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="db8ea-136">The following example shows how to:</span></span>

* <span data-ttu-id="db8ea-137">Behalten Sie den Status, bevor Sie zur Anmeldeseite umleiten.</span><span class="sxs-lookup"><span data-stu-id="db8ea-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="db8ea-138">Stellen Sie den vorherigen Status nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters wieder her.</span><span class="sxs-lookup"><span data-stu-id="db8ea-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="db8ea-139">App-Status vor einem Authentifizierungsvorgang speichern</span><span class="sxs-lookup"><span data-stu-id="db8ea-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="db8ea-140">Während eines Authentifizierungsvorgangs gibt es Fälle, in denen Sie den App-Status speichern möchten, bevor der Browser an die IP umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="db8ea-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="db8ea-141">Dies kann der Fall sein, wenn Sie so etwas wie einen Statuscontainer verwenden und den Status wiederherstellen möchten, nachdem die Authentifizierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="db8ea-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="db8ea-142">Sie können ein benutzerdefiniertes Authentifizierungsstatusobjekt verwenden, um den app-spezifischen Status oder einen Verweis darauf beizubehalten und diesen Status wiederherzustellen, sobald der Authentifizierungsvorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="db8ea-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="db8ea-143">`Authentication`Komponente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="db8ea-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="db8ea-144">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="db8ea-144">Customize app routes</span></span>

<span data-ttu-id="db8ea-145">Standardmäßig verwendet `Microsoft.AspNetCore.Components.WebAssembly.Authentication` die Bibliothek die in der folgenden Tabelle gezeigten Routen zur Darstellung verschiedener Authentifizierungszustände.</span><span class="sxs-lookup"><span data-stu-id="db8ea-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="db8ea-146">Route</span><span class="sxs-lookup"><span data-stu-id="db8ea-146">Route</span></span>                            | <span data-ttu-id="db8ea-147">Zweck</span><span class="sxs-lookup"><span data-stu-id="db8ea-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="db8ea-148">Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="db8ea-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="db8ea-149">Behandelt das Ergebnis eines Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="db8ea-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="db8ea-150">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="db8ea-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="db8ea-151">Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="db8ea-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="db8ea-152">Behandelt das Ergebnis eines Abmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="db8ea-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="db8ea-153">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="db8ea-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="db8ea-154">Gibt an, dass sich der Benutzer erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="db8ea-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="db8ea-155">Löst einen Vorgang zum Bearbeiten des Benutzerprofils aus.</span><span class="sxs-lookup"><span data-stu-id="db8ea-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="db8ea-156">Löst einen Vorgang zum Registrieren eines neuen Benutzers aus.</span><span class="sxs-lookup"><span data-stu-id="db8ea-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="db8ea-157">Die in der obigen Tabelle angezeigten Routen sind über `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfigurierbar.</span><span class="sxs-lookup"><span data-stu-id="db8ea-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="db8ea-158">Bestätigen Sie beim Festlegen von Optionen zum Bereitstellen benutzerdefinierter Routen, dass die App über eine Route verfügt, die jeden Pfad verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="db8ea-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="db8ea-159">Im folgenden Beispiel werden allen Pfaden `/security`mit vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="db8ea-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="db8ea-160">`Authentication`Komponente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="db8ea-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="db8ea-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="db8ea-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="db8ea-162">Wenn die Anforderung völlig andere Pfade erfordert, legen Sie `RemoteAuthenticatorView` die Routen wie zuvor beschrieben fest, und rendern Sie den mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="db8ea-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="db8ea-163">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten aufteilen.</span><span class="sxs-lookup"><span data-stu-id="db8ea-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="db8ea-164">Anpassen der Authentifizierungsbenutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="db8ea-164">Customize the authentication user interface</span></span>

<span data-ttu-id="db8ea-165">`RemoteAuthenticatorView`enthält einen Standardsatz von UI-Stücken für jeden Authentifizierungsstatus.</span><span class="sxs-lookup"><span data-stu-id="db8ea-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="db8ea-166">Jeder Zustand kann durch Übergeben `RenderFragment`einer benutzerdefinierten angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="db8ea-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="db8ea-167">Um den angezeigten Text während des ersten `RemoteAuthenticatorView` Anmeldevorgangs anzupassen, können Sie Folgendes ändern.</span><span class="sxs-lookup"><span data-stu-id="db8ea-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="db8ea-168">`Authentication`Komponente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="db8ea-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="db8ea-169">Der `RemoteAuthenticatorView` verfügt über ein Fragment, das pro Authentifizierungsroute verwendet werden kann, die in der folgenden Tabelle angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="db8ea-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="db8ea-170">Route</span><span class="sxs-lookup"><span data-stu-id="db8ea-170">Route</span></span>                            | <span data-ttu-id="db8ea-171">Fragment</span><span class="sxs-lookup"><span data-stu-id="db8ea-171">Fragment</span></span>                |
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
