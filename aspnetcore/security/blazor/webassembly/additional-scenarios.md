---
title: Weitere ASP.net Core Blazor Webassembly zusätzliche Sicherheitsszenarien
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: ccb512392341e3eea33f4ab45740b7900f7b63f9
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989469"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="10c7e-102">ASP.net Core blazor Webassembly zusätzliche Sicherheitsszenarien</span><span class="sxs-lookup"><span data-stu-id="10c7e-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="10c7e-103">Von [Javier calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="10c7e-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a><span data-ttu-id="10c7e-104">Behandeln von tokenanforderungs-Fehlern</span><span class="sxs-lookup"><span data-stu-id="10c7e-104">Handle token request errors</span></span>

<span data-ttu-id="10c7e-105">Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identitäts Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der die Daten.</span><span class="sxs-lookup"><span data-stu-id="10c7e-105">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="10c7e-106">Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="10c7e-106">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="10c7e-107">Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="10c7e-107">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="10c7e-108">In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="10c7e-108">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="10c7e-109">Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet.</span><span class="sxs-lookup"><span data-stu-id="10c7e-109">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="10c7e-110">Dieses Szenario tritt auf, wenn ein Benutzer `https://login.microsoftonline.com` besucht und sich abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="10c7e-110">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="10c7e-111">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="10c7e-111">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="10c7e-112">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="10c7e-112">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="10c7e-113">Sie sind Teil der Natur von Spas.</span><span class="sxs-lookup"><span data-stu-id="10c7e-113">They are part of the nature of SPAs.</span></span> <span data-ttu-id="10c7e-114">Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="10c7e-114">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="10c7e-115">Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="10c7e-115">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="10c7e-116">Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="10c7e-116">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="10c7e-117">Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="10c7e-117">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="10c7e-118">Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="10c7e-118">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="10c7e-119">Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="10c7e-119">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="10c7e-120">Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="10c7e-120">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="10c7e-121">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="10c7e-121">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="10c7e-122">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="10c7e-122">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="10c7e-123">Speichert den aktuellen Seiten Status im Sitzungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="10c7e-123">Store the current page state in session storage.</span></span> <span data-ttu-id="10c7e-124">Prüfen Sie während `OnInitializeAsync`, ob der Zustand wieder hergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="10c7e-124">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="10c7e-125">Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.</span><span class="sxs-lookup"><span data-stu-id="10c7e-125">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="10c7e-126">Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="10c7e-126">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="10c7e-127">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="10c7e-127">The following example shows how to:</span></span>

* <span data-ttu-id="10c7e-128">Status vor der Umleitung zur Anmeldeseite beibehalten.</span><span class="sxs-lookup"><span data-stu-id="10c7e-128">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="10c7e-129">Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.</span><span class="sxs-lookup"><span data-stu-id="10c7e-129">Recover the previous state afterward authentication using the query string parameter.</span></span>

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
            await httpClient.PostJsonAsync("Save", User);
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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="10c7e-130">Speichern des App-Zustands vor einem Authentifizierungs Vorgang</span><span class="sxs-lookup"><span data-stu-id="10c7e-130">Save app state before an authentication operation</span></span>

<span data-ttu-id="10c7e-131">Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="10c7e-131">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="10c7e-132">Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="10c7e-132">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="10c7e-133">Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="10c7e-133">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="10c7e-134">`Authentication` Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="10c7e-134">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="request-additional-access-tokens"></a><span data-ttu-id="10c7e-135">Anfordern zusätzlicher Zugriffs Token</span><span class="sxs-lookup"><span data-stu-id="10c7e-135">Request additional access tokens</span></span>

<span data-ttu-id="10c7e-136">Die meisten apps benötigen nur ein Zugriffs Token für die Interaktion mit den geschützten Ressourcen, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="10c7e-136">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="10c7e-137">In einigen Szenarien erfordert eine APP möglicherweise mehr als ein Token, um mit zwei oder mehr Ressourcen zu interagieren.</span><span class="sxs-lookup"><span data-stu-id="10c7e-137">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span> <span data-ttu-id="10c7e-138">Die `IAccessTokenProvider.RequestToken`-Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Token mit einem bestimmten Satz von Bereichen bereitzustellen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="10c7e-138">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a><span data-ttu-id="10c7e-139">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="10c7e-139">Customize app routes</span></span>

<span data-ttu-id="10c7e-140">Standardmäßig verwendet die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.</span><span class="sxs-lookup"><span data-stu-id="10c7e-140">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="10c7e-141">Route</span><span class="sxs-lookup"><span data-stu-id="10c7e-141">Route</span></span>                            | <span data-ttu-id="10c7e-142">Zweck</span><span class="sxs-lookup"><span data-stu-id="10c7e-142">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="10c7e-143">Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="10c7e-143">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="10c7e-144">Behandelt das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="10c7e-144">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="10c7e-145">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="10c7e-145">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="10c7e-146">Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="10c7e-146">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="10c7e-147">Behandelt das Ergebnis eines Abmelde Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="10c7e-147">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="10c7e-148">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="10c7e-148">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="10c7e-149">Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="10c7e-149">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="10c7e-150">Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="10c7e-150">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="10c7e-151">Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="10c7e-151">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="10c7e-152">Die in der vorangehenden Tabelle gezeigten Routen können über `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="10c7e-152">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="10c7e-153">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet</span><span class="sxs-lookup"><span data-stu-id="10c7e-153">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="10c7e-154">Im folgenden Beispiel werden allen Pfaden `/security`vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="10c7e-154">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="10c7e-155">`Authentication` Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="10c7e-155">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="10c7e-156">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="10c7e-156">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="10c7e-157">Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest, und Rendering Sie die `RemoteAuthenticatorView` mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="10c7e-157">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="10c7e-158">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="10c7e-158">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="10c7e-159">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="10c7e-159">Customize the authentication user interface</span></span>

<span data-ttu-id="10c7e-160">`RemoteAuthenticatorView` enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status.</span><span class="sxs-lookup"><span data-stu-id="10c7e-160">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="10c7e-161">Jeder Status kann angepasst werden, indem ein benutzerdefinierter `RenderFragment`übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="10c7e-161">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="10c7e-162">Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können Sie die `RemoteAuthenticatorView` wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="10c7e-162">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="10c7e-163">`Authentication` Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="10c7e-163">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="10c7e-164">Der `RemoteAuthenticatorView` verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="10c7e-164">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="10c7e-165">Route</span><span class="sxs-lookup"><span data-stu-id="10c7e-165">Route</span></span>                            | <span data-ttu-id="10c7e-166">Fragment</span><span class="sxs-lookup"><span data-stu-id="10c7e-166">Fragment</span></span>                |
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
