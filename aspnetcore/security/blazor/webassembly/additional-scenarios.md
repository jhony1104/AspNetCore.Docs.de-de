---
<span data-ttu-id="0bb42-101">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-102">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-103">'Blazor'</span></span>
- <span data-ttu-id="0bb42-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-104">'Identity'</span></span>
- <span data-ttu-id="0bb42-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-106">'Razor'</span></span>
- <span data-ttu-id="0bb42-107">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="0bb42-108">BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="0bb42-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="0bb42-109">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0bb42-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="0bb42-110">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="0bb42-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="0bb42-111">Der `AuthorizationMessageHandler` Dienst kann mit verwendet werden `HttpClient` , um Zugriffs Token an ausgehende Anforderungen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="0bb42-112">Token werden mithilfe des vorhandenen `IAccessTokenProvider` Dienstanbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="0bb42-113">Wenn ein Token nicht abgerufen werden kann, `AccessTokenNotAvailableException` wird eine ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0bb42-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="0bb42-114">`AccessTokenNotAvailableException`verfügt über eine- `Redirect` Methode, die verwendet werden kann, um den Benutzer zum Identitäts Anbieter zu navigieren, um ein neues Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="0bb42-115">`AuthorizationMessageHandler`Kann mithilfe der-Methode mit den autorisierten URLs, Bereichen und Rückgabe-URL konfiguriert werden `ConfigureHandler` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="0bb42-116">Im folgenden Beispiel wird `AuthorizationMessageHandler` ein `HttpClient` in `Program.Main` (*Program.cs*) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="0bb42-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0bb42-117">Zur einfacheren Verwendung `BaseAddressAuthorizationMessageHandler` ist ein enthalten, das mit der APP-Basisadresse als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="0bb42-118">Die Authentifizierungs fähigen Blazor webassemblyvorlagen verwenden nun <xref:System.Net.Http.IHttpClientFactory> im Server-API-Projekt, um eine <xref:System.Net.Http.HttpClient> mit dem einzurichten `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="0bb42-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="0bb42-119">Wenn der Client `CreateClient` im vorherigen Beispiel mit erstellt wird, werden die- <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="0bb42-120">Der konfigurierte <xref:System.Net.Http.HttpClient> wird dann verwendet, um autorisierte Anforderungen mit einem einfachen Muster zu erstellen `try-catch` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="0bb42-121">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="0bb42-122">Typisierte HttpClient-Instanz</span><span class="sxs-lookup"><span data-stu-id="0bb42-122">Typed HttpClient</span></span>

<span data-ttu-id="0bb42-123">Ein typisierter Client kann definiert werden, der alle Probleme mit der HTTP-und tokenübernahme innerhalb einer einzelnen Klasse behandelt.</span><span class="sxs-lookup"><span data-stu-id="0bb42-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="0bb42-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bb42-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="0bb42-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="0bb42-126">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="0bb42-127">Konfigurieren des httpclient-Handlers</span><span class="sxs-lookup"><span data-stu-id="0bb42-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="0bb42-128">Der Handler kann für ausgehende HTTP-Anforderungen weiter konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="0bb42-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="0bb42-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="0bb42-130">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="0bb42-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="0bb42-131">Wenn die Blazor Webassembly-App normalerweise einen sicheren Standardwert verwendet <xref:System.Net.Http.HttpClient> , kann die APP auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen erstellen, indem eine mit dem Namen konfiguriert wird <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="0bb42-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="0bb42-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="0bb42-133">Die vorherige Registrierung ist zusätzlich zur vorhandenen sicheren Standard <xref:System.Net.Http.HttpClient> Registrierung.</span><span class="sxs-lookup"><span data-stu-id="0bb42-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="0bb42-134">Eine-Komponente erstellt die <xref:System.Net.Http.HttpClient> aus dem <xref:System.Net.Http.IHttpClientFactory> , um nicht authentifizierte oder nicht autorisierte Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0bb42-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="0bb42-135">Der Controller in der Server-API `WeatherForecastNoAuthenticationController` für das vorherige Beispiel ist nicht mit dem- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="0bb42-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="0bb42-136">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="0bb42-136">Request additional access tokens</span></span>

<span data-ttu-id="0bb42-137">Zugriffs Token können manuell abgerufen werden, indem aufgerufen wird `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="0bb42-138">Im folgenden Beispiel werden zusätzliche Azure Active Directory-Microsoft Graph-API-Bereiche (AAD) für eine APP benötigt, um Benutzerdaten zu lesen und e-Mails zu senden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="0bb42-139">Nachdem Sie die Berechtigungen für die Microsoft Graph-API im Azure Aad-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="0bb42-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="0bb42-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0bb42-141">Die- `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Zugriffs Token mit einem bestimmten Satz von Bereichen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="0bb42-142">In einer- Razor Komponente:</span><span class="sxs-lookup"><span data-stu-id="0bb42-142">In a Razor component:</span></span>

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

