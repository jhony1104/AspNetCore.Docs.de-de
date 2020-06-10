---
title: BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Server
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server für zusätzliche Sicherheitsszenarien konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: f172df7f2333fd79ba175ec8a3a6925ec07f1113
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851524"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="9ec0f-103">BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="9ec0f-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="9ec0f-104">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9ec0f-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="9ec0f-105">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="9ec0f-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="9ec0f-106">Token, die außerhalb der Razor Komponenten in einer Blazor Server-app verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="9ec0f-107">Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices` Beispiels, finden Sie unter [übergeben von Token an eine serverseitige Blazor Anwendung](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="9ec0f-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="9ec0f-108">Authentifizieren Blazor Sie die Server-APP wie bei einer regulären Razor Seite oder MVC-app.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="9ec0f-109">Stellen Sie die Token für das Authentifizierungs Cookie bereit, und speichern Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="9ec0f-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="9ec0f-111">Definieren Sie eine Klasse, die den anfänglichen App-Zustand mit den Zugriffs-und Aktualisierungs Token übergibt:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="9ec0f-112">Definieren eines **Bereichs** bezogenen tokenanbienerdiensts, der in der APP verwendet werden kann Blazor , um die Token von der [Abhängigkeitsinjektion (di)](xref:blazor/dependency-injection)aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="9ec0f-113">`Startup.ConfigureServices`Fügen Sie in Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="9ec0f-114">Erstellen Sie in der Datei " *_Host. cshtml* " und die Instanz von, `InitialApplicationState` und übergeben Sie Sie als Parameter an die APP:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="9ec0f-115">`App`Lösen Sie in der Komponente (*app. Razor*) den Dienst auf, und initialisieren Sie ihn mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="9ec0f-116">Fügen Sie im Dienst, der eine sichere API-Anforderung sendet, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="9ec0f-117">Verwenden von Open ID Connect (oidc) v 2.0-Endpunkten</span><span class="sxs-lookup"><span data-stu-id="9ec0f-117">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="9ec0f-118">In der Authentifizierungs Bibliothek und in den Vorlagen werden die Blazor Endpunkte von Open ID Connect (oidc) v 1.0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-118">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="9ec0f-119">Um einen v 2.0-Endpunkt zu verwenden, konfigurieren Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> Option in der <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="9ec0f-119">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="9ec0f-120">Alternativ kann die Einstellung in der APP-Einstellungsdatei (*appSettings. JSON*) vorgenommen werden:</span><span class="sxs-lookup"><span data-stu-id="9ec0f-120">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="9ec0f-121">Wenn das über ein Segment an die Autorität nicht für den oidc-Anbieter der APP geeignet ist, z. b. bei nicht-Aad-Anbietern, legen Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Eigenschaft direkt fest.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-121">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="9ec0f-122">Legen Sie die-Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> oder in der APP-Einstellungsdatei mit dem <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-122">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="9ec0f-123">Änderungen am Code</span><span class="sxs-lookup"><span data-stu-id="9ec0f-123">Code changes</span></span>

* <span data-ttu-id="9ec0f-124">Die Liste der Ansprüche im ID-Token ändert sich für v 2.0-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-124">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="9ec0f-125">Weitere Informationen finden Sie unter [Gründe für die Aktualisierung auf Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="9ec0f-125">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="9ec0f-126">in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-126">in the Azure documentation.</span></span>
* <span data-ttu-id="9ec0f-127">Da Ressourcen in Bereichs-URIs für v 2.0-Endpunkte angegeben werden, entfernen Sie die Einstellung der- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> Eigenschaft in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="9ec0f-127">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="9ec0f-128">Sie finden den APP-ID-URI, der in der APP-Registrierungs Beschreibung des oidc-Anbieters verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9ec0f-128">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
