---
<span data-ttu-id="47c8e-101">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-103">'Blazor'</span></span>
- <span data-ttu-id="47c8e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-104">'Identity'</span></span>
- <span data-ttu-id="47c8e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-106">'Razor'</span></span>
- <span data-ttu-id="47c8e-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="47c8e-108">BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="47c8e-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="47c8e-109">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="47c8e-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="47c8e-110">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="47c8e-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="47c8e-111">Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> Dienst kann mit verwendet werden <xref:System.Net.Http.HttpClient> , um Zugriffs Token an ausgehende Anforderungen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="47c8e-112">Token werden mithilfe des vorhandenen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Dienstanbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="47c8e-113">Wenn ein Token nicht abgerufen werden kann, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> wird eine ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="47c8e-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="47c8e-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>verfügt über eine- <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> Methode, die verwendet werden kann, um den Benutzer zum Identitäts Anbieter zu navigieren, um ein neues Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="47c8e-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Kann mithilfe der-Methode mit den autorisierten URLs, Bereichen und Rückgabe-URL konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="47c8e-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="47c8e-116">Im folgenden Beispiel wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> ein <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="47c8e-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

<span data-ttu-id="47c8e-117">Zur einfacheren Verwendung <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ist ein enthalten, das mit der APP-Basisadresse als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="47c8e-118">Die Authentifizierungs fähigen Blazor webassemblyvorlagen verwenden nun <xref:System.Net.Http.IHttpClientFactory> im Server-API-Projekt, um eine <xref:System.Net.Http.HttpClient> mit dem einzurichten <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="47c8e-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="47c8e-119">Wenn der Client <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> im vorherigen Beispiel mit erstellt wird, werden die- <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="47c8e-120">Der konfigurierte <xref:System.Net.Http.HttpClient> wird dann verwendet, um autorisierte Anforderungen mithilfe eines einfachen [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Musters auszuführen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="47c8e-121">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="47c8e-122">Typisierte HttpClient-Instanz</span><span class="sxs-lookup"><span data-stu-id="47c8e-122">Typed HttpClient</span></span>

<span data-ttu-id="47c8e-123">Ein typisierter Client kann definiert werden, der alle Probleme mit der HTTP-und tokenübernahme innerhalb einer einzelnen Klasse behandelt.</span><span class="sxs-lookup"><span data-stu-id="47c8e-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="47c8e-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="47c8e-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="47c8e-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="47c8e-126">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="47c8e-127">Konfigurieren des httpclient-Handlers</span><span class="sxs-lookup"><span data-stu-id="47c8e-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="47c8e-128">Der Handler kann für ausgehende HTTP-Anforderungen weiter konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="47c8e-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="47c8e-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="47c8e-130">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="47c8e-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="47c8e-131">Wenn die Blazor Webassembly-App normalerweise einen sicheren Standardwert verwendet <xref:System.Net.Http.HttpClient> , kann die APP auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen erstellen, indem eine mit dem Namen konfiguriert wird <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="47c8e-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="47c8e-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="47c8e-133">Die vorherige Registrierung ist zusätzlich zur vorhandenen sicheren Standard <xref:System.Net.Http.HttpClient> Registrierung.</span><span class="sxs-lookup"><span data-stu-id="47c8e-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="47c8e-134">Eine-Komponente erstellt die <xref:System.Net.Http.HttpClient> aus dem <xref:System.Net.Http.IHttpClientFactory> , um nicht authentifizierte oder nicht autorisierte Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="47c8e-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="47c8e-135">Der Controller in der Server-API `WeatherForecastNoAuthenticationController` für das vorherige Beispiel ist nicht mit dem- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="47c8e-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="47c8e-136">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="47c8e-136">Request additional access tokens</span></span>

<span data-ttu-id="47c8e-137">Zugriffs Token können manuell abgerufen werden, indem aufgerufen wird `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="47c8e-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="47c8e-138">Im folgenden Beispiel werden zusätzliche Azure Active Directory-Microsoft Graph-API-Bereiche (AAD) für eine APP benötigt, um Benutzerdaten zu lesen und e-Mails zu senden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="47c8e-139">Nachdem Sie die Berechtigungen für die Microsoft Graph-API im Azure Aad-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="47c8e-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="47c8e-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="47c8e-141">Die- `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Zugriffs Token mit einem bestimmten Satz von Bereichen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="47c8e-142">In einer- Razor Komponente:</span><span class="sxs-lookup"><span data-stu-id="47c8e-142">In a Razor component:</span></span>

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

