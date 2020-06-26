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
ms.openlocfilehash: 13007df4ddddd31dd0508e9526775a6d33e0fd97
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242913"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Anfügen von Token an ausgehende Anforderungen

Der Dienst <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mit <xref:System.Net.Http.HttpClient> verwendet werden, um Zugriffstoken an ausgehende Anforderungen anzufügen. Token werden mithilfe des vorhandenen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>-Dienstes abgerufen. Wenn ein Token nicht abgerufen werden kann, wird eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ausgelöst. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> verfügt über eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>-Methode, die verwendet werden kann, um den Benutzer zum Abrufen eines neuen Token zum Identitätsanbieter zu navigieren. Der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mithilfe der Methode <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> mit den autorisierten URLs, Bereichen und der Rückgabe-URL konfiguriert werden.

Verwenden Sie einen der folgenden Ansätze, um einen Meldungshandler für ausgehende Anforderungen zu konfigurieren:

* [Benutzerdefinierte `AuthorizationMessageHandler`-Klasse](#custom-authorizationmessagehandler-class) (*Empfohlen*)
* [Konfigurieren von `AuthorizationMessageHandler`](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a>Die benutzerdefinierte Klasse „AuthorizationMessageHandler“

Im folgenden Beispiel wird die benutzerdefinierte Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> erweitert, die zum Konfigurieren von <xref:System.Net.Http.HttpClient> verwendet werden kann:

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

In `Program.Main` (`Program.cs`) wird ein <xref:System.Net.Http.HttpClient> mit dem benutzerdefinierten Handler für Autorisierungsmeldungen konfiguriert:

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen. Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket) erstellt wird, werden dem <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an die Server-API gestellt werden:

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

### <a name="configure-authorizationmessagehandler"></a>Konfigurieren des AuthorizationMessageHandler

Im folgenden Beispiel konfiguriert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> einen <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):

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

Der Einfachheit halber ist ein <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> enthalten, der mit der Basisadresse der App als autorisierte URL vorkonfiguriert ist. Die Blazor WebAssembly-Vorlagen mit aktivierter Authentifizierung verwenden jetzt die <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket) im Server-API-Projekt, um einen <xref:System.Net.Http.HttpClient> mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> einzurichten:

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

Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> im obenstehenden Beispiel erstellt wird, werden dem <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an das Serverprojekt gestellt werden.

Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen:

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

## <a name="typed-httpclient"></a>Typisierte HttpClient-Instanz

Es kann ein typisierter Client definiert werden, der alle Probleme mit dem HTTP- und Tokenabruf innerhalb einer einzelnen Klasse bearbeitet.

