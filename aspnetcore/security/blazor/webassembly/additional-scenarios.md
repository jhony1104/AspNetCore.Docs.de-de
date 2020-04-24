---
title: Zusätzliche Blazor Sicherheitsszenarien für ASP.net Core Webassembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111174"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="986b4-102">ASP.net Core blazor Webassembly zusätzliche Sicherheitsszenarien</span><span class="sxs-lookup"><span data-stu-id="986b4-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="986b4-103">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="986b4-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="986b4-104">Die Anleitung in diesem Artikel gilt für ASP.net Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="986b4-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="986b4-105">Dieses Thema wird aktualisiert, um Preview 5 am Freitag, dem 24. April, zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="986b4-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="986b4-106">Anfordern zusätzlicher Zugriffs Token</span><span class="sxs-lookup"><span data-stu-id="986b4-106">Request additional access tokens</span></span>

<span data-ttu-id="986b4-107">Die meisten apps benötigen nur ein Zugriffs Token für die Interaktion mit den geschützten Ressourcen, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="986b4-107">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="986b4-108">In einigen Szenarien erfordert eine APP möglicherweise mehr als ein Token, um mit zwei oder mehr Ressourcen zu interagieren.</span><span class="sxs-lookup"><span data-stu-id="986b4-108">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="986b4-109">Im folgenden Beispiel werden zusätzliche Azure Active Directory-Microsoft Graph-API-Bereiche (AAD) für eine APP benötigt, um Benutzerdaten zu lesen und e-Mails zu senden.</span><span class="sxs-lookup"><span data-stu-id="986b4-109">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="986b4-110">Nachdem Sie die Berechtigungen für die Microsoft Graph-API im Azure Aad-Portal hinzugefügt haben, werden die zusätzlichen Bereiche`Program.Main`in der Client-app (, *Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="986b4-110">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="986b4-111">Die `IAccessTokenProvider.RequestToken` -Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Token mit einem bestimmten Satz von Bereichen bereitzustellen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="986b4-111">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="986b4-112">`TryGetToken`Renditen</span><span class="sxs-lookup"><span data-stu-id="986b4-112">`TryGetToken` returns:</span></span>

* <span data-ttu-id="986b4-113">`true`mit der `token` zu verwendenden.</span><span class="sxs-lookup"><span data-stu-id="986b4-113">`true` with the `token` for use.</span></span>
* <span data-ttu-id="986b4-114">`false`, wenn das Token nicht abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="986b4-114">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="986b4-115">Token an ausgehende Anforderungen anfügen</span><span class="sxs-lookup"><span data-stu-id="986b4-115">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="986b4-116">Der `AuthorizationMessageHandler` Dienst kann mit `HttpClient` verwendet werden, um Zugriffs Token an ausgehende Anforderungen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="986b4-116">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="986b4-117">Token werden mithilfe des vorhandenen `IAccessTokenProvider` Dienstanbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="986b4-117">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="986b4-118">Wenn ein Token nicht abgerufen werden kann, `AccessTokenNotAvailableException` wird eine ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="986b4-118">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="986b4-119">`AccessTokenNotAvailableException`verfügt über `Redirect` eine-Methode, die verwendet werden kann, um den Benutzer zum Identitäts Anbieter zu navigieren, um ein neues Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="986b4-119">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="986b4-120">`AuthorizationMessageHandler` Kann mithilfe der `ConfigureHandler` -Methode mit den autorisierten URLs, Bereichen und Rückgabe-URL konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-120">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="986b4-121">Im folgenden Beispiel `AuthorizationMessageHandler` wird ein `HttpClient` in `Program.Main` (*Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="986b4-121">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="986b4-122">Zur einfacheren Verwendung ist `BaseAddressAuthorizationMessageHandler` ein enthalten, das mit der APP-Basisadresse als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-122">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="986b4-123">Die Authentifizierungs fähigen blazor-webassemblyvorlagen verwenden jetzt [ihttpclientfactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) , um `HttpClient` eine mit `BaseAddressAuthorizationMessageHandler`dem einzurichten:</span><span class="sxs-lookup"><span data-stu-id="986b4-123">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="986b4-124">Wenn der Client im vorherigen Beispiel `CreateClient` mit erstellt wird, werden die `HttpClient` -Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-124">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="986b4-125">Der konfigurierte `HttpClient` wird dann verwendet, um autorisierte Anforderungen mit einem `try-catch` einfachen Muster zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="986b4-125">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="986b4-126">Die folgende `FetchData` Komponente fordert Wettervorhersagedaten an:</span><span class="sxs-lookup"><span data-stu-id="986b4-126">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="986b4-127">Alternativ können Sie einen typisierten Client definieren, der alle Probleme mit der HTTP-und tokenübernahme innerhalb einer einzelnen Klasse behandelt:</span><span class="sxs-lookup"><span data-stu-id="986b4-127">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="986b4-128">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="986b4-128">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="986b4-129">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="986b4-129">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="986b4-130">*Fetchdata. Razor*:</span><span class="sxs-lookup"><span data-stu-id="986b4-130">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="986b4-131">Behandeln von tokenanforderungs-Fehlern</span><span class="sxs-lookup"><span data-stu-id="986b4-131">Handle token request errors</span></span>

<span data-ttu-id="986b4-132">Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identitäts Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der seine Anmelde Informationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="986b4-132">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="986b4-133">Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="986b4-133">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="986b4-134">Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="986b4-134">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="986b4-135">In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="986b4-135">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="986b4-136">Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet.</span><span class="sxs-lookup"><span data-stu-id="986b4-136">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="986b4-137">Dieses Szenario tritt auf, wenn ein `https://login.microsoftonline.com` Benutzer die Benutzer besucht und abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="986b4-137">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="986b4-138">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-138">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="986b4-139">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="986b4-139">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="986b4-140">Sie sind Teil der Natur von Spas.</span><span class="sxs-lookup"><span data-stu-id="986b4-140">They are part of the nature of SPAs.</span></span> <span data-ttu-id="986b4-141">Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="986b4-141">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="986b4-142">Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="986b4-142">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="986b4-143">Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="986b4-143">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="986b4-144">Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="986b4-144">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="986b4-145">Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="986b4-145">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="986b4-146">Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="986b4-146">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="986b4-147">Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="986b4-147">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="986b4-148">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="986b4-148">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="986b4-149">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="986b4-149">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="986b4-150">Speichert den aktuellen Seiten Status im Sitzungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="986b4-150">Store the current page state in session storage.</span></span> <span data-ttu-id="986b4-151">Über `OnInitializeAsync`prüfen Sie während des weiteren, ob der Zustand wieder hergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="986b4-151">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="986b4-152">Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.</span><span class="sxs-lookup"><span data-stu-id="986b4-152">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="986b4-153">Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="986b4-153">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="986b4-154">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="986b4-154">The following example shows how to:</span></span>

* <span data-ttu-id="986b4-155">Status vor der Umleitung zur Anmeldeseite beibehalten.</span><span class="sxs-lookup"><span data-stu-id="986b4-155">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="986b4-156">Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.</span><span class="sxs-lookup"><span data-stu-id="986b4-156">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="986b4-157">Speichern des App-Zustands vor einem Authentifizierungs Vorgang</span><span class="sxs-lookup"><span data-stu-id="986b4-157">Save app state before an authentication operation</span></span>

<span data-ttu-id="986b4-158">Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="986b4-158">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="986b4-159">Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="986b4-159">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="986b4-160">Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="986b4-160">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="986b4-161">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="986b4-161">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="986b4-162">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="986b4-162">Customize app routes</span></span>

<span data-ttu-id="986b4-163">Standardmäßig verwendet die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.</span><span class="sxs-lookup"><span data-stu-id="986b4-163">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="986b4-164">Route</span><span class="sxs-lookup"><span data-stu-id="986b4-164">Route</span></span>                            | <span data-ttu-id="986b4-165">Zweck</span><span class="sxs-lookup"><span data-stu-id="986b4-165">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="986b4-166">Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="986b4-166">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="986b4-167">Behandelt das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="986b4-167">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="986b4-168">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="986b4-168">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="986b4-169">Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="986b4-169">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="986b4-170">Behandelt das Ergebnis eines Abmelde Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="986b4-170">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="986b4-171">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="986b4-171">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="986b4-172">Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="986b4-172">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="986b4-173">Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="986b4-173">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="986b4-174">Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="986b4-174">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="986b4-175">Die in der vorangehenden Tabelle gezeigten Routen können über `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-175">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="986b4-176">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet</span><span class="sxs-lookup"><span data-stu-id="986b4-176">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="986b4-177">Im folgenden Beispiel wird allen Pfaden ein Präfix vorangestellt `/security`.</span><span class="sxs-lookup"><span data-stu-id="986b4-177">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="986b4-178">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="986b4-178">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="986b4-179">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="986b4-179">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="986b4-180">Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest `RemoteAuthenticatorView` , und erstellen Sie mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="986b4-180">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="986b4-181">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="986b4-181">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="986b4-182">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="986b4-182">Customize the authentication user interface</span></span>

<span data-ttu-id="986b4-183">`RemoteAuthenticatorView`enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status.</span><span class="sxs-lookup"><span data-stu-id="986b4-183">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="986b4-184">Jeder Zustand kann durch Übergeben eines benutzerdefinierten `RenderFragment`angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-184">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="986b4-185">Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können `RemoteAuthenticatorView` Sie den wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="986b4-185">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="986b4-186">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="986b4-186">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="986b4-187">Der `RemoteAuthenticatorView` verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="986b4-187">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="986b4-188">Route</span><span class="sxs-lookup"><span data-stu-id="986b4-188">Route</span></span>                            | <span data-ttu-id="986b4-189">Fragment</span><span class="sxs-lookup"><span data-stu-id="986b4-189">Fragment</span></span>                |
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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="986b4-190">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="986b4-190">Support prerendering with authentication</span></span>

<span data-ttu-id="986b4-191">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="986b4-191">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="986b4-192">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-192">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="986b4-193">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-193">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="986b4-194">Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="986b4-194">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="986b4-195">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="986b4-195">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="986b4-196">Ersetzen Sie in der Methode `Startup.Configure` der Server-App `endpoints.MapFallbackToFile("index.html")` durch `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="986b4-196">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="986b4-197">Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-197">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="986b4-198">Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="986b4-198">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="986b4-199">Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein.</span><span class="sxs-lookup"><span data-stu-id="986b4-199">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="986b4-200">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="986b4-200">Update the file's contents:</span></span>

* <span data-ttu-id="986b4-201">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="986b4-201">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="986b4-202">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="986b4-202">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="986b4-203">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="986b4-203">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="986b4-204">Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="986b4-204">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="986b4-205">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="986b4-205">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="986b4-206">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="986b4-206">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="986b4-207">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="986b4-207">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="986b4-208">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="986b4-208">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="986b4-209">Szenario:</span><span class="sxs-lookup"><span data-stu-id="986b4-209">In this scenario:</span></span>

* <span data-ttu-id="986b4-210">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="986b4-210">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="986b4-211">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-211">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="986b4-212">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="986b4-212">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="986b4-213">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="986b4-213">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="986b4-214">Konfigurieren Sie ASP.NET Core Identity mit einem dritten Anmeldeanbieter.</span><span class="sxs-lookup"><span data-stu-id="986b4-214">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="986b4-215">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="986b4-215">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="986b4-216">Wenn sich ein Benutzer anmeldet, erfasst ASP.NET Core Identity die Tokens für Zugriff und Aktualisierung als Teil des Authentifizierungsprozesses.</span><span class="sxs-lookup"><span data-stu-id="986b4-216">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="986b4-217">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="986b4-217">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="986b4-218">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="986b4-218">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="986b4-219">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="986b4-219">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="986b4-220">Verwenden Sie dann das Zugriffstoken des Drittanbieters, um API-Ressourcen von Drittanbietern direkt von ASP.NET Core Identity auf dem Client abzurufen.</span><span class="sxs-lookup"><span data-stu-id="986b4-220">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="986b4-221">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="986b4-221">We don't recommend this approach.</span></span> <span data-ttu-id="986b4-222">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="986b4-222">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="986b4-223">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="986b4-223">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="986b4-224">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="986b4-224">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="986b4-225">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="986b4-225">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="986b4-226">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="986b4-226">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="986b4-227">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="986b4-227">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="986b4-228">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="986b4-228">Make an API call from the client to the server API.</span></span> <span data-ttu-id="986b4-229">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="986b4-229">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="986b4-230">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="986b4-230">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="986b4-231">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="986b4-231">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="986b4-232">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="986b4-232">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
