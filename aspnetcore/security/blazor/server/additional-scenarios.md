---
<span data-ttu-id="1c71b-101">Title: "ASP.net Core Blazor Server zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Sie Blazor Server für zusätzliche Sicherheitsszenarien konfigurieren."</span><span class="sxs-lookup"><span data-stu-id="1c71b-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="1c71b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1c71b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c71b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c71b-103">'Blazor'</span></span>
- <span data-ttu-id="1c71b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c71b-104">'Identity'</span></span>
- <span data-ttu-id="1c71b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c71b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c71b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c71b-106">'Razor'</span></span>
- <span data-ttu-id="1c71b-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1c71b-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="1c71b-108">BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="1c71b-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="1c71b-109">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1c71b-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="1c71b-110">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="1c71b-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="1c71b-111">Token, die außerhalb der Razor Komponenten in einer Blazor Server-app verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1c71b-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="1c71b-112">Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices` Beispiels, finden Sie unter [übergeben von Token an eine serverseitige Blazor Anwendung](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="1c71b-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="1c71b-113">Authentifizieren Blazor Sie die Server-APP wie bei einer regulären Razor Seite oder MVC-app.</span><span class="sxs-lookup"><span data-stu-id="1c71b-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="1c71b-114">Stellen Sie die Token für das Authentifizierungs Cookie bereit, und speichern Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="1c71b-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="1c71b-115">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1c71b-115">For example:</span></span>

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

<span data-ttu-id="1c71b-116">Definieren Sie eine Klasse, die den anfänglichen App-Zustand mit den Zugriffs-und Aktualisierungs Token übergibt:</span><span class="sxs-lookup"><span data-stu-id="1c71b-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1c71b-117">Definieren eines **Bereichs** bezogenen tokenanbienerdiensts, der in der APP verwendet werden kann Blazor , um die Token von der [Abhängigkeitsinjektion (di)](xref:blazor/dependency-injection)aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="1c71b-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1c71b-118">`Startup.ConfigureServices`Fügen Sie in Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="1c71b-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="1c71b-119">Erstellen Sie in der Datei " *_Host. cshtml* " und die Instanz von, `InitialApplicationState` und übergeben Sie Sie als Parameter an die APP:</span><span class="sxs-lookup"><span data-stu-id="1c71b-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="1c71b-120">`App`Lösen Sie in der Komponente (*app. Razor*) den Dienst auf, und initialisieren Sie ihn mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="1c71b-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="1c71b-121">Fügen Sie im Dienst, der eine sichere API-Anforderung sendet, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="1c71b-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="1c71b-122">Verwenden von Open ID Connect (oidc) v 2.0-Endpunkten</span><span class="sxs-lookup"><span data-stu-id="1c71b-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="1c71b-123">In der Authentifizierungs Bibliothek und in den Vorlagen werden die Blazor Endpunkte von Open ID Connect (oidc) v 1.0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="1c71b-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="1c71b-124">Um einen v 2.0-Endpunkt zu verwenden, konfigurieren Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> Option in der <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1c71b-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="1c71b-125">Alternativ kann die Einstellung in der APP-Einstellungsdatei (*appSettings. JSON*) vorgenommen werden:</span><span class="sxs-lookup"><span data-stu-id="1c71b-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="1c71b-126">Wenn das über ein Segment an die Autorität nicht für den oidc-Anbieter der APP geeignet ist, z. b. bei nicht-Aad-Anbietern, legen Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Eigenschaft direkt fest.</span><span class="sxs-lookup"><span data-stu-id="1c71b-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="1c71b-127">Legen Sie die-Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> oder in der APP-Einstellungsdatei mit dem <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="1c71b-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="1c71b-128">Änderungen am Code</span><span class="sxs-lookup"><span data-stu-id="1c71b-128">Code changes</span></span>

* <span data-ttu-id="1c71b-129">Die Liste der Ansprüche im ID-Token ändert sich für v 2.0-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="1c71b-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="1c71b-130">Weitere Informationen finden Sie unter [Gründe für die Aktualisierung auf Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="1c71b-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="1c71b-131">in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="1c71b-131">in the Azure documentation.</span></span>
* <span data-ttu-id="1c71b-132">Da Ressourcen in Bereichs-URIs für v 2.0-Endpunkte angegeben werden, entfernen Sie die Einstellung der- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> Eigenschaft in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1c71b-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

<span data-ttu-id="1c71b-133">Sie finden den APP-ID-URI, der in der APP-Registrierungs Beschreibung des oidc-Anbieters verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1c71b-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