`WeatherForecastClient.cs`:

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

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `using static BlazorSample.Data;`).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData`-Komponente (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Konfigurieren des HttpClient-Handlers

Der Handler kann mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> für ausgehende HTTP-Anforderungen weiter konfiguriert werden.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient

Wenn die Blazor WebAssembly-App normalerweise einen sicheren Standard-<xref:System.Net.Http.HttpClient> verwendet, kann die App auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen stellen, indem sie einen benannten <xref:System.Net.Http.HttpClient> konfiguriert:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Die obenstehende Registrierung erfolgt zusätzlich zur bereits vorhandenen sicheren Standardregistrierung des <xref:System.Net.Http.HttpClient>.

Eine Komponente erstellt den <xref:System.Net.Http.HttpClient> aus der <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)-Paket), um nicht authentifizierte oder nicht autorisierte Anforderungen zu stellen:

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
> Der Controller in der Server-API (`WeatherForecastNoAuthenticationController`) für das obenstehende Beispiel ist nicht mit dem Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) gekennzeichnet.

## <a name="request-additional-access-tokens"></a>Anfordern zusätzlicher Zugriffstoken

Zugriffstoken können mithilfe von `IAccessTokenProvider.RequestAccessToken` manuell abgerufen werden.

Im folgenden Beispiel werden zusätzliche Microsoft Graph-API-Bereiche für Azure Active Directory (ADD) von einer App benötigt, damit diese Benutzerdaten lesen und E-Mails senden kann. Nachdem Sie die Berechtigungen für die Microsoft Graph-API im AAD-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.

`Program.Main` (`Program.cs`):

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

Die Methode `IAccessTokenProvider.RequestToken` stellt eine Überladung bereit, mit der Apps Zugriffstoken mit vorgegebenen Bereichen bereitstellen kann.

In einer Razor-Komponente:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> gibt Folgendes zurück:

* `true` mit dem `token` zur Verwendung
* `false`, wenn das Token nicht abgerufen wird

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen

Wenn Sie WebAssembly in einer Blazor WebAssembly-App ausführen, können der [`HttpClient`](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> zum Anpassen von Anforderungen verwendet werden. Beispielsweise können Sie die HTTP-Methode und die Anforderungsheader angeben. Die folgende Komponente sendet eine `POST`-Anforderung an einen To-Do-List-API-Endpunkt auf dem Server und zeigt den Antworttext an:

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

Die .NET-WebAssembly-Implementierung von <xref:System.Net.Http.HttpClient> verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

HTTP FETCH-Anforderungsoptionen können mit <xref:System.Net.Http.HttpRequestMessage>-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.

| Erweiterungsmethode | FETCH-Anforderungseigenschaft |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Sie können weitere Optionen mithilfe der allgemeineren <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>-Erweiterungsmethode festlegen.
 
Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren. Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A>-Erweiterungsmethode für die Anforderung.

Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Erweiterungsmethode:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.

Die folgende Richtlinie enthält die Konfiguration für:

* Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).
* Any-Methode (Verb).
* `Content-Type`- und `Authorization`-Header. Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.
* Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (`Components/HTTPRequestTester.razor`).

## <a name="handle-token-request-errors"></a>Behandeln von Fehlern mit Tokenanforderungen

Wenn eine Single-Page-Anwendung (Single Page Application, SPA) einen Benutzer mithilfe von Open ID Connect (OICD) authentifiziert, wird der Authentifizierungsstatus lokal innerhalb der SPA und im Identity-Anbieter (Identity Provider, IP) in Form eines Sitzungscookies verwaltet, das festgelegt wird, wenn der Benutzer seine Anmeldeinformationen bereitstellt.

Die Token, die der IP für den Benutzer ausgibt, sind in der Regel nur für kurze Zeit (etwa eine Stunde) gültig, sodass die Client-App regelmäßig neue Token abrufen muss. Andernfalls wird der Benutzer nach Ablauf der ausgegebenen Token abgemeldet. In den meisten Fällen sind die OICD-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer noch mal authentifizieren muss, weil ein Authentifizierungsstatus oder eine Sitzung im IP gespeichert ist.

Manchmal kann der Client ohne Benutzerinteraktion keine Token abrufen. Dies ist zum Beispiel dann der Fall, wenn sich der Benutzer aus einem beliebigen Grund explizit vom IP abmeldet. Dies passiert, wenn der Benutzer `https://login.microsoftonline.com` aufruft und sich abmeldet. In diesen Szenarios weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Alle Token, die im Client enthalten sind, sind dann möglicherweise nicht mehr gültig. Außerdem kann der Client keine neuen Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.

Diese Szenarios sind nicht spezifisch für die tokenbasierte Authentifizierung. Sie gehören bei SPAs dazu. Wenn eine SPA, die Cookies verwendet, eine Server-API aufruft, wird ein Fehler ausgelöst, wenn das Authentifizierungscookie entfernt wird.

Wenn eine App API-Aufrufe für geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:

* Damit ein neues Zugriffstoken zum Aufrufen der API bereitgestellt werden kann, muss der Benutzer sich möglicherweise noch einmal authentifizieren.
* Auch wenn der Client über ein Token verfügt, das anscheinend gültig ist, kann der Serveraufruf fehlschlagen, weil das Token vom Benutzer widerrufen wurde.

Wenn die App ein Token anfordert, gibt es zwei Möglichkeiten:

* Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.
* Die Anforderung schlägt fehl, und die App muss den Benutzer noch einmal authentifizieren, um ein neues Token zu erhalten.

Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen. Es gibt mehrere Ansätze, die unterschiedlich komplex sind:

* Speichern Sie den aktuellen Seitenzustand im Sitzungsspeicher. Überprüfen Sie während des [`OnInitializedAsync`-Lebenszyklusereignisses ](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), ob der Zustand wiederhergestellt werden kann, bevor Sie fortfahren.
* Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand aktualisieren muss.
* Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.

Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Speichern des Zustands vor der Umleitung auf die Anmeldeseite
* Wiederherstellen des vorherigen Zustands nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters

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

## <a name="save-app-state-before-an-authentication-operation"></a>Speichern des App-Zustands vor einem Authentifizierungsvorgang

Während eines Authentifizierungsvorgangs sollten Sie ggf. den App-Zustand speichern, bevor der Browser an den IP umgeleitet wird. Dies bietet sich zum Beispiel dann an, wenn Sie einen Zustandscontainer verwenden und den Zustand nach erfolgreicher Authentifizierung wiederherstellen möchten. Sie können ein benutzerdefiniertes Zustandsobjekt für die Authentifizierung verwenden, um den App-spezifischen Zustand oder einen Verweis auf diesen zu speichern und diesen Zustand nach erfolgreichem Abschluss des Authentifizierungsvorgangs wiederherzustellen. Im folgenden Beispiel wird dieser Ansatz veranschaulicht.

In der App wird eine Zustandscontainerklasse mit Eigenschaften erstellt, die die Zustandswerte der App enthalten. Im folgenden Beispiel wird der Container verwendet, um den Leistungsindikatorwert der Komponente `Counter` der Standardvorlage (`Pages/Counter.razor`) beizubehalten. Methoden zum (De-)Serialisieren des Containers basierend auf <xref:System.Text.Json>

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

Die Komponente `Counter` verwendet den Zustandscontainer, um den Wert `currentCount` außerhalb der Komponente beizubehalten:

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

Erstellen Sie einen `ApplicationAuthenticationState` aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>. Stellen Sie eine `Id`-Eigenschaft bereit, die als Bezeichner für den lokal gespeicherten Zustand fungiert.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

Die Komponente `Authentication` (`Pages/Authentication.razor`) speichert den Zustand der App mithilfe des lokalen Sitzungsspeichers über die `StateContainer`-(De-)Serialisierungsmethoden `GetStateForLocalStorage` und `SetStateFromLocalStorage` und stellt diesen auch darüber wieder her:

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

In diesem Beispiel wird AAD für die Authentifizierung verwendet. In `Program.Main` (`Program.cs`):

* Der `ApplicationAuthenticationState` wird als `RemoteAuthenticationState`-Typ der Microsoft-Authentifizierungsbibliothek konfiguriert.
* Der Zustandscontainer wird im Dienstcontainer registriert.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Anpassen von App-Routen

Standardmäßig verwendet die Bibliothek [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) die Routen, die in der folgenden Tabelle angezeigt werden, um unterschiedliche Authentifizierungszustände darzustellen.

| Route                            | Zweck |
| -------------------------------- | ------- |
| `authentication/login`           | Löst einen Anmeldevorgang aus |
| `authentication/login-callback`  | Verarbeitet das Ergebnis eines beliebigen Anmeldevorgangs. |
| `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus einem beliebigen Grund fehlschlägt |
| `authentication/logout`          | Löst einen Abmeldevorgang aus |
| `authentication/logout-callback` | Verarbeitet das Ergebnis eines beliebigen Abmeldevorgangs |
| `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus einem beliebigen Grund fehlschlägt |
| `authentication/logged-out`      | Gibt an, dass ein Benutzer sich erfolgreich abgemeldet hat |
| `authentication/profile`         | Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten |
| `authentication/register`        | Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren |

Die in der obigen Tabelle aufgeführten Routen können über <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> konfiguriert werden. Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die App über eine Route verfügt, die die einzelnen Pfade verarbeitet.

Im folgenden Beispiel haben alle Pfade das Präfix `/security`.

