---
title: Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly
author: guardrex
description: Informationen zum Konfigurieren von Blazor WebAssembly für zusätzliche Sicherheitsszenarios
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 2020b422ad48a9c4c52f2670fd3b5054aa4d60c5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103289"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="0181a-103">Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0181a-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="0181a-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0181a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="0181a-105">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="0181a-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="0181a-106">Der Dienst <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mit <xref:System.Net.Http.HttpClient> verwendet werden, um Zugriffstoken an ausgehende Anforderungen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="0181a-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="0181a-107">Token werden mithilfe des vorhandenen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>-Dienstes abgerufen.</span><span class="sxs-lookup"><span data-stu-id="0181a-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="0181a-108">Wenn ein Token nicht abgerufen werden kann, wird eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0181a-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="0181a-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> verfügt über eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>-Methode, die verwendet werden kann, um den Benutzer zum Abrufen eines neuen Token zum Identitätsanbieter zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="0181a-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="0181a-110">Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mithilfe der Methode <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> mit den autorisierten URLs, Bereichen und der Rückgabe-URL konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="0181a-111">Verwenden Sie einen der folgenden Ansätze, um einen Meldungshandler für ausgehende Anforderungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="0181a-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="0181a-112">[Die benutzerdefinierte Klasse „AuthorizationMessageHandler“](#custom-authorizationmessagehandler-class) (*empfohlen*)</span><span class="sxs-lookup"><span data-stu-id="0181a-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="0181a-113">Konfigurieren des AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="0181a-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="0181a-114">Die benutzerdefinierte Klasse „AuthorizationMessageHandler“</span><span class="sxs-lookup"><span data-stu-id="0181a-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="0181a-115">Im folgenden Beispiel wird die benutzerdefinierte Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> erweitert, die zum Konfigurieren von <xref:System.Net.Http.HttpClient> verwendet werden kann:</span><span class="sxs-lookup"><span data-stu-id="0181a-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="0181a-116">In `Program.Main` (*Program.cs*) wird ein <xref:System.Net.Http.HttpClient> mit dem benutzerdefinierten Handler für Autorisierungsmeldungen konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="0181a-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="0181a-117">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um mithilfe des [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Musters autorisierte Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0181a-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="0181a-118">Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket) erstellt wird, werden dem <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an die Server-API gestellt werden:</span><span class="sxs-lookup"><span data-stu-id="0181a-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="0181a-119">Konfigurieren des AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="0181a-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="0181a-120">Im folgenden Beispiel konfiguriert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> einen <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0181a-121">Der Einfachheit halber ist ein <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> enthalten, der mit der Basisadresse der App als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="0181a-122">Die Blazor WebAssembly-Vorlagen mit aktivierter Authentifizierung verwenden jetzt die <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket) im Server-API-Projekt, um einen <xref:System.Net.Http.HttpClient> mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> einzurichten:</span><span class="sxs-lookup"><span data-stu-id="0181a-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="0181a-123">Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> im obenstehenden Beispiel erstellt wird, werden dem <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an das Serverprojekt gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="0181a-124">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen:</span><span class="sxs-lookup"><span data-stu-id="0181a-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="0181a-125">Typisierte HttpClient-Instanz</span><span class="sxs-lookup"><span data-stu-id="0181a-125">Typed HttpClient</span></span>

<span data-ttu-id="0181a-126">Es kann ein typisierter Client definiert werden, der alle Probleme mit dem HTTP- und Tokenabruf innerhalb einer einzelnen Klasse bearbeitet.</span><span class="sxs-lookup"><span data-stu-id="0181a-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="0181a-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="0181a-127">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="0181a-128">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="0181a-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="0181a-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="0181a-130">`FetchData`-Komponente (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="0181a-130">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="0181a-131">Konfigurieren des HttpClient-Handlers</span><span class="sxs-lookup"><span data-stu-id="0181a-131">Configure the HttpClient handler</span></span>

<span data-ttu-id="0181a-132">Der Handler kann mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> für ausgehende HTTP-Anforderungen weiter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-132">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="0181a-133">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="0181a-134">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="0181a-134">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="0181a-135">Wenn die Blazor WebAssembly-App normalerweise einen sicheren Standard-<xref:System.Net.Http.HttpClient> verwendet, kann die App auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen stellen, indem sie einen benannten <xref:System.Net.Http.HttpClient> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="0181a-135">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="0181a-136">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-136">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="0181a-137">Die obenstehende Registrierung erfolgt zusätzlich zur bereits vorhandenen sicheren Standardregistrierung des <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="0181a-137">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="0181a-138">Eine Komponente erstellt den <xref:System.Net.Http.HttpClient> aus der <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket), um nicht authentifizierte oder nicht autorisierte Anforderungen zu stellen:</span><span class="sxs-lookup"><span data-stu-id="0181a-138">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="0181a-139">Der Controller in der Server-API (`WeatherForecastNoAuthenticationController`) für das obenstehende Beispiel ist nicht mit dem Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="0181a-139">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="0181a-140">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="0181a-140">Request additional access tokens</span></span>

<span data-ttu-id="0181a-141">Zugriffstoken können mithilfe von `IAccessTokenProvider.RequestAccessToken` manuell abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-141">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="0181a-142">Im folgenden Beispiel werden zusätzliche Microsoft Graph-API-Bereiche für Azure Active Directory (ADD) von einer App benötigt, damit diese Benutzerdaten lesen und E-Mails senden kann.</span><span class="sxs-lookup"><span data-stu-id="0181a-142">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="0181a-143">Nachdem Sie die Berechtigungen für die Microsoft Graph-API im AAD-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="0181a-143">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="0181a-144">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-144">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0181a-145">Die Methode `IAccessTokenProvider.RequestToken` stellt eine Überladung bereit, mit der Apps Zugriffstoken mit vorgegebenen Bereichen bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="0181a-145">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="0181a-146">In einer Razor-Komponente:</span><span class="sxs-lookup"><span data-stu-id="0181a-146">In a Razor component:</span></span>

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

<span data-ttu-id="0181a-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> gibt Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="0181a-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="0181a-148">`true` mit dem `token` zur Verwendung</span><span class="sxs-lookup"><span data-stu-id="0181a-148">`true` with the `token` for use.</span></span>
* <span data-ttu-id="0181a-149">`false`, wenn das Token nicht abgerufen wird</span><span class="sxs-lookup"><span data-stu-id="0181a-149">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="0181a-150">HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="0181a-150">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="0181a-151">Wenn Sie WebAssembly in einer Blazor WebAssembly-App ausführen, können der [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> zum Anpassen von Anforderungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-151">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="0181a-152">Beispielsweise können Sie die HTTP-Methode und die Anforderungsheader angeben.</span><span class="sxs-lookup"><span data-stu-id="0181a-152">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="0181a-153">Die folgende Komponente sendet eine `POST`-Anforderung an einen To-Do-List-API-Endpunkt auf dem Server und zeigt den Antworttext an:</span><span class="sxs-lookup"><span data-stu-id="0181a-153">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="0181a-154">Die .NET-WebAssembly-Implementierung von <xref:System.Net.Http.HttpClient> verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="0181a-154">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="0181a-155">FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="0181a-155">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="0181a-156">HTTP FETCH-Anforderungsoptionen können mit <xref:System.Net.Http.HttpRequestMessage>-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="0181a-156">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="0181a-157">Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="0181a-157">Extension method</span></span> | <span data-ttu-id="0181a-158">FETCH-Anforderungseigenschaft</span><span class="sxs-lookup"><span data-stu-id="0181a-158">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="0181a-159">credentials</span><span class="sxs-lookup"><span data-stu-id="0181a-159">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="0181a-160">cache</span><span class="sxs-lookup"><span data-stu-id="0181a-160">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="0181a-161">mode</span><span class="sxs-lookup"><span data-stu-id="0181a-161">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="0181a-162">integrity</span><span class="sxs-lookup"><span data-stu-id="0181a-162">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="0181a-163">Sie können weitere Optionen mithilfe der allgemeineren <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>-Erweiterungsmethode festlegen.</span><span class="sxs-lookup"><span data-stu-id="0181a-163">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="0181a-164">Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="0181a-164">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="0181a-165">Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A>-Erweiterungsmethode für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0181a-165">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="0181a-166">Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="0181a-166">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="0181a-167">Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="0181a-167">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="0181a-168">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-168">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="0181a-169">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="0181a-169">The following policy includes configuration for:</span></span>

* <span data-ttu-id="0181a-170">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="0181a-170">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="0181a-171">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="0181a-171">Any method (verb).</span></span>
* <span data-ttu-id="0181a-172">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="0181a-172">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="0181a-173">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="0181a-173">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="0181a-174">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="0181a-174">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="0181a-175">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="0181a-175">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="0181a-176">Behandeln von Fehlern mit Tokenanforderungen</span><span class="sxs-lookup"><span data-stu-id="0181a-176">Handle token request errors</span></span>

<span data-ttu-id="0181a-177">Wenn eine Single-Page-Anwendung (Single Page Application, SPA) einen Benutzer mithilfe von Open ID Connect (OICD) authentifiziert, wird der Authentifizierungsstatus lokal innerhalb der SPA und im Identity-Anbieter (Identity Provider, IP) in Form eines Sitzungscookies verwaltet, das festgelegt wird, wenn der Benutzer seine Anmeldeinformationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="0181a-177">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="0181a-178">Die Token, die der IP für den Benutzer ausgibt, sind in der Regel nur für kurze Zeit (etwa eine Stunde) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="0181a-178">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="0181a-179">Andernfalls wird der Benutzer nach Ablauf der ausgegebenen Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="0181a-179">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="0181a-180">In den meisten Fällen sind die OICD-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer noch mal authentifizieren muss, weil ein Authentifizierungsstatus oder eine Sitzung im IP gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-180">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="0181a-181">Manchmal kann der Client ohne Benutzerinteraktion keine Token abrufen. Dies ist zum Beispiel dann der Fall, wenn sich der Benutzer aus einem beliebigen Grund explizit vom IP abmeldet.</span><span class="sxs-lookup"><span data-stu-id="0181a-181">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="0181a-182">Dies passiert, wenn der Benutzer `https://login.microsoftonline.com` aufruft und sich abmeldet. In diesen Szenarios weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Alle Token, die im Client enthalten sind, sind dann möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="0181a-182">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="0181a-183">Außerdem kann der Client keine neuen Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-183">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="0181a-184">Diese Szenarios sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="0181a-184">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="0181a-185">Sie gehören bei SPAs dazu.</span><span class="sxs-lookup"><span data-stu-id="0181a-185">They are part of the nature of SPAs.</span></span> <span data-ttu-id="0181a-186">Wenn eine SPA, die Cookies verwendet, eine Server-API aufruft, wird ein Fehler ausgelöst, wenn das Authentifizierungscookie entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="0181a-186">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="0181a-187">Wenn eine App API-Aufrufe für geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="0181a-187">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="0181a-188">Damit ein neues Zugriffstoken zum Aufrufen der API bereitgestellt werden kann, muss der Benutzer sich möglicherweise noch einmal authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="0181a-188">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="0181a-189">Auch wenn der Client über ein Token verfügt, das anscheinend gültig ist, kann der Serveraufruf fehlschlagen, weil das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="0181a-189">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="0181a-190">Wenn die App ein Token anfordert, gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="0181a-190">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="0181a-191">Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="0181a-191">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="0181a-192">Die Anforderung schlägt fehl, und die App muss den Benutzer noch einmal authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0181a-192">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="0181a-193">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="0181a-193">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="0181a-194">Es gibt mehrere Ansätze, die unterschiedlich komplex sind:</span><span class="sxs-lookup"><span data-stu-id="0181a-194">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="0181a-195">Speichern Sie den aktuellen Seitenzustand im Sitzungsspeicher.</span><span class="sxs-lookup"><span data-stu-id="0181a-195">Store the current page state in session storage.</span></span> <span data-ttu-id="0181a-196">Überprüfen Sie während des [Lebenszyklusereignisses „OnInitializedAsync“](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), ob der Zustand wiederhergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="0181a-196">During the [OnInitializedAsync lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="0181a-197">Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand aktualisieren muss.</span><span class="sxs-lookup"><span data-stu-id="0181a-197">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="0181a-198">Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="0181a-198">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="0181a-199">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="0181a-199">The following example shows how to:</span></span>

* <span data-ttu-id="0181a-200">Speichern des Zustands vor der Umleitung auf die Anmeldeseite</span><span class="sxs-lookup"><span data-stu-id="0181a-200">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="0181a-201">Wiederherstellen des vorherigen Zustands nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters</span><span class="sxs-lookup"><span data-stu-id="0181a-201">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="0181a-202">Speichern des App-Zustands vor einem Authentifizierungsvorgang</span><span class="sxs-lookup"><span data-stu-id="0181a-202">Save app state before an authentication operation</span></span>

<span data-ttu-id="0181a-203">Während eines Authentifizierungsvorgangs sollten Sie ggf. den App-Zustand speichern, bevor der Browser an den IP umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="0181a-203">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="0181a-204">Dies bietet sich zum Beispiel dann an, wenn Sie einen Zustandscontainer verwenden und den Zustand nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="0181a-204">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="0181a-205">Sie können ein benutzerdefiniertes Zustandsobjekt für die Authentifizierung verwenden, um den App-spezifischen Zustand oder einen Verweis auf diesen zu speichern und diesen Zustand nach erfolgreichem Abschluss des Authentifizierungsvorgangs wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="0181a-205">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="0181a-206">Im folgenden Beispiel wird dieser Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="0181a-206">The following example demonstrates the approach.</span></span>

<span data-ttu-id="0181a-207">In der App wird eine Zustandscontainerklasse mit Eigenschaften erstellt, die die Zustandswerte der App enthalten.</span><span class="sxs-lookup"><span data-stu-id="0181a-207">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="0181a-208">Im folgenden Beispiel wird der Container verwendet, um den Leistungsindikatorwert der Komponente `Counter` der Standardvorlage (*Pages/Counter.razor*) beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="0181a-208">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="0181a-209">Methoden zum (De-)Serialisieren des Containers basierend auf <xref:System.Text.Json></span><span class="sxs-lookup"><span data-stu-id="0181a-209">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="0181a-210">Die Komponente `Counter` verwendet den Zustandscontainer, um den Wert `currentCount` außerhalb der Komponente beizubehalten:</span><span class="sxs-lookup"><span data-stu-id="0181a-210">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="0181a-211">Erstellen Sie einen `ApplicationAuthenticationState` aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="0181a-211">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="0181a-212">Stellen Sie eine `Id`-Eigenschaft bereit, die als Bezeichner für den lokal gespeicherten Zustand fungiert.</span><span class="sxs-lookup"><span data-stu-id="0181a-212">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="0181a-213">*ApplicationAuthenticationState.cs:*</span><span class="sxs-lookup"><span data-stu-id="0181a-213">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="0181a-214">Die Komponente `Authentication` (*Pages/Authentication.razor*) speichert den Zustand der App mithilfe des lokalen Sitzungsspeichers über die `StateContainer`-(De-)Serialisierungsmethoden `GetStateForLocalStorage` und `SetStateFromLocalStorage` und stellt diesen auch darüber wieder her:</span><span class="sxs-lookup"><span data-stu-id="0181a-214">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="0181a-215">In diesem Beispiel wird AAD für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="0181a-215">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="0181a-216">In `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-216">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="0181a-217">Der `ApplicationAuthenticationState` wird als `RemoteAuthenticationState`-Typ der Microsoft-Authentifizierungsbibliothek konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="0181a-217">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="0181a-218">Der Zustandscontainer wird im Dienstcontainer registriert.</span><span class="sxs-lookup"><span data-stu-id="0181a-218">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="0181a-219">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="0181a-219">Customize app routes</span></span>

<span data-ttu-id="0181a-220">Standardmäßig verwendet die Bibliothek [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) die Routen, die in der folgenden Tabelle angezeigt werden, um unterschiedliche Authentifizierungszustände darzustellen.</span><span class="sxs-lookup"><span data-stu-id="0181a-220">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="0181a-221">Route</span><span class="sxs-lookup"><span data-stu-id="0181a-221">Route</span></span>                            | <span data-ttu-id="0181a-222">Zweck</span><span class="sxs-lookup"><span data-stu-id="0181a-222">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="0181a-223">Löst einen Anmeldevorgang aus</span><span class="sxs-lookup"><span data-stu-id="0181a-223">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="0181a-224">Verarbeitet das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="0181a-224">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="0181a-225">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus einem beliebigen Grund fehlschlägt</span><span class="sxs-lookup"><span data-stu-id="0181a-225">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="0181a-226">Löst einen Abmeldevorgang aus</span><span class="sxs-lookup"><span data-stu-id="0181a-226">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="0181a-227">Verarbeitet das Ergebnis eines beliebigen Abmeldevorgangs</span><span class="sxs-lookup"><span data-stu-id="0181a-227">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="0181a-228">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus einem beliebigen Grund fehlschlägt</span><span class="sxs-lookup"><span data-stu-id="0181a-228">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="0181a-229">Gibt an, dass ein Benutzer sich erfolgreich abgemeldet hat</span><span class="sxs-lookup"><span data-stu-id="0181a-229">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="0181a-230">Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten</span><span class="sxs-lookup"><span data-stu-id="0181a-230">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="0181a-231">Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren</span><span class="sxs-lookup"><span data-stu-id="0181a-231">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="0181a-232">Die in der obigen Tabelle aufgeführten Routen können über <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-232">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0181a-233">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die App über eine Route verfügt, die die einzelnen Pfade verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="0181a-233">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="0181a-234">Im folgenden Beispiel haben alle Pfade das Präfix `/security`.</span><span class="sxs-lookup"><span data-stu-id="0181a-234">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="0181a-235">Komponente `Authentication` (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="0181a-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="0181a-236">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0181a-236">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0181a-237">Wenn vollkommen unterschiedliche Pfade verlangt werden, legen Sie die Routen wie zuvor beschrieben fest, und rendern Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="0181a-237">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="0181a-238">Wenn Sie sich hierfür entscheiden, können Sie die Benutzeroberfläche auf mehrere Seiten aufteilen.</span><span class="sxs-lookup"><span data-stu-id="0181a-238">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="0181a-239">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0181a-239">Customize the authentication user interface</span></span>

<span data-ttu-id="0181a-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> enthält Standardbenutzeroberflächenelemente für alle Authentifizierungszustände.</span><span class="sxs-lookup"><span data-stu-id="0181a-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="0181a-241">Jeder Zustand kann angepasst werden, indem ein benutzerdefiniertes <xref:Microsoft.AspNetCore.Components.RenderFragment>übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="0181a-241">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="0181a-242">Wenn Sie den angezeigten Text während des ersten Anmeldeprozesses anpassen möchten, können Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="0181a-242">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="0181a-243">Komponente `Authentication` (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="0181a-243">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="0181a-244">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das für die Authentifizierungsrouten in der folgenden Tabelle verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0181a-244">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="0181a-245">Route</span><span class="sxs-lookup"><span data-stu-id="0181a-245">Route</span></span>                            | <span data-ttu-id="0181a-246">Fragment</span><span class="sxs-lookup"><span data-stu-id="0181a-246">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="0181a-247">Anpassen des Benutzers</span><span class="sxs-lookup"><span data-stu-id="0181a-247">Customize the user</span></span>

<span data-ttu-id="0181a-248">Benutzer, die an die App gebunden sind, können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-248">Users bound to the app can be customized.</span></span> <span data-ttu-id="0181a-249">Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr`-Anspruch für jede Authentifizierungsmethode des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="0181a-249">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="0181a-250">Erstellen Sie eine Klasse, die die Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert:</span><span class="sxs-lookup"><span data-stu-id="0181a-250">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="0181a-251">Erstellen Sie eine Factory, die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> erweitert:</span><span class="sxs-lookup"><span data-stu-id="0181a-251">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="0181a-252">Registrieren Sie die `CustomAccountFactory` für den verwendeten Authentifizierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="0181a-252">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="0181a-253">Sie können die folgenden Registrierungen verwenden:</span><span class="sxs-lookup"><span data-stu-id="0181a-253">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="0181a-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="0181a-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="0181a-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="0181a-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="0181a-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="0181a-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="0181a-257">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0181a-257">Support prerendering with authentication</span></span>

<span data-ttu-id="0181a-258">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="0181a-258">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="0181a-259">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-259">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="0181a-260">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-260">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="0181a-261">Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="0181a-261">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="0181a-262">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="0181a-262">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="0181a-263">Ersetzen Sie in der Methode `Startup.Configure` der Server-App [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) durch [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="0181a-263">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="0181a-264">Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-264">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="0181a-265">Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0181a-265">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="0181a-266">Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein.</span><span class="sxs-lookup"><span data-stu-id="0181a-266">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="0181a-267">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="0181a-267">Update the file's contents:</span></span>

* <span data-ttu-id="0181a-268">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="0181a-268">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="0181a-269">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0181a-269">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="0181a-270">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="0181a-270">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="0181a-271">Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="0181a-271">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="0181a-272">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="0181a-272">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="0181a-273">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="0181a-273">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="0181a-274">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="0181a-274">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="0181a-275">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="0181a-275">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="0181a-276">Szenario:</span><span class="sxs-lookup"><span data-stu-id="0181a-276">In this scenario:</span></span>

* <span data-ttu-id="0181a-277">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="0181a-277">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="0181a-278">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-278">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="0181a-279">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0181a-279">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="0181a-280">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="0181a-280">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="0181a-281">Konfigurieren Sie Identity mit einem dritten Anmeldeanbieter.</span><span class="sxs-lookup"><span data-stu-id="0181a-281">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="0181a-282">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="0181a-282">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="0181a-283">Wenn sich ein Benutzer anmeldet, erfasst Identity als Teil des Authentifizierungsprozesses die Token für Zugriff und Aktualisierung.</span><span class="sxs-lookup"><span data-stu-id="0181a-283">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="0181a-284">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0181a-284">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="0181a-285">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="0181a-285">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="0181a-286">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0181a-286">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="0181a-287">Verwenden Sie dann das Zugriffstoken des Drittanbieters, um API-Ressourcen von Drittanbietern direkt über Identity auf dem Client abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0181a-287">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="0181a-288">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="0181a-288">We don't recommend this approach.</span></span> <span data-ttu-id="0181a-289">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="0181a-289">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="0181a-290">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="0181a-290">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="0181a-291">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="0181a-291">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="0181a-292">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="0181a-292">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="0181a-293">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="0181a-293">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="0181a-294">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="0181a-294">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="0181a-295">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="0181a-295">Make an API call from the client to the server API.</span></span> <span data-ttu-id="0181a-296">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="0181a-296">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="0181a-297">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="0181a-297">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="0181a-298">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="0181a-298">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="0181a-299">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="0181a-299">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="0181a-300">Verwenden von OpenID Connect 2.0-Endpunkten (OIDC)</span><span class="sxs-lookup"><span data-stu-id="0181a-300">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="0181a-301">Die Authentifizierungsbibliothek sowie Blazor-Vorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC).</span><span class="sxs-lookup"><span data-stu-id="0181a-301">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="0181a-302">Wenn Sie einen Endpunkt der Version 2.0 verwenden möchten, konfigurieren Sie die JWT-Bearer-Option <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="0181a-302">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="0181a-303">Im folgenden Beispiel wird AAD für Version 2.0 konfiguriert, indem ein `v2.0`-Segment an die Eigenschaft <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> angehängt wird:</span><span class="sxs-lookup"><span data-stu-id="0181a-303">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="0181a-304">Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (*appsettings.json*) festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="0181a-304">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="0181a-305">Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest.</span><span class="sxs-lookup"><span data-stu-id="0181a-305">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="0181a-306">Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der Datei mit den App-Einstellungen (*appsettings.json*) mit dem Schlüssel `Authority` fest.</span><span class="sxs-lookup"><span data-stu-id="0181a-306">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="0181a-307">Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0.</span><span class="sxs-lookup"><span data-stu-id="0181a-307">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="0181a-308">Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="0181a-308">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
