---
title: Zusätzliche Blazor Sicherheitsszenarien für ASP.net Core Server
author: guardrex
description: Erfahren Sie, wie Blazor Sie Server für zusätzliche Sicherheitsszenarien konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772803"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="186ae-103">Zusätzliche Blazor Sicherheitsszenarien für ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="186ae-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="186ae-104">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="186ae-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="186ae-105">Übergeben von Token an Blazor eine Server-App</span><span class="sxs-lookup"><span data-stu-id="186ae-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="186ae-106">Token, die außerhalb der Razor Komponenten in einer Blazor Server-app verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="186ae-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="186ae-107">Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices` Beispiels, finden Sie unter [übergeben von Token an eine serverseitige Blazor Anwendung](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="186ae-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="186ae-108">Authentifizieren Sie Blazor die Server-APP wie bei einer regulären Razor Seite oder MVC-app.</span><span class="sxs-lookup"><span data-stu-id="186ae-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="186ae-109">Stellen Sie die Token für das Authentifizierungs Cookie bereit, und speichern Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="186ae-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="186ae-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="186ae-110">For example:</span></span>

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

<span data-ttu-id="186ae-111">Definieren Sie eine Klasse, die den anfänglichen App-Zustand mit den Zugriffs-und Aktualisierungs Token übergibt:</span><span class="sxs-lookup"><span data-stu-id="186ae-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="186ae-112">Definieren eines **Bereichs** bezogenen tokenanbienerdiensts, der in Blazor der APP verwendet werden kann, um die Token von der [Abhängigkeitsinjektion (di)](xref:blazor/dependency-injection)aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="186ae-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="186ae-113">Fügen `Startup.ConfigureServices`Sie in Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="186ae-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="186ae-114">Erstellen Sie in der Datei " *_Host. cshtml* " und `InitialApplicationState` die Instanz von, und übergeben Sie Sie als Parameter an die APP:</span><span class="sxs-lookup"><span data-stu-id="186ae-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="186ae-115">Lösen Sie `App` in der Komponente (*app. Razor*) den Dienst auf, und initialisieren Sie ihn mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="186ae-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="186ae-116">Fügen Sie im Dienst, der eine sichere API-Anforderung sendet, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="186ae-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