`Authentication`-Komponente (`Pages/Authentication.razor`):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Wenn vollkommen unterschiedliche Pfade verlangt werden, legen Sie die Routen wie zuvor beschrieben fest, und rendern Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Wenn Sie sich hierfür entscheiden, können Sie die Benutzeroberfläche auf mehrere Seiten aufteilen.

## <a name="customize-the-authentication-user-interface"></a>Anpassen der Benutzeroberfläche für die Authentifizierung

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> enthält Standardbenutzeroberflächenelemente für alle Authentifizierungszustände. Jeder Zustand kann angepasst werden, indem ein benutzerdefiniertes <xref:Microsoft.AspNetCore.Components.RenderFragment>übergeben wird. Wenn Sie den angezeigten Text während des ersten Anmeldeprozesses anpassen möchten, können Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.

`Authentication`-Komponente (`Pages/Authentication.razor`):

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

Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das für die Authentifizierungsrouten in der folgenden Tabelle verwendet werden kann.

| Route                            | Fragment                |
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

## <a name="customize-the-user"></a>Anpassen des Benutzers

Benutzer, die an die App gebunden sind, können angepasst werden. Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr`-Anspruch für jede Authentifizierungsmethode des Benutzers.

Erstellen Sie eine Klasse, die die Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Erstellen Sie eine Factory, die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> erweitert:

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

Registrieren Sie die `CustomAccountFactory` für den verwendeten Authentifizierungsanbieter. Sie können die folgenden Registrierungen verwenden: 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

## <a name="support-prerendering-with-authentication"></a>Unterstützen des Vorabrenderings mit Authentifizierung

Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:

* Pfade vorab rendert, für die keine Autorisierung erforderlich ist.
* Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.

Schreiben Sie in der `Program`-Klasse der Client-App (`Program.cs`) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):

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

Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:

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

Ersetzen Sie in der Methode `Startup.Configure` der Server-App [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) durch [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Erstellen Sie in der Server-App einen Ordner `Pages`, falls dieser nicht vorhanden ist. Erstellen Sie in der Server-App innerhalb des Ordners `Pages` die Seite `_Host.cshtml`. Fügen Sie den Inhalt der Datei `wwwroot/index.html` der Client-App in die Datei `Pages/_Host.cshtml` ein. Aktualisieren Sie den Inhalt der Datei:

* Fügen Sie `@page "_Host"` am Anfang der Datei ein.
* Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Optionen für gehostete Apps und dritte Anmeldeanbieter

Wenn eine gehostete Blazor-WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung. Welche Sie auswählen, hängt von Ihrem Szenario ab.

Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs

So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Szenario:

* Der Server, der die App hostet, spielt keine Rolle.
* APIs auf dem Server können nicht geschützt werden.
* Die App kann nur geschützte APIs von Drittanbietern aufrufen.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter

Konfigurieren Sie Identity mit einem dritten Anmeldeanbieter. Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.

Wenn sich ein Benutzer anmeldet, erfasst Identity als Teil des Authentifizierungsprozesses die Token für Zugriff und Aktualisierung. An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters

Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen. Verwenden Sie dann das Zugriffstoken des Drittanbieters, um API-Ressourcen von Drittanbietern direkt über Identity auf dem Client abzurufen.

Dieser Ansatz wird nicht empfohlen. Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden. In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden. Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.

* Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.
* Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs

Führen Sie einen API-Aufruf auf dem Client für die Server-API durch. Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.

Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:

* Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.
* Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Verwenden von OpenID Connect 2.0-Endpunkten (OIDC)

Die Authentifizierungsbibliothek sowie Blazor-Vorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC). Wenn Sie einen Endpunkt der Version 2.0 verwenden möchten, konfigurieren Sie die JWT-Bearer-Option <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType>. Im folgenden Beispiel wird AAD für Version 2.0 konfiguriert, indem ein `v2.0`-Segment an die Eigenschaft <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> angehängt wird:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest. Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der Datei mit den App-Einstellungen (`appsettings.json`) mit dem `Authority`-Schlüssel fest.

Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0. Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).