<span data-ttu-id="47c8e-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Renditen</span><span class="sxs-lookup"><span data-stu-id="47c8e-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="47c8e-144">`true`mit der `token` zu verwendenden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="47c8e-145">`false`, wenn das Token nicht abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="47c8e-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="47c8e-146">HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="47c8e-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="47c8e-147">Beim Ausführen von Blazor webassemblys in einer webassemblyapp können Sie mithilfe von [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> Anforderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="47c8e-148">Beispielsweise können Sie die HTTP-Methode und die Anforderungs Header angeben.</span><span class="sxs-lookup"><span data-stu-id="47c8e-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="47c8e-149">Die folgende Komponente sendet eine `POST` Anforderung an einen API-Endpunkt der Aufgabenliste auf dem Server und zeigt den Antworttext an:</span><span class="sxs-lookup"><span data-stu-id="47c8e-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="47c8e-150">Die .NET-WebAssembly-Implementierung von <xref:System.Net.Http.HttpClient> verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="47c8e-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="47c8e-151">FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="47c8e-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="47c8e-152">HTTP FETCH-Anforderungsoptionen können mit <xref:System.Net.Http.HttpRequestMessage>-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="47c8e-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="47c8e-153">Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="47c8e-153">Extension method</span></span> | <span data-ttu-id="47c8e-154">FETCH-Anforderungseigenschaft</span><span class="sxs-lookup"><span data-stu-id="47c8e-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="47c8e-155">credentials</span><span class="sxs-lookup"><span data-stu-id="47c8e-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="47c8e-156">cache</span><span class="sxs-lookup"><span data-stu-id="47c8e-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="47c8e-157">mode</span><span class="sxs-lookup"><span data-stu-id="47c8e-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="47c8e-158">integrity</span><span class="sxs-lookup"><span data-stu-id="47c8e-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="47c8e-159">Sie können weitere Optionen mithilfe der allgemeineren <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>-Erweiterungsmethode festlegen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="47c8e-160">Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="47c8e-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="47c8e-161">Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A>-Erweiterungsmethode für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="47c8e-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="47c8e-162">Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="47c8e-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="47c8e-163">Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="47c8e-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="47c8e-164">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="47c8e-165">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="47c8e-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="47c8e-166">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="47c8e-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="47c8e-167">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="47c8e-167">Any method (verb).</span></span>
* <span data-ttu-id="47c8e-168">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="47c8e-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="47c8e-169">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="47c8e-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="47c8e-170">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="47c8e-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="47c8e-171">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="47c8e-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="47c8e-172">Behandeln von tokenanforderungs-Fehlern</span><span class="sxs-lookup"><span data-stu-id="47c8e-172">Handle token request errors</span></span>

<span data-ttu-id="47c8e-173">Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identity Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der seine Anmelde Informationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="47c8e-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="47c8e-174">Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="47c8e-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="47c8e-175">Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="47c8e-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="47c8e-176">In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="47c8e-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="47c8e-177">Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet.</span><span class="sxs-lookup"><span data-stu-id="47c8e-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="47c8e-178">Dieses Szenario tritt auf, wenn ein Benutzer die Benutzer besucht `https://login.microsoftonline.com` und abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="47c8e-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="47c8e-179">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="47c8e-180">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="47c8e-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="47c8e-181">Sie sind Teil der Natur von Spas.</span><span class="sxs-lookup"><span data-stu-id="47c8e-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="47c8e-182">Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="47c8e-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="47c8e-183">Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="47c8e-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="47c8e-184">Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="47c8e-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="47c8e-185">Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="47c8e-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="47c8e-186">Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="47c8e-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="47c8e-187">Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="47c8e-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="47c8e-188">Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="47c8e-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="47c8e-189">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="47c8e-190">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="47c8e-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="47c8e-191">Speichert den aktuellen Seiten Status im Sitzungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="47c8e-191">Store the current page state in session storage.</span></span> <span data-ttu-id="47c8e-192">Überprüfen Sie während des [oninitializedasync Lifecycle-Ereignisses](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), ob der Zustand wieder hergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="47c8e-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="47c8e-193">Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.</span><span class="sxs-lookup"><span data-stu-id="47c8e-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="47c8e-194">Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="47c8e-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="47c8e-195">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="47c8e-195">The following example shows how to:</span></span>

* <span data-ttu-id="47c8e-196">Status vor der Umleitung zur Anmeldeseite beibehalten.</span><span class="sxs-lookup"><span data-stu-id="47c8e-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="47c8e-197">Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.</span><span class="sxs-lookup"><span data-stu-id="47c8e-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="47c8e-198">Speichern des App-Zustands vor einem Authentifizierungs Vorgang</span><span class="sxs-lookup"><span data-stu-id="47c8e-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="47c8e-199">Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="47c8e-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="47c8e-200">Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="47c8e-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="47c8e-201">Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="47c8e-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="47c8e-202">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="47c8e-203">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="47c8e-203">Customize app routes</span></span>

<span data-ttu-id="47c8e-204">Standardmäßig verwendet die [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.</span><span class="sxs-lookup"><span data-stu-id="47c8e-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="47c8e-205">Route</span><span class="sxs-lookup"><span data-stu-id="47c8e-205">Route</span></span>                            | <span data-ttu-id="47c8e-206">Zweck</span><span class="sxs-lookup"><span data-stu-id="47c8e-206">Purpose</span></span> |
| ---
<span data-ttu-id="47c8e-207">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-209">'Blazor'</span></span>
- <span data-ttu-id="47c8e-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-210">'Identity'</span></span>
- <span data-ttu-id="47c8e-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-212">'Razor'</span></span>
- <span data-ttu-id="47c8e-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-214">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-216">'Blazor'</span></span>
- <span data-ttu-id="47c8e-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-217">'Identity'</span></span>
- <span data-ttu-id="47c8e-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-219">'Razor'</span></span>
- <span data-ttu-id="47c8e-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-221">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-223">'Blazor'</span></span>
- <span data-ttu-id="47c8e-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-224">'Identity'</span></span>
- <span data-ttu-id="47c8e-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-226">'Razor'</span></span>
- <span data-ttu-id="47c8e-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-228">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-230">'Blazor'</span></span>
- <span data-ttu-id="47c8e-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-231">'Identity'</span></span>
- <span data-ttu-id="47c8e-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-233">'Razor'</span></span>
- <span data-ttu-id="47c8e-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-235">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-237">'Blazor'</span></span>
- <span data-ttu-id="47c8e-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-238">'Identity'</span></span>
- <span data-ttu-id="47c8e-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-240">'Razor'</span></span>
- <span data-ttu-id="47c8e-241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-242">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-244">'Blazor'</span></span>
- <span data-ttu-id="47c8e-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-245">'Identity'</span></span>
- <span data-ttu-id="47c8e-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-247">'Razor'</span></span>
- <span data-ttu-id="47c8e-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-249">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-251">'Blazor'</span></span>
- <span data-ttu-id="47c8e-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-252">'Identity'</span></span>
- <span data-ttu-id="47c8e-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-254">'Razor'</span></span>
- <span data-ttu-id="47c8e-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-256">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-258">'Blazor'</span></span>
- <span data-ttu-id="47c8e-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-259">'Identity'</span></span>
- <span data-ttu-id="47c8e-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-261">'Razor'</span></span>
- <span data-ttu-id="47c8e-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-263">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-265">'Blazor'</span></span>
- <span data-ttu-id="47c8e-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-266">'Identity'</span></span>
- <span data-ttu-id="47c8e-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-268">'Razor'</span></span>
- <span data-ttu-id="47c8e-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-270">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-272">'Blazor'</span></span>
- <span data-ttu-id="47c8e-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-273">'Identity'</span></span>
- <span data-ttu-id="47c8e-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-275">'Razor'</span></span>
- <span data-ttu-id="47c8e-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-277">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-279">'Blazor'</span></span>
- <span data-ttu-id="47c8e-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-280">'Identity'</span></span>
- <span data-ttu-id="47c8e-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-282">'Razor'</span></span>
- <span data-ttu-id="47c8e-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-284">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-286">'Blazor'</span></span>
- <span data-ttu-id="47c8e-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-287">'Identity'</span></span>
- <span data-ttu-id="47c8e-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-289">'Razor'</span></span>
- <span data-ttu-id="47c8e-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-291">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-293">'Blazor'</span></span>
- <span data-ttu-id="47c8e-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-294">'Identity'</span></span>
- <span data-ttu-id="47c8e-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-296">'Razor'</span></span>
- <span data-ttu-id="47c8e-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-298">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-300">'Blazor'</span></span>
- <span data-ttu-id="47c8e-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-301">'Identity'</span></span>
- <span data-ttu-id="47c8e-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-303">'Razor'</span></span>
- <span data-ttu-id="47c8e-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-304">'SignalR' uid:</span></span> 

<span data-ttu-id="47c8e-305">---------------- | ---Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-307">'Blazor'</span></span>
- <span data-ttu-id="47c8e-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-308">'Identity'</span></span>
- <span data-ttu-id="47c8e-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-310">'Razor'</span></span>
- <span data-ttu-id="47c8e-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-311">'SignalR' uid:</span></span> 

<span data-ttu-id="47c8e-312">---- | | `authentication/login`           | Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="47c8e-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="47c8e-313">| | `authentication/login-callback`  | Behandelt das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="47c8e-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="47c8e-314">| | `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="47c8e-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="47c8e-315">| | `authentication/logout`          | Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="47c8e-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="47c8e-316">| | `authentication/logout-callback` | Behandelt das Ergebnis eines Abmelde Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="47c8e-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="47c8e-317">| | `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="47c8e-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="47c8e-318">| | `authentication/logged-out`      | Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="47c8e-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="47c8e-319">| | `authentication/profile`         | Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="47c8e-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="47c8e-320">| | `authentication/register`        | Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="47c8e-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="47c8e-321">Die in der vorangehenden Tabelle gezeigten Routen können über konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="47c8e-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="47c8e-322">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet</span><span class="sxs-lookup"><span data-stu-id="47c8e-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="47c8e-323">Im folgenden Beispiel wird allen Pfaden ein Präfix vorangestellt `/security` .</span><span class="sxs-lookup"><span data-stu-id="47c8e-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="47c8e-324">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="47c8e-325">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="47c8e-326">Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest, und erstellen Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="47c8e-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="47c8e-327">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="47c8e-328">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="47c8e-328">Customize the authentication user interface</span></span>

<span data-ttu-id="47c8e-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status.</span><span class="sxs-lookup"><span data-stu-id="47c8e-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="47c8e-330">Jeder Zustand kann durch Übergeben eines benutzerdefinierten angepasst werden <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="47c8e-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="47c8e-331">Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können Sie den <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="47c8e-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="47c8e-332">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="47c8e-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="47c8e-333">Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="47c8e-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="47c8e-334">Route</span><span class="sxs-lookup"><span data-stu-id="47c8e-334">Route</span></span>                            | <span data-ttu-id="47c8e-335">Fragment</span><span class="sxs-lookup"><span data-stu-id="47c8e-335">Fragment</span></span>                |
| ---
<span data-ttu-id="47c8e-336">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-338">'Blazor'</span></span>
- <span data-ttu-id="47c8e-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-339">'Identity'</span></span>
- <span data-ttu-id="47c8e-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-341">'Razor'</span></span>
- <span data-ttu-id="47c8e-342">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-343">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-345">'Blazor'</span></span>
- <span data-ttu-id="47c8e-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-346">'Identity'</span></span>
- <span data-ttu-id="47c8e-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-348">'Razor'</span></span>
- <span data-ttu-id="47c8e-349">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-350">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-352">'Blazor'</span></span>
- <span data-ttu-id="47c8e-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-353">'Identity'</span></span>
- <span data-ttu-id="47c8e-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-355">'Razor'</span></span>
- <span data-ttu-id="47c8e-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-357">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-359">'Blazor'</span></span>
- <span data-ttu-id="47c8e-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-360">'Identity'</span></span>
- <span data-ttu-id="47c8e-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-362">'Razor'</span></span>
- <span data-ttu-id="47c8e-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-364">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-366">'Blazor'</span></span>
- <span data-ttu-id="47c8e-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-367">'Identity'</span></span>
- <span data-ttu-id="47c8e-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-369">'Razor'</span></span>
- <span data-ttu-id="47c8e-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-371">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-373">'Blazor'</span></span>
- <span data-ttu-id="47c8e-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-374">'Identity'</span></span>
- <span data-ttu-id="47c8e-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-376">'Razor'</span></span>
- <span data-ttu-id="47c8e-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-378">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-380">'Blazor'</span></span>
- <span data-ttu-id="47c8e-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-381">'Identity'</span></span>
- <span data-ttu-id="47c8e-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-383">'Razor'</span></span>
- <span data-ttu-id="47c8e-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-385">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-387">'Blazor'</span></span>
- <span data-ttu-id="47c8e-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-388">'Identity'</span></span>
- <span data-ttu-id="47c8e-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-390">'Razor'</span></span>
- <span data-ttu-id="47c8e-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-392">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-394">'Blazor'</span></span>
- <span data-ttu-id="47c8e-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-395">'Identity'</span></span>
- <span data-ttu-id="47c8e-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-397">'Razor'</span></span>
- <span data-ttu-id="47c8e-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-399">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-401">'Blazor'</span></span>
- <span data-ttu-id="47c8e-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-402">'Identity'</span></span>
- <span data-ttu-id="47c8e-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-404">'Razor'</span></span>
- <span data-ttu-id="47c8e-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-406">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-408">'Blazor'</span></span>
- <span data-ttu-id="47c8e-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-409">'Identity'</span></span>
- <span data-ttu-id="47c8e-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-411">'Razor'</span></span>
- <span data-ttu-id="47c8e-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-413">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-415">'Blazor'</span></span>
- <span data-ttu-id="47c8e-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-416">'Identity'</span></span>
- <span data-ttu-id="47c8e-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-418">'Razor'</span></span>
- <span data-ttu-id="47c8e-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-420">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-422">'Blazor'</span></span>
- <span data-ttu-id="47c8e-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-423">'Identity'</span></span>
- <span data-ttu-id="47c8e-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-425">'Razor'</span></span>
- <span data-ttu-id="47c8e-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-427">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-429">'Blazor'</span></span>
- <span data-ttu-id="47c8e-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-430">'Identity'</span></span>
- <span data-ttu-id="47c8e-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-432">'Razor'</span></span>
- <span data-ttu-id="47c8e-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-433">'SignalR' uid:</span></span> 

<span data-ttu-id="47c8e-434">---------------- | ---Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-436">'Blazor'</span></span>
- <span data-ttu-id="47c8e-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-437">'Identity'</span></span>
- <span data-ttu-id="47c8e-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-439">'Razor'</span></span>
- <span data-ttu-id="47c8e-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-441">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-443">'Blazor'</span></span>
- <span data-ttu-id="47c8e-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-444">'Identity'</span></span>
- <span data-ttu-id="47c8e-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-446">'Razor'</span></span>
- <span data-ttu-id="47c8e-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-448">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-450">'Blazor'</span></span>
- <span data-ttu-id="47c8e-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-451">'Identity'</span></span>
- <span data-ttu-id="47c8e-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-453">'Razor'</span></span>
- <span data-ttu-id="47c8e-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-455">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-457">'Blazor'</span></span>
- <span data-ttu-id="47c8e-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-458">'Identity'</span></span>
- <span data-ttu-id="47c8e-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-460">'Razor'</span></span>
- <span data-ttu-id="47c8e-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-462">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-464">'Blazor'</span></span>
- <span data-ttu-id="47c8e-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-465">'Identity'</span></span>
- <span data-ttu-id="47c8e-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-467">'Razor'</span></span>
- <span data-ttu-id="47c8e-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-469">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-471">'Blazor'</span></span>
- <span data-ttu-id="47c8e-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-472">'Identity'</span></span>
- <span data-ttu-id="47c8e-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-474">'Razor'</span></span>
- <span data-ttu-id="47c8e-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-476">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-478">'Blazor'</span></span>
- <span data-ttu-id="47c8e-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-479">'Identity'</span></span>
- <span data-ttu-id="47c8e-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-481">'Razor'</span></span>
- <span data-ttu-id="47c8e-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-483">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-485">'Blazor'</span></span>
- <span data-ttu-id="47c8e-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-486">'Identity'</span></span>
- <span data-ttu-id="47c8e-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-488">'Razor'</span></span>
- <span data-ttu-id="47c8e-489">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="47c8e-490">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="47c8e-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="47c8e-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="47c8e-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="47c8e-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-492">'Blazor'</span></span>
- <span data-ttu-id="47c8e-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="47c8e-493">'Identity'</span></span>
- <span data-ttu-id="47c8e-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="47c8e-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="47c8e-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="47c8e-495">'Razor'</span></span>
- <span data-ttu-id="47c8e-496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="47c8e-496">'SignalR' uid:</span></span> 

<span data-ttu-id="47c8e-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="47c8e-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="47c8e-498">Benutzer anpassen</span><span class="sxs-lookup"><span data-stu-id="47c8e-498">Customize the user</span></span>

<span data-ttu-id="47c8e-499">Benutzer, die an die APP gebunden sind, können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="47c8e-500">Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr` Anspruch für jede Authentifizierungsmethode des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="47c8e-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="47c8e-501">Erstellen Sie eine Klasse, die die- <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Klasse erweitert:</span><span class="sxs-lookup"><span data-stu-id="47c8e-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="47c8e-502">Erstellen Sie eine Factory, die Folgendes erweitert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="47c8e-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="47c8e-503">Registrieren Sie `CustomAccountFactory` für den verwendeten Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="47c8e-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="47c8e-504">Jede der folgenden Registrierungen ist gültig:</span><span class="sxs-lookup"><span data-stu-id="47c8e-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="47c8e-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="47c8e-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="47c8e-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="47c8e-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="47c8e-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="47c8e-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="47c8e-508">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="47c8e-508">Support prerendering with authentication</span></span>

<span data-ttu-id="47c8e-509">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="47c8e-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="47c8e-510">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="47c8e-511">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="47c8e-512">Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="47c8e-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="47c8e-513">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="47c8e-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="47c8e-514">Ersetzen Sie in der-Methode der Server-APP `Startup.Configure` [Endpunkte. Mapfallbackyfile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) mit [Endpunkten. Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="47c8e-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="47c8e-515">Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="47c8e-516">Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47c8e-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="47c8e-517">Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein.</span><span class="sxs-lookup"><span data-stu-id="47c8e-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="47c8e-518">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="47c8e-518">Update the file's contents:</span></span>

* <span data-ttu-id="47c8e-519">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="47c8e-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="47c8e-520">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="47c8e-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="47c8e-521">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="47c8e-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="47c8e-522">Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="47c8e-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="47c8e-523">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="47c8e-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="47c8e-524">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="47c8e-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="47c8e-525">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="47c8e-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="47c8e-526">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="47c8e-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="47c8e-527">Szenario:</span><span class="sxs-lookup"><span data-stu-id="47c8e-527">In this scenario:</span></span>

* <span data-ttu-id="47c8e-528">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="47c8e-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="47c8e-529">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="47c8e-530">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="47c8e-531">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="47c8e-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="47c8e-532">Konfigurieren Sie Identity mit einem Drittanbieter-Anmelde Anbieter.</span><span class="sxs-lookup"><span data-stu-id="47c8e-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="47c8e-533">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="47c8e-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="47c8e-534">Wenn sich ein Benutzer anmeldet, Identity sammelt Zugriffs-und Aktualisierungs Token im Rahmen des Authentifizierungs Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="47c8e-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="47c8e-535">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="47c8e-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="47c8e-536">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="47c8e-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="47c8e-537">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="47c8e-538">Verwenden Sie von dort aus das Drittanbieter-Zugriffs Token, um API-Ressourcen von Drittanbietern direkt von Identity auf dem Client aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="47c8e-539">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-539">We don't recommend this approach.</span></span> <span data-ttu-id="47c8e-540">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="47c8e-541">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="47c8e-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="47c8e-542">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="47c8e-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="47c8e-543">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="47c8e-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="47c8e-544">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="47c8e-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="47c8e-545">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="47c8e-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="47c8e-546">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="47c8e-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="47c8e-547">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="47c8e-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="47c8e-548">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="47c8e-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="47c8e-549">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="47c8e-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="47c8e-550">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="47c8e-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="47c8e-551">Verwenden von Open ID Connect (oidc) v 2.0-Endpunkten</span><span class="sxs-lookup"><span data-stu-id="47c8e-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="47c8e-552">In der Authentifizierungs Bibliothek und in den Vorlagen werden die Blazor Endpunkte von Open ID Connect (oidc) v 1.0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="47c8e-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="47c8e-553">Um einen v 2.0-Endpunkt zu verwenden, konfigurieren Sie die Option JWT-Träger <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="47c8e-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="47c8e-554">Im folgenden Beispiel wird Aad für v 2.0 konfiguriert, indem ein `v2.0` Segment an die-Eigenschaft angehängt wird <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="47c8e-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="47c8e-555">Alternativ kann die Einstellung in der APP-Einstellungsdatei (*appSettings. JSON*) vorgenommen werden:</span><span class="sxs-lookup"><span data-stu-id="47c8e-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="47c8e-556">Wenn das über ein Segment an die Autorität nicht für den oidc-Anbieter der APP geeignet ist, z. b. bei nicht-Aad-Anbietern, legen Sie die- <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Eigenschaft direkt fest.</span><span class="sxs-lookup"><span data-stu-id="47c8e-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="47c8e-557">Legen Sie die-Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der APP-Einstellungsdatei (*appSettings. JSON*) mit dem `Authority` Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="47c8e-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="47c8e-558">Die Liste der Ansprüche im ID-Token ändert sich für v 2.0-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="47c8e-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="47c8e-559">Weitere Informationen finden Sie unter [Gründe für die Aktualisierung auf Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="47c8e-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
