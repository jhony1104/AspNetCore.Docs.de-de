---
<span data-ttu-id="1466a-101">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: guardrex Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="1466a-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: guardrex description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="1466a-102">monikerrange: ' >= aspnetcore-3,1 ' ms. Author: Riande ms. Custom: MVC ms. Date: 06/01/2020 NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="1466a-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/01/2020 no-loc:</span></span>
- <span data-ttu-id="1466a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1466a-103">'Blazor'</span></span>
- <span data-ttu-id="1466a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1466a-104">'Identity'</span></span>
- <span data-ttu-id="1466a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1466a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1466a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1466a-106">'Razor'</span></span>
- <span data-ttu-id="1466a-107">' SignalR ' UID: Security/blazor/Webassembly/additional-Szenarios</span><span class="sxs-lookup"><span data-stu-id="1466a-107">'SignalR' uid: security/blazor/webassembly/additional-scenarios</span></span>

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="1466a-108">BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="1466a-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="1466a-109">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1466a-109">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="1466a-110">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="1466a-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="1466a-111">Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> Dienst kann mit verwendet werden <xref:System.Net.Http.HttpClient> , um Zugriffs Token an ausgehende Anforderungen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="1466a-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="1466a-112">Token werden mithilfe des vorhandenen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Dienstanbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1466a-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="1466a-113">Wenn ein Token nicht abgerufen werden kann, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> wird eine ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1466a-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="1466a-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>verfügt über eine- <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> Methode, die verwendet werden kann, um den Benutzer zum Identitäts Anbieter zu navigieren, um ein neues Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1466a-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="1466a-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Kann mithilfe der-Methode mit den autorisierten URLs, Bereichen und Rückgabe-URL konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="1466a-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="1466a-116">Verwenden Sie einen der folgenden Ansätze, um einen Nachrichten Handler für ausgehende Anforderungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1466a-116">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="1466a-117">[Custom authorizationmessagehandler-Klasse](#custom-authorizationmessagehandler-class) (*empfohlen*)</span><span class="sxs-lookup"><span data-stu-id="1466a-117">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="1466a-118">Konfigurieren von authorizationmessagehandler</span><span class="sxs-lookup"><span data-stu-id="1466a-118">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="1466a-119">Custom authorizationmessagehandler-Klasse</span><span class="sxs-lookup"><span data-stu-id="1466a-119">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="1466a-120">Im folgenden Beispiel erweitert eine benutzerdefinierte-Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , die zum Konfigurieren eines verwendet werden kann <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="1466a-120">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="1466a-121">In `Program.Main` (*Program.cs*) wird eine <xref:System.Net.Http.HttpClient> mit dem benutzerdefinierten Autorisierungs Nachrichten Handler konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="1466a-121">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="1466a-122">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um mithilfe des [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Musters autorisierte Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1466a-122">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="1466a-123">Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Package) erstellt wird, werden die- <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an die Server-API gestellt werden:</span><span class="sxs-lookup"><span data-stu-id="1466a-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="1466a-124">Konfigurieren von authorizationmessagehandler</span><span class="sxs-lookup"><span data-stu-id="1466a-124">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="1466a-125">Im folgenden Beispiel wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> ein <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="1466a-125">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="1466a-126">Zur einfacheren Verwendung <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ist ein enthalten, das mit der APP-Basisadresse als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-126">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="1466a-127">Die Authentifizierungs fähigen Blazor webassemblyvorlagen verwenden jetzt <xref:System.Net.Http.IHttpClientFactory> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Package) im Server-API-Projekt, um eine <xref:System.Net.Http.HttpClient> mit dem einzurichten <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="1466a-127">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="1466a-128">Wenn der Client <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> im vorherigen Beispiel mit erstellt wird, werden die- <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1466a-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="1466a-129">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Musters zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="1466a-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="1466a-130">Typisierte HttpClient-Instanz</span><span class="sxs-lookup"><span data-stu-id="1466a-130">Typed HttpClient</span></span>

<span data-ttu-id="1466a-131">Ein typisierter Client kann definiert werden, der alle Probleme mit der HTTP-und tokenübernahme innerhalb einer einzelnen Klasse behandelt.</span><span class="sxs-lookup"><span data-stu-id="1466a-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="1466a-132">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="1466a-132">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="1466a-133">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1466a-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="1466a-134">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="1466a-134">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="1466a-135">Konfigurieren des httpclient-Handlers</span><span class="sxs-lookup"><span data-stu-id="1466a-135">Configure the HttpClient handler</span></span>

<span data-ttu-id="1466a-136">Der Handler kann für ausgehende HTTP-Anforderungen weiter konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="1466a-136">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="1466a-137">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1466a-137">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="1466a-138">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="1466a-138">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="1466a-139">Wenn die Blazor Webassembly-App normalerweise einen sicheren Standardwert verwendet <xref:System.Net.Http.HttpClient> , kann die APP auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen erstellen, indem eine mit dem Namen konfiguriert wird <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="1466a-139">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="1466a-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1466a-140">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="1466a-141">Die vorherige Registrierung ist zusätzlich zur vorhandenen sicheren Standard <xref:System.Net.Http.HttpClient> Registrierung.</span><span class="sxs-lookup"><span data-stu-id="1466a-141">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="1466a-142">Eine-Komponente erstellt die <xref:System.Net.Http.HttpClient> aus dem <xref:System.Net.Http.IHttpClientFactory> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) -Paket), um nicht authentifizierte oder nicht autorisierte Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="1466a-142">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="1466a-143">Der Controller in der Server-API `WeatherForecastNoAuthenticationController` für das vorherige Beispiel ist nicht mit dem- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="1466a-143">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="1466a-144">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="1466a-144">Request additional access tokens</span></span>

<span data-ttu-id="1466a-145">Zugriffs Token können manuell abgerufen werden, indem aufgerufen wird `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="1466a-145">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="1466a-146">Im folgenden Beispiel werden zusätzliche Azure Active Directory-Microsoft Graph-API-Bereiche (AAD) für eine APP benötigt, um Benutzerdaten zu lesen und e-Mails zu senden.</span><span class="sxs-lookup"><span data-stu-id="1466a-146">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="1466a-147">Nachdem Sie die Berechtigungen für die Microsoft Graph-API im Azure Aad-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="1466a-147">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="1466a-148">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1466a-148">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="1466a-149">Die- `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Zugriffs Token mit einem bestimmten Satz von Bereichen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="1466a-149">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="1466a-150">In einer- Razor Komponente:</span><span class="sxs-lookup"><span data-stu-id="1466a-150">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

<span data-ttu-id="1466a-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Renditen</span><span class="sxs-lookup"><span data-stu-id="1466a-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="1466a-152">`true`mit der `token` zu verwendenden.</span><span class="sxs-lookup"><span data-stu-id="1466a-152">`true` with the `token` for use.</span></span>
* <span data-ttu-id="1466a-153">`false`, wenn das Token nicht abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1466a-153">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="1466a-154">HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="1466a-154">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="1466a-155">Beim Ausführen von Blazor webassemblys in einer webassemblyapp können Sie mithilfe von [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> Anforderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="1466a-155">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="1466a-156">Beispielsweise können Sie die HTTP-Methode und die Anforderungs Header angeben.</span><span class="sxs-lookup"><span data-stu-id="1466a-156">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="1466a-157">Die folgende Komponente sendet eine `POST` Anforderung an einen API-Endpunkt der Aufgabenliste auf dem Server und zeigt den Antworttext an:</span><span class="sxs-lookup"><span data-stu-id="1466a-157">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="1466a-158">Die .NET-WebAssembly-Implementierung von <xref:System.Net.Http.HttpClient> verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="1466a-158">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="1466a-159">FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="1466a-159">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="1466a-160">HTTP FETCH-Anforderungsoptionen können mit <xref:System.Net.Http.HttpRequestMessage>-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="1466a-160">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="1466a-161">Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="1466a-161">Extension method</span></span> | <span data-ttu-id="1466a-162">FETCH-Anforderungseigenschaft</span><span class="sxs-lookup"><span data-stu-id="1466a-162">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="1466a-163">credentials</span><span class="sxs-lookup"><span data-stu-id="1466a-163">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="1466a-164">cache</span><span class="sxs-lookup"><span data-stu-id="1466a-164">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="1466a-165">mode</span><span class="sxs-lookup"><span data-stu-id="1466a-165">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="1466a-166">integrity</span><span class="sxs-lookup"><span data-stu-id="1466a-166">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="1466a-167">Sie können weitere Optionen mithilfe der allgemeineren <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>-Erweiterungsmethode festlegen.</span><span class="sxs-lookup"><span data-stu-id="1466a-167">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="1466a-168">Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="1466a-168">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="1466a-169">Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A>-Erweiterungsmethode für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1466a-169">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="1466a-170">Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="1466a-170">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="1466a-171">Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="1466a-171">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="1466a-172">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="1466a-172">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="1466a-173">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="1466a-173">The following policy includes configuration for:</span></span>

* <span data-ttu-id="1466a-174">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="1466a-174">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="1466a-175">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="1466a-175">Any method (verb).</span></span>
* <span data-ttu-id="1466a-176">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="1466a-176">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="1466a-177">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="1466a-177">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="1466a-178">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="1466a-178">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="1466a-179">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="1466a-179">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="1466a-180">Behandeln von tokenanforderungs-Fehlern</span><span class="sxs-lookup"><span data-stu-id="1466a-180">Handle token request errors</span></span>

<span data-ttu-id="1466a-181">Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identity Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der seine Anmelde Informationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="1466a-181">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="1466a-182">Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="1466a-182">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="1466a-183">Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="1466a-183">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="1466a-184">In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="1466a-184">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="1466a-185">Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet.</span><span class="sxs-lookup"><span data-stu-id="1466a-185">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="1466a-186">Dieses Szenario tritt auf, wenn ein Benutzer die Benutzer besucht `https://login.microsoftonline.com` und abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="1466a-186">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="1466a-187">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-187">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="1466a-188">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="1466a-188">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="1466a-189">Sie sind Teil der Natur von Spas.</span><span class="sxs-lookup"><span data-stu-id="1466a-189">They are part of the nature of SPAs.</span></span> <span data-ttu-id="1466a-190">Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="1466a-190">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="1466a-191">Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="1466a-191">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="1466a-192">Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="1466a-192">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="1466a-193">Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="1466a-193">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="1466a-194">Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="1466a-194">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="1466a-195">Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="1466a-195">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="1466a-196">Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="1466a-196">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="1466a-197">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="1466a-197">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="1466a-198">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="1466a-198">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="1466a-199">Speichert den aktuellen Seiten Status im Sitzungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="1466a-199">Store the current page state in session storage.</span></span> <span data-ttu-id="1466a-200">Überprüfen Sie während des [oninitializedasync Lifecycle-Ereignisses](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), ob der Zustand wieder hergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="1466a-200">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="1466a-201">Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.</span><span class="sxs-lookup"><span data-stu-id="1466a-201">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="1466a-202">Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="1466a-202">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="1466a-203">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="1466a-203">The following example shows how to:</span></span>

* <span data-ttu-id="1466a-204">Status vor der Umleitung zur Anmeldeseite beibehalten.</span><span class="sxs-lookup"><span data-stu-id="1466a-204">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="1466a-205">Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.</span><span class="sxs-lookup"><span data-stu-id="1466a-205">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="1466a-206">Speichern des App-Zustands vor einem Authentifizierungs Vorgang</span><span class="sxs-lookup"><span data-stu-id="1466a-206">Save app state before an authentication operation</span></span>

<span data-ttu-id="1466a-207">Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="1466a-207">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="1466a-208">Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="1466a-208">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="1466a-209">Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="1466a-209">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="1466a-210">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1466a-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="1466a-211">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="1466a-211">Customize app routes</span></span>

<span data-ttu-id="1466a-212">Standardmäßig verwendet die [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.</span><span class="sxs-lookup"><span data-stu-id="1466a-212">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="1466a-213">Route</span><span class="sxs-lookup"><span data-stu-id="1466a-213">Route</span></span>                            | <span data-ttu-id="1466a-214">Zweck</span><span class="sxs-lookup"><span data-stu-id="1466a-214">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="1466a-215">Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="1466a-215">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="1466a-216">Behandelt das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="1466a-216">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="1466a-217">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="1466a-217">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="1466a-218">Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="1466a-218">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="1466a-219">Behandelt das Ergebnis eines Abmelde Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="1466a-219">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="1466a-220">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="1466a-220">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="1466a-221">Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="1466a-221">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="1466a-222">Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="1466a-222">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="1466a-223">Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="1466a-223">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="1466a-224">Die in der vorangehenden Tabelle gezeigten Routen können über konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1466a-224">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1466a-225">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet</span><span class="sxs-lookup"><span data-stu-id="1466a-225">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="1466a-226">Im folgenden Beispiel wird allen Pfaden ein Präfix vorangestellt `/security` .</span><span class="sxs-lookup"><span data-stu-id="1466a-226">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="1466a-227">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1466a-227">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="1466a-228">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1466a-228">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="1466a-229">Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest, und erstellen Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="1466a-229">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="1466a-230">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="1466a-230">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="1466a-231">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1466a-231">Customize the authentication user interface</span></span>

<span data-ttu-id="1466a-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status.</span><span class="sxs-lookup"><span data-stu-id="1466a-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="1466a-233">Jeder Zustand kann durch Übergeben eines benutzerdefinierten angepasst werden <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="1466a-233">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="1466a-234">Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können Sie den <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="1466a-234">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="1466a-235">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1466a-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="1466a-236">Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1466a-236">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="1466a-237">Route</span><span class="sxs-lookup"><span data-stu-id="1466a-237">Route</span></span>                            | <span data-ttu-id="1466a-238">Fragment</span><span class="sxs-lookup"><span data-stu-id="1466a-238">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="1466a-239">Benutzer anpassen</span><span class="sxs-lookup"><span data-stu-id="1466a-239">Customize the user</span></span>

<span data-ttu-id="1466a-240">Benutzer, die an die APP gebunden sind, können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="1466a-240">Users bound to the app can be customized.</span></span> <span data-ttu-id="1466a-241">Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr` Anspruch für jede Authentifizierungsmethode des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="1466a-241">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="1466a-242">Erstellen Sie eine Klasse, die die- <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Klasse erweitert:</span><span class="sxs-lookup"><span data-stu-id="1466a-242">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="1466a-243">Erstellen Sie eine Factory, die Folgendes erweitert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="1466a-243">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="1466a-244">Registrieren Sie `CustomAccountFactory` für den verwendeten Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="1466a-244">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="1466a-245">Jede der folgenden Registrierungen ist gültig:</span><span class="sxs-lookup"><span data-stu-id="1466a-245">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="1466a-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="1466a-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="1466a-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="1466a-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="1466a-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="1466a-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="1466a-249">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="1466a-249">Support prerendering with authentication</span></span>

<span data-ttu-id="1466a-250">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="1466a-250">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="1466a-251">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-251">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="1466a-252">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-252">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="1466a-253">Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="1466a-253">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

<span data-ttu-id="1466a-254">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="1466a-254">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="1466a-255">Ersetzen Sie in der-Methode der Server-APP `Startup.Configure` [Endpunkte. Mapfallbackyfile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) mit [Endpunkten. Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="1466a-255">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="1466a-256">Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-256">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="1466a-257">Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1466a-257">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="1466a-258">Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein.</span><span class="sxs-lookup"><span data-stu-id="1466a-258">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="1466a-259">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="1466a-259">Update the file's contents:</span></span>

* <span data-ttu-id="1466a-260">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="1466a-260">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="1466a-261">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1466a-261">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="1466a-262">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="1466a-262">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="1466a-263">Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="1466a-263">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="1466a-264">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="1466a-264">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="1466a-265">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="1466a-265">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="1466a-266">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="1466a-266">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="1466a-267">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="1466a-267">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="1466a-268">Szenario:</span><span class="sxs-lookup"><span data-stu-id="1466a-268">In this scenario:</span></span>

* <span data-ttu-id="1466a-269">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="1466a-269">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="1466a-270">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="1466a-270">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="1466a-271">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1466a-271">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="1466a-272">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="1466a-272">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="1466a-273">Konfigurieren Sie Identity mit einem Drittanbieter-Anmelde Anbieter.</span><span class="sxs-lookup"><span data-stu-id="1466a-273">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="1466a-274">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="1466a-274">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="1466a-275">Wenn sich ein Benutzer anmeldet, Identity sammelt Zugriffs-und Aktualisierungs Token im Rahmen des Authentifizierungs Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="1466a-275">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="1466a-276">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="1466a-276">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="1466a-277">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="1466a-277">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="1466a-278">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1466a-278">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="1466a-279">Verwenden Sie von dort aus das Drittanbieter-Zugriffs Token, um API-Ressourcen von Drittanbietern direkt von Identity auf dem Client aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1466a-279">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="1466a-280">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="1466a-280">We don't recommend this approach.</span></span> <span data-ttu-id="1466a-281">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="1466a-281">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="1466a-282">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="1466a-282">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="1466a-283">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="1466a-283">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="1466a-284">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="1466a-284">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="1466a-285">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="1466a-285">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="1466a-286">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="1466a-286">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="1466a-287">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="1466a-287">Make an API call from the client to the server API.</span></span> <span data-ttu-id="1466a-288">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1466a-288">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="1466a-289">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="1466a-289">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="1466a-290">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="1466a-290">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="1466a-291">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="1466a-291">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="1466a-292">Verwenden von Open ID Connect (oidc) v 2.0-Endpunkten</span><span class="sxs-lookup"><span data-stu-id="1466a-292">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="1466a-293">In der Authentifizierungs Bibliothek und in den Vorlagen werden die Blazor Endpunkte von Open ID Connect (oidc) v 1.0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="1466a-293">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="1466a-294">Um einen v 2.0-Endpunkt zu verwenden, konfigurieren Sie die Option JWT-Träger <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1466a-294">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="1466a-295">Im folgenden Beispiel wird Aad für v 2.0 konfiguriert, indem ein `v2.0` Segment an die-Eigenschaft angehängt wird <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="1466a-295">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="1466a-296">Alternativ kann die Einstellung in der APP-Einstellungsdatei (*appSettings. JSON*) vorgenommen werden:</span><span class="sxs-lookup"><span data-stu-id="1466a-296">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="1466a-297">Wenn das über ein Segment an die Autorität nicht für den oidc-Anbieter der APP geeignet ist, z. b. bei nicht-Aad-Anbietern, legen Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Eigenschaft direkt fest.</span><span class="sxs-lookup"><span data-stu-id="1466a-297">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="1466a-298">Legen Sie die-Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der APP-Einstellungsdatei (*appSettings. JSON*) mit dem `Authority` Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="1466a-298">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="1466a-299">Die Liste der Ansprüche im ID-Token ändert sich für v 2.0-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="1466a-299">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="1466a-300">Weitere Informationen finden Sie unter [Gründe für die Aktualisierung auf Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="1466a-300">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