<span data-ttu-id="0bb42-143">`TryGetToken`Renditen</span><span class="sxs-lookup"><span data-stu-id="0bb42-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="0bb42-144">`true`mit der `token` zu verwendenden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="0bb42-145">`false`, wenn das Token nicht abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0bb42-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="0bb42-146">HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="0bb42-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="0bb42-147">Beim Ausführen von Blazor webassemblys in einer webassemblyapp können Sie mithilfe von [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> Anforderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="0bb42-148">Beispielsweise können Sie die HTTP-Methode und die Anforderungs Header angeben.</span><span class="sxs-lookup"><span data-stu-id="0bb42-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="0bb42-149">Die folgende Komponente sendet eine `POST` Anforderung an einen API-Endpunkt der Aufgabenliste auf dem Server und zeigt den Antworttext an:</span><span class="sxs-lookup"><span data-stu-id="0bb42-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="0bb42-150">Die .NET-WebAssembly-Implementierung von `HttpClient` verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="0bb42-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="0bb42-151">FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="0bb42-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="0bb42-152">HTTP FETCH-Anforderungsoptionen können mit `HttpRequestMessage`-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="0bb42-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="0bb42-153">`HttpRequestMessage`-Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="0bb42-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="0bb42-154">FETCH-Anforderungseigenschaft</span><span class="sxs-lookup"><span data-stu-id="0bb42-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="0bb42-155">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-156">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-157">'Blazor'</span></span>
- <span data-ttu-id="0bb42-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-158">'Identity'</span></span>
- <span data-ttu-id="0bb42-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-160">'Razor'</span></span>
- <span data-ttu-id="0bb42-161">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-162">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-163">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-164">'Blazor'</span></span>
- <span data-ttu-id="0bb42-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-165">'Identity'</span></span>
- <span data-ttu-id="0bb42-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-167">'Razor'</span></span>
- <span data-ttu-id="0bb42-168">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-169">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-170">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-171">'Blazor'</span></span>
- <span data-ttu-id="0bb42-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-172">'Identity'</span></span>
- <span data-ttu-id="0bb42-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-174">'Razor'</span></span>
- <span data-ttu-id="0bb42-175">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-176">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-177">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-178">'Blazor'</span></span>
- <span data-ttu-id="0bb42-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-179">'Identity'</span></span>
- <span data-ttu-id="0bb42-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-181">'Razor'</span></span>
- <span data-ttu-id="0bb42-182">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-183">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-184">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-185">'Blazor'</span></span>
- <span data-ttu-id="0bb42-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-186">'Identity'</span></span>
- <span data-ttu-id="0bb42-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-188">'Razor'</span></span>
- <span data-ttu-id="0bb42-189">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-190">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-191">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-192">'Blazor'</span></span>
- <span data-ttu-id="0bb42-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-193">'Identity'</span></span>
- <span data-ttu-id="0bb42-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-195">'Razor'</span></span>
- <span data-ttu-id="0bb42-196">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-197">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-198">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-199">'Blazor'</span></span>
- <span data-ttu-id="0bb42-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-200">'Identity'</span></span>
- <span data-ttu-id="0bb42-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-202">'Razor'</span></span>
- <span data-ttu-id="0bb42-203">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-204">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-205">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-206">'Blazor'</span></span>
- <span data-ttu-id="0bb42-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-207">'Identity'</span></span>
- <span data-ttu-id="0bb42-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-209">'Razor'</span></span>
- <span data-ttu-id="0bb42-210">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-211">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-212">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-213">'Blazor'</span></span>
- <span data-ttu-id="0bb42-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-214">'Identity'</span></span>
- <span data-ttu-id="0bb42-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-216">'Razor'</span></span>
- <span data-ttu-id="0bb42-217">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-218">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-219">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-220">'Blazor'</span></span>
- <span data-ttu-id="0bb42-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-221">'Identity'</span></span>
- <span data-ttu-id="0bb42-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-223">'Razor'</span></span>
- <span data-ttu-id="0bb42-224">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-225">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-226">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-227">'Blazor'</span></span>
- <span data-ttu-id="0bb42-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-228">'Identity'</span></span>
- <span data-ttu-id="0bb42-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-230">'Razor'</span></span>
- <span data-ttu-id="0bb42-231">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-232">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-233">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-234">'Blazor'</span></span>
- <span data-ttu-id="0bb42-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-235">'Identity'</span></span>
- <span data-ttu-id="0bb42-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-237">'Razor'</span></span>
- <span data-ttu-id="0bb42-238">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-239">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-240">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-241">'Blazor'</span></span>
- <span data-ttu-id="0bb42-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-242">'Identity'</span></span>
- <span data-ttu-id="0bb42-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-244">'Razor'</span></span>
- <span data-ttu-id="0bb42-245">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-246">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-247">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-248">'Blazor'</span></span>
- <span data-ttu-id="0bb42-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-249">'Identity'</span></span>
- <span data-ttu-id="0bb42-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-251">'Razor'</span></span>
- <span data-ttu-id="0bb42-252">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-253">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-254">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-255">'Blazor'</span></span>
- <span data-ttu-id="0bb42-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-256">'Identity'</span></span>
- <span data-ttu-id="0bb42-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-258">'Razor'</span></span>
- <span data-ttu-id="0bb42-259">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-260">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-261">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-262">'Blazor'</span></span>
- <span data-ttu-id="0bb42-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-263">'Identity'</span></span>
- <span data-ttu-id="0bb42-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-265">'Razor'</span></span>
- <span data-ttu-id="0bb42-266">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-266">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-267">------------------- | ---Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-268">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-269">'Blazor'</span></span>
- <span data-ttu-id="0bb42-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-270">'Identity'</span></span>
- <span data-ttu-id="0bb42-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-272">'Razor'</span></span>
- <span data-ttu-id="0bb42-273">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-274">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-275">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-276">'Blazor'</span></span>
- <span data-ttu-id="0bb42-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-277">'Identity'</span></span>
- <span data-ttu-id="0bb42-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-279">'Razor'</span></span>
- <span data-ttu-id="0bb42-280">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-281">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-282">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-283">'Blazor'</span></span>
- <span data-ttu-id="0bb42-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-284">'Identity'</span></span>
- <span data-ttu-id="0bb42-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-286">'Razor'</span></span>
- <span data-ttu-id="0bb42-287">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-288">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-289">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-290">'Blazor'</span></span>
- <span data-ttu-id="0bb42-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-291">'Identity'</span></span>
- <span data-ttu-id="0bb42-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-293">'Razor'</span></span>
- <span data-ttu-id="0bb42-294">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-295">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-296">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-297">'Blazor'</span></span>
- <span data-ttu-id="0bb42-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-298">'Identity'</span></span>
- <span data-ttu-id="0bb42-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-300">'Razor'</span></span>
- <span data-ttu-id="0bb42-301">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-302">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-303">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-304">'Blazor'</span></span>
- <span data-ttu-id="0bb42-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-305">'Identity'</span></span>
- <span data-ttu-id="0bb42-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-307">'Razor'</span></span>
- <span data-ttu-id="0bb42-308">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-309">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-310">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-311">'Blazor'</span></span>
- <span data-ttu-id="0bb42-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-312">'Identity'</span></span>
- <span data-ttu-id="0bb42-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-314">'Razor'</span></span>
- <span data-ttu-id="0bb42-315">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-316">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-317">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-318">'Blazor'</span></span>
- <span data-ttu-id="0bb42-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-319">'Identity'</span></span>
- <span data-ttu-id="0bb42-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-321">'Razor'</span></span>
- <span data-ttu-id="0bb42-322">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-323">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-324">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-325">'Blazor'</span></span>
- <span data-ttu-id="0bb42-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-326">'Identity'</span></span>
- <span data-ttu-id="0bb42-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-328">'Razor'</span></span>
- <span data-ttu-id="0bb42-329">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-329">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-330">----------- | | `SetBrowserRequestCredentials`         |  [Anmelde](https://developer.mozilla.org/docs/Web/API/Request/credentials) Informationen | | `SetBrowserRequestCache`               |  [Cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [Modus](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [Integrität](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="0bb42-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="0bb42-331">Sie können weitere Optionen mithilfe der allgemeineren `SetBrowserRequestOption`-Erweiterungsmethode festlegen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="0bb42-332">Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="0bb42-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="0bb42-333">Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die `SetBrowserResponseStreamingEnabled`-Erweiterungsmethode für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0bb42-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="0bb42-334">Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die `SetBrowserRequestCredentials`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="0bb42-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="0bb42-335">Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="0bb42-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="0bb42-336">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="0bb42-337">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="0bb42-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="0bb42-338">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="0bb42-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="0bb42-339">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="0bb42-339">Any method (verb).</span></span>
* <span data-ttu-id="0bb42-340">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="0bb42-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="0bb42-341">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="0bb42-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="0bb42-342">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="0bb42-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="0bb42-343">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="0bb42-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="0bb42-344">Behandeln von tokenanforderungs-Fehlern</span><span class="sxs-lookup"><span data-stu-id="0bb42-344">Handle token request errors</span></span>

<span data-ttu-id="0bb42-345">Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identity Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der seine Anmelde Informationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="0bb42-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="0bb42-346">Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="0bb42-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="0bb42-347">Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="0bb42-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="0bb42-348">In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.</span><span class="sxs-lookup"><span data-stu-id="0bb42-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="0bb42-349">Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet.</span><span class="sxs-lookup"><span data-stu-id="0bb42-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="0bb42-350">Dieses Szenario tritt auf, wenn ein Benutzer die Benutzer besucht `https://login.microsoftonline.com` und abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="0bb42-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="0bb42-351">Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="0bb42-352">Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="0bb42-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="0bb42-353">Sie sind Teil der Natur von Spas.</span><span class="sxs-lookup"><span data-stu-id="0bb42-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="0bb42-354">Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="0bb42-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="0bb42-355">Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="0bb42-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="0bb42-356">Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="0bb42-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="0bb42-357">Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="0bb42-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="0bb42-358">Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:</span><span class="sxs-lookup"><span data-stu-id="0bb42-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="0bb42-359">Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="0bb42-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="0bb42-360">Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0bb42-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="0bb42-361">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="0bb42-362">Es gibt mehrere Ansätze mit zunehmender Komplexität:</span><span class="sxs-lookup"><span data-stu-id="0bb42-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="0bb42-363">Speichert den aktuellen Seiten Status im Sitzungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="0bb42-363">Store the current page state in session storage.</span></span> <span data-ttu-id="0bb42-364">`OnInitializeAsync`Überprüfen Sie während des weiteren, ob der Zustand wieder hergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0bb42-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="0bb42-365">Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.</span><span class="sxs-lookup"><span data-stu-id="0bb42-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="0bb42-366">Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="0bb42-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="0bb42-367">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="0bb42-367">The following example shows how to:</span></span>

* <span data-ttu-id="0bb42-368">Status vor der Umleitung zur Anmeldeseite beibehalten.</span><span class="sxs-lookup"><span data-stu-id="0bb42-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="0bb42-369">Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.</span><span class="sxs-lookup"><span data-stu-id="0bb42-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="0bb42-370">Speichern des App-Zustands vor einem Authentifizierungs Vorgang</span><span class="sxs-lookup"><span data-stu-id="0bb42-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="0bb42-371">Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="0bb42-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="0bb42-372">Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="0bb42-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="0bb42-373">Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="0bb42-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="0bb42-374">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="0bb42-375">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="0bb42-375">Customize app routes</span></span>

<span data-ttu-id="0bb42-376">Standardmäßig verwendet die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.</span><span class="sxs-lookup"><span data-stu-id="0bb42-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="0bb42-377">Route</span><span class="sxs-lookup"><span data-stu-id="0bb42-377">Route</span></span>                            | <span data-ttu-id="0bb42-378">Zweck</span><span class="sxs-lookup"><span data-stu-id="0bb42-378">Purpose</span></span> |
| ---
<span data-ttu-id="0bb42-379">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-380">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-381">'Blazor'</span></span>
- <span data-ttu-id="0bb42-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-382">'Identity'</span></span>
- <span data-ttu-id="0bb42-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-384">'Razor'</span></span>
- <span data-ttu-id="0bb42-385">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-386">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-387">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-388">'Blazor'</span></span>
- <span data-ttu-id="0bb42-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-389">'Identity'</span></span>
- <span data-ttu-id="0bb42-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-391">'Razor'</span></span>
- <span data-ttu-id="0bb42-392">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-393">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-394">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-395">'Blazor'</span></span>
- <span data-ttu-id="0bb42-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-396">'Identity'</span></span>
- <span data-ttu-id="0bb42-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-398">'Razor'</span></span>
- <span data-ttu-id="0bb42-399">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-400">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-401">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-402">'Blazor'</span></span>
- <span data-ttu-id="0bb42-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-403">'Identity'</span></span>
- <span data-ttu-id="0bb42-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-405">'Razor'</span></span>
- <span data-ttu-id="0bb42-406">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-407">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-408">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-409">'Blazor'</span></span>
- <span data-ttu-id="0bb42-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-410">'Identity'</span></span>
- <span data-ttu-id="0bb42-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-412">'Razor'</span></span>
- <span data-ttu-id="0bb42-413">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-414">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-415">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-416">'Blazor'</span></span>
- <span data-ttu-id="0bb42-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-417">'Identity'</span></span>
- <span data-ttu-id="0bb42-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-419">'Razor'</span></span>
- <span data-ttu-id="0bb42-420">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-421">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-422">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-423">'Blazor'</span></span>
- <span data-ttu-id="0bb42-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-424">'Identity'</span></span>
- <span data-ttu-id="0bb42-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-426">'Razor'</span></span>
- <span data-ttu-id="0bb42-427">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-428">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-429">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-430">'Blazor'</span></span>
- <span data-ttu-id="0bb42-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-431">'Identity'</span></span>
- <span data-ttu-id="0bb42-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-433">'Razor'</span></span>
- <span data-ttu-id="0bb42-434">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-435">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-436">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-437">'Blazor'</span></span>
- <span data-ttu-id="0bb42-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-438">'Identity'</span></span>
- <span data-ttu-id="0bb42-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-440">'Razor'</span></span>
- <span data-ttu-id="0bb42-441">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-442">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-443">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-444">'Blazor'</span></span>
- <span data-ttu-id="0bb42-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-445">'Identity'</span></span>
- <span data-ttu-id="0bb42-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-447">'Razor'</span></span>
- <span data-ttu-id="0bb42-448">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-449">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-450">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-451">'Blazor'</span></span>
- <span data-ttu-id="0bb42-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-452">'Identity'</span></span>
- <span data-ttu-id="0bb42-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-454">'Razor'</span></span>
- <span data-ttu-id="0bb42-455">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-456">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-457">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-458">'Blazor'</span></span>
- <span data-ttu-id="0bb42-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-459">'Identity'</span></span>
- <span data-ttu-id="0bb42-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-461">'Razor'</span></span>
- <span data-ttu-id="0bb42-462">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-463">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-464">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-465">'Blazor'</span></span>
- <span data-ttu-id="0bb42-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-466">'Identity'</span></span>
- <span data-ttu-id="0bb42-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-468">'Razor'</span></span>
- <span data-ttu-id="0bb42-469">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-470">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-471">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-472">'Blazor'</span></span>
- <span data-ttu-id="0bb42-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-473">'Identity'</span></span>
- <span data-ttu-id="0bb42-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-475">'Razor'</span></span>
- <span data-ttu-id="0bb42-476">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-476">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-477">---------------- | ---Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-478">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-479">'Blazor'</span></span>
- <span data-ttu-id="0bb42-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-480">'Identity'</span></span>
- <span data-ttu-id="0bb42-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-482">'Razor'</span></span>
- <span data-ttu-id="0bb42-483">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-483">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-484">---- | | `authentication/login`           | Löst einen Anmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="0bb42-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="0bb42-485">| | `authentication/login-callback`  | Behandelt das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="0bb42-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="0bb42-486">| | `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="0bb42-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="0bb42-487">| | `authentication/logout`          | Löst einen Abmeldevorgang aus.</span><span class="sxs-lookup"><span data-stu-id="0bb42-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="0bb42-488">| | `authentication/logout-callback` | Behandelt das Ergebnis eines Abmelde Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="0bb42-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="0bb42-489">| | `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="0bb42-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="0bb42-490">| | `authentication/logged-out`      | Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat.</span><span class="sxs-lookup"><span data-stu-id="0bb42-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="0bb42-491">| | `authentication/profile`         | Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="0bb42-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="0bb42-492">| | `authentication/register`        | Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="0bb42-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="0bb42-493">Die in der vorangehenden Tabelle gezeigten Routen können über konfiguriert werden `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="0bb42-494">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet</span><span class="sxs-lookup"><span data-stu-id="0bb42-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="0bb42-495">Im folgenden Beispiel wird allen Pfaden ein Präfix vorangestellt `/security` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="0bb42-496">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="0bb42-497">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0bb42-498">Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest, und erstellen Sie `RemoteAuthenticatorView` mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="0bb42-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="0bb42-499">Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="0bb42-500">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0bb42-500">Customize the authentication user interface</span></span>

<span data-ttu-id="0bb42-501">`RemoteAuthenticatorView`enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status.</span><span class="sxs-lookup"><span data-stu-id="0bb42-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="0bb42-502">Jeder Zustand kann durch Übergeben eines benutzerdefinierten angepasst werden `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="0bb42-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="0bb42-503">Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können Sie den `RemoteAuthenticatorView` wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="0bb42-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="0bb42-504">`Authentication`Komponente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="0bb42-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="0bb42-505">Der `RemoteAuthenticatorView` verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0bb42-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="0bb42-506">Route</span><span class="sxs-lookup"><span data-stu-id="0bb42-506">Route</span></span>                            | <span data-ttu-id="0bb42-507">Fragment</span><span class="sxs-lookup"><span data-stu-id="0bb42-507">Fragment</span></span>                |
| ---
<span data-ttu-id="0bb42-508">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-509">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-510">'Blazor'</span></span>
- <span data-ttu-id="0bb42-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-511">'Identity'</span></span>
- <span data-ttu-id="0bb42-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-513">'Razor'</span></span>
- <span data-ttu-id="0bb42-514">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-515">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-516">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-517">'Blazor'</span></span>
- <span data-ttu-id="0bb42-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-518">'Identity'</span></span>
- <span data-ttu-id="0bb42-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-520">'Razor'</span></span>
- <span data-ttu-id="0bb42-521">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-522">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-523">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-524">'Blazor'</span></span>
- <span data-ttu-id="0bb42-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-525">'Identity'</span></span>
- <span data-ttu-id="0bb42-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-527">'Razor'</span></span>
- <span data-ttu-id="0bb42-528">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-529">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-530">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-531">'Blazor'</span></span>
- <span data-ttu-id="0bb42-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-532">'Identity'</span></span>
- <span data-ttu-id="0bb42-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-534">'Razor'</span></span>
- <span data-ttu-id="0bb42-535">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-536">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-537">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-538">'Blazor'</span></span>
- <span data-ttu-id="0bb42-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-539">'Identity'</span></span>
- <span data-ttu-id="0bb42-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-541">'Razor'</span></span>
- <span data-ttu-id="0bb42-542">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-543">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-544">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-545">'Blazor'</span></span>
- <span data-ttu-id="0bb42-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-546">'Identity'</span></span>
- <span data-ttu-id="0bb42-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-548">'Razor'</span></span>
- <span data-ttu-id="0bb42-549">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-550">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-551">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-552">'Blazor'</span></span>
- <span data-ttu-id="0bb42-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-553">'Identity'</span></span>
- <span data-ttu-id="0bb42-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-555">'Razor'</span></span>
- <span data-ttu-id="0bb42-556">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-557">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-558">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-559">'Blazor'</span></span>
- <span data-ttu-id="0bb42-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-560">'Identity'</span></span>
- <span data-ttu-id="0bb42-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-562">'Razor'</span></span>
- <span data-ttu-id="0bb42-563">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-564">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-565">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-566">'Blazor'</span></span>
- <span data-ttu-id="0bb42-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-567">'Identity'</span></span>
- <span data-ttu-id="0bb42-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-569">'Razor'</span></span>
- <span data-ttu-id="0bb42-570">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-571">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-572">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-573">'Blazor'</span></span>
- <span data-ttu-id="0bb42-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-574">'Identity'</span></span>
- <span data-ttu-id="0bb42-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-576">'Razor'</span></span>
- <span data-ttu-id="0bb42-577">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-578">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-579">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-580">'Blazor'</span></span>
- <span data-ttu-id="0bb42-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-581">'Identity'</span></span>
- <span data-ttu-id="0bb42-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-583">'Razor'</span></span>
- <span data-ttu-id="0bb42-584">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-585">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-586">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-587">'Blazor'</span></span>
- <span data-ttu-id="0bb42-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-588">'Identity'</span></span>
- <span data-ttu-id="0bb42-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-590">'Razor'</span></span>
- <span data-ttu-id="0bb42-591">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-592">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-593">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-594">'Blazor'</span></span>
- <span data-ttu-id="0bb42-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-595">'Identity'</span></span>
- <span data-ttu-id="0bb42-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-597">'Razor'</span></span>
- <span data-ttu-id="0bb42-598">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-599">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-600">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-601">'Blazor'</span></span>
- <span data-ttu-id="0bb42-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-602">'Identity'</span></span>
- <span data-ttu-id="0bb42-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-604">'Razor'</span></span>
- <span data-ttu-id="0bb42-605">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-605">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-606">---------------- | ---Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-607">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-608">'Blazor'</span></span>
- <span data-ttu-id="0bb42-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-609">'Identity'</span></span>
- <span data-ttu-id="0bb42-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-611">'Razor'</span></span>
- <span data-ttu-id="0bb42-612">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-613">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-614">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-615">'Blazor'</span></span>
- <span data-ttu-id="0bb42-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-616">'Identity'</span></span>
- <span data-ttu-id="0bb42-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-618">'Razor'</span></span>
- <span data-ttu-id="0bb42-619">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-620">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-621">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-622">'Blazor'</span></span>
- <span data-ttu-id="0bb42-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-623">'Identity'</span></span>
- <span data-ttu-id="0bb42-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-625">'Razor'</span></span>
- <span data-ttu-id="0bb42-626">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-627">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-628">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-629">'Blazor'</span></span>
- <span data-ttu-id="0bb42-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-630">'Identity'</span></span>
- <span data-ttu-id="0bb42-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-632">'Razor'</span></span>
- <span data-ttu-id="0bb42-633">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-634">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-635">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-636">'Blazor'</span></span>
- <span data-ttu-id="0bb42-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-637">'Identity'</span></span>
- <span data-ttu-id="0bb42-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-639">'Razor'</span></span>
- <span data-ttu-id="0bb42-640">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-641">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-642">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-643">'Blazor'</span></span>
- <span data-ttu-id="0bb42-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-644">'Identity'</span></span>
- <span data-ttu-id="0bb42-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-646">'Razor'</span></span>
- <span data-ttu-id="0bb42-647">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-648">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-649">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-650">'Blazor'</span></span>
- <span data-ttu-id="0bb42-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-651">'Identity'</span></span>
- <span data-ttu-id="0bb42-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-653">'Razor'</span></span>
- <span data-ttu-id="0bb42-654">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-655">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-656">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-657">'Blazor'</span></span>
- <span data-ttu-id="0bb42-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-658">'Identity'</span></span>
- <span data-ttu-id="0bb42-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-660">'Razor'</span></span>
- <span data-ttu-id="0bb42-661">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0bb42-662">Title: "ASP.net Core Blazor Webassembly zusätzliche Sicherheits Szenarios" Author: Description: "erfahren Sie, wie Blazor Webassembly für zusätzliche Sicherheits Szenarios konfiguriert wird."</span><span class="sxs-lookup"><span data-stu-id="0bb42-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0bb42-663">monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="0bb42-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0bb42-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-664">'Blazor'</span></span>
- <span data-ttu-id="0bb42-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0bb42-665">'Identity'</span></span>
- <span data-ttu-id="0bb42-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0bb42-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="0bb42-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0bb42-667">'Razor'</span></span>
- <span data-ttu-id="0bb42-668">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="0bb42-668">'SignalR' uid:</span></span> 

<span data-ttu-id="0bb42-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="0bb42-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="0bb42-670">Benutzer anpassen</span><span class="sxs-lookup"><span data-stu-id="0bb42-670">Customize the user</span></span>

<span data-ttu-id="0bb42-671">Benutzer, die an die APP gebunden sind, können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="0bb42-672">Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr` Anspruch für jede Authentifizierungsmethode des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="0bb42-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="0bb42-673">Erstellen Sie eine Klasse, die die- `RemoteUserAccount` Klasse erweitert:</span><span class="sxs-lookup"><span data-stu-id="0bb42-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="0bb42-674">Erstellen Sie eine Factory, die Folgendes erweitert `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="0bb42-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="0bb42-675">Registrieren Sie `CustomAccountFactory` für den verwendeten Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="0bb42-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="0bb42-676">Jede der folgenden Registrierungen ist gültig:</span><span class="sxs-lookup"><span data-stu-id="0bb42-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="0bb42-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="0bb42-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="0bb42-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="0bb42-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="0bb42-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="0bb42-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="0bb42-680">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0bb42-680">Support prerendering with authentication</span></span>

<span data-ttu-id="0bb42-681">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="0bb42-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="0bb42-682">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="0bb42-683">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="0bb42-684">Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="0bb42-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="0bb42-685">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="0bb42-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="0bb42-686">Ersetzen Sie in der Methode `Startup.Configure` der Server-App `endpoints.MapFallbackToFile("index.html")` durch `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="0bb42-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="0bb42-687">Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="0bb42-688">Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0bb42-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="0bb42-689">Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein.</span><span class="sxs-lookup"><span data-stu-id="0bb42-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="0bb42-690">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="0bb42-690">Update the file's contents:</span></span>

* <span data-ttu-id="0bb42-691">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="0bb42-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="0bb42-692">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0bb42-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="0bb42-693">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="0bb42-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="0bb42-694">Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="0bb42-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="0bb42-695">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="0bb42-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="0bb42-696">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="0bb42-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="0bb42-697">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="0bb42-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="0bb42-698">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="0bb42-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="0bb42-699">Szenario:</span><span class="sxs-lookup"><span data-stu-id="0bb42-699">In this scenario:</span></span>

* <span data-ttu-id="0bb42-700">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="0bb42-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="0bb42-701">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="0bb42-702">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="0bb42-703">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="0bb42-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="0bb42-704">Konfigurieren Sie Identity mit einem Drittanbieter-Anmelde Anbieter.</span><span class="sxs-lookup"><span data-stu-id="0bb42-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="0bb42-705">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="0bb42-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="0bb42-706">Wenn sich ein Benutzer anmeldet, Identity sammelt Zugriffs-und Aktualisierungs Token im Rahmen des Authentifizierungs Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="0bb42-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="0bb42-707">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0bb42-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="0bb42-708">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="0bb42-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="0bb42-709">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="0bb42-710">Verwenden Sie von dort aus das Drittanbieter-Zugriffs Token, um API-Ressourcen von Drittanbietern direkt von Identity auf dem Client aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="0bb42-711">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-711">We don't recommend this approach.</span></span> <span data-ttu-id="0bb42-712">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="0bb42-713">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="0bb42-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="0bb42-714">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="0bb42-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="0bb42-715">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="0bb42-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="0bb42-716">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="0bb42-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="0bb42-717">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="0bb42-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="0bb42-718">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="0bb42-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="0bb42-719">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0bb42-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="0bb42-720">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="0bb42-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="0bb42-721">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="0bb42-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="0bb42-722">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="0bb42-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="0bb42-723">Verwenden von Open ID Connect (oidc) v 2.0-Endpunkten</span><span class="sxs-lookup"><span data-stu-id="0bb42-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="0bb42-724">In der Authentifizierungs Bibliothek und in den Vorlagen werden die Blazor Endpunkte von Open ID Connect (oidc) v 1.0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="0bb42-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="0bb42-725">Um einen v 2.0-Endpunkt zu verwenden, konfigurieren Sie die Option JWT-Träger <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0bb42-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="0bb42-726">Im folgenden Beispiel wird Aad für v 2.0 konfiguriert, indem ein `v2.0` Segment an die-Eigenschaft angehängt wird `Authority` :</span><span class="sxs-lookup"><span data-stu-id="0bb42-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="0bb42-727">Alternativ kann die Einstellung in der APP-Einstellungsdatei (*appSettings. JSON*) vorgenommen werden:</span><span class="sxs-lookup"><span data-stu-id="0bb42-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="0bb42-728">Wenn das über ein Segment an die Autorität nicht für den oidc-Anbieter der APP geeignet ist, z. b. bei nicht-Aad-Anbietern, legen Sie die- `Authority` Eigenschaft direkt fest.</span><span class="sxs-lookup"><span data-stu-id="0bb42-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="0bb42-729">Legen Sie die-Eigenschaft entweder in `JwtBearerOptions` oder in der APP-Einstellungsdatei mit dem `Authority` Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="0bb42-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="0bb42-730">Die Liste der Ansprüche im ID-Token ändert sich für v 2.0-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="0bb42-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="0bb42-731">Weitere Informationen finden Sie unter [Gründe für die Aktualisierung auf Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="0bb42-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
