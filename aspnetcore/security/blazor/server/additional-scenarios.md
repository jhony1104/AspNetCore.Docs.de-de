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
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206347"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Zusätzliche Blazor Sicherheitsszenarien für ASP.net Core Server

Von [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Übergeben von Token an Blazor eine Server-App

Token, die außerhalb der Razor-Komponenten in Blazor einer Server-app verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten weitergegeben werden. Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices` Beispiels, finden Sie unter [übergeben von Token an eine serverseitige Blazor Anwendung](https://github.com/javiercn/blazor-server-aad-sample).

Authentifizieren Sie Blazor die Server-APP wie bei einer regulären Razor Pages oder MVC-app. Stellen Sie die Token für das Authentifizierungs Cookie bereit, und speichern Sie Sie. Zum Beispiel:

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

Definieren Sie eine Klasse, die den anfänglichen App-Zustand mit den Zugriffs-und Aktualisierungs Token übergibt:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definieren eines **Bereichs** bezogenen tokenanbienerdiensts, der in Blazor der APP verwendet werden kann, um die Token von der [Abhängigkeitsinjektion (di)](xref:blazor/dependency-injection)aufzulösen:

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Fügen `Startup.ConfigureServices`Sie in Dienste für Folgendes hinzu:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Erstellen Sie in der Datei " *_Host. cshtml* " und `InitialApplicationState` die Instanz von, und übergeben Sie Sie als Parameter an die APP:

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

Lösen Sie `App` in der Komponente (*app. Razor*) den Dienst auf, und initialisieren Sie ihn mit den Daten aus dem Parameter:

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

Fügen Sie im Dienst, der eine sichere API-Anforderung sendet, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:

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
