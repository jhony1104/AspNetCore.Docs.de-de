---
title: BlazorZusätzliche Sicherheitsszenarien für ASP.net Core Webassembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor Webassembly für zusätzliche Sicherheitsszenarien konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153478"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.net Core blazor Webassembly zusätzliche Sicherheitsszenarien

Von [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Anfügen von Token an ausgehende Anforderungen

Der `AuthorizationMessageHandler` Dienst kann mit verwendet werden `HttpClient` , um Zugriffs Token an ausgehende Anforderungen anzufügen. Token werden mithilfe des vorhandenen `IAccessTokenProvider` Dienstanbieter abgerufen. Wenn ein Token nicht abgerufen werden kann, `AccessTokenNotAvailableException` wird eine ausgelöst. `AccessTokenNotAvailableException`verfügt über eine- `Redirect` Methode, die verwendet werden kann, um den Benutzer zum Identitäts Anbieter zu navigieren, um ein neues Token abzurufen. `AuthorizationMessageHandler`Kann mithilfe der-Methode mit den autorisierten URLs, Bereichen und Rückgabe-URL konfiguriert werden `ConfigureHandler` .

Im folgenden Beispiel wird `AuthorizationMessageHandler` ein `HttpClient` in `Program.Main` (*Program.cs*) konfiguriert:

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

Zur einfacheren Verwendung `BaseAddressAuthorizationMessageHandler` ist ein enthalten, das mit der APP-Basisadresse als autorisierte URL vorkonfiguriert ist. Die Authentifizierungs fähigen blazor-webassemblyvorlagen verwenden nun <xref:System.Net.Http.IHttpClientFactory> im Server-API-Projekt, um eine <xref:System.Net.Http.HttpClient> mit dem einzurichten `BaseAddressAuthorizationMessageHandler` :

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

Wenn der Client `CreateClient` im vorherigen Beispiel mit erstellt wird, werden die- <xref:System.Net.Http.HttpClient> Instanzen bereitgestellt, die Zugriffs Token enthalten, wenn Anforderungen an das Server Projekt gestellt werden.

Der konfigurierte <xref:System.Net.Http.HttpClient> wird dann verwendet, um autorisierte Anforderungen mit einem einfachen Muster zu erstellen `try-catch` .

`FetchData`Komponente (*pages/fetchdata. Razor*):

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

## <a name="typed-httpclient"></a>Typisierter httpclient

Ein typisierter Client kann definiert werden, der alle Probleme mit der HTTP-und tokenübernahme innerhalb einer einzelnen Klasse behandelt.

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData`Komponente (*pages/fetchdata. Razor*):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Konfigurieren des httpclient-Handlers

Der Handler kann für ausgehende HTTP-Anforderungen weiter konfiguriert werden <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client

Wenn die blazor Webassembly-App normalerweise einen sicheren Standardwert verwendet <xref:System.Net.Http.HttpClient> , kann die APP auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen erstellen, indem Sie einen mit dem Namen konfiguriert <xref:System.Net.Http.HttpClient> :

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Die vorherige Registrierung ist zusätzlich zur vorhandenen sicheren Standard <xref:System.Net.Http.HttpClient> Registrierung.

Eine-Komponente erstellt die <xref:System.Net.Http.HttpClient> aus dem <xref:System.Net.Http.IHttpClientFactory> , um nicht authentifizierte oder nicht autorisierte Anforderungen zu erstellen:

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
> Der Controller in der Server-API `WeatherForecastNoAuthenticationController` für das vorherige Beispiel ist nicht mit dem- [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Attribut gekennzeichnet.

## <a name="request-additional-access-tokens"></a>Anfordern zusätzlicher Zugriffstoken

Zugriffs Token können manuell abgerufen werden, indem aufgerufen wird `IAccessTokenProvider.RequestAccessToken` .

Im folgenden Beispiel werden zusätzliche Azure Active Directory-Microsoft Graph-API-Bereiche (AAD) für eine APP benötigt, um Benutzerdaten zu lesen und e-Mails zu senden. Nachdem Sie die Berechtigungen für die Microsoft Graph-API im Azure Aad-Portal hinzugefügt haben, werden die zusätzlichen Bereiche in der Client-App konfiguriert.

`Program.Main`(*Program.cs*):

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

Die- `IAccessTokenProvider.RequestToken` Methode stellt eine Überladung bereit, die es einer App ermöglicht, ein Zugriffs Token mit einem bestimmten Satz von Bereichen bereitzustellen.

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

`TryGetToken`Renditen

* `true`mit der `token` zu verwendenden.
* `false`, wenn das Token nicht abgerufen wird.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen

Beim Ausführen von webassemblys in einer blazor Webassembly-App können Sie [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> zum Anpassen von Anforderungen verwenden. Beispielsweise können Sie die HTTP-Methode und die Anforderungs Header angeben. Die folgende Komponente sendet eine `POST` Anforderung an einen API-Endpunkt der Aufgabenliste auf dem Server und zeigt den Antworttext an:

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

Die .NET-WebAssembly-Implementierung von `HttpClient` verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

HTTP FETCH-Anforderungsoptionen können mit `HttpRequestMessage`-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.

| `HttpRequestMessage`-Erweiterungsmethode | FETCH-Anforderungseigenschaft |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Sie können weitere Optionen mithilfe der allgemeineren `SetBrowserRequestOption`-Erweiterungsmethode festlegen.
 
Die HTTP-Antwort wird in der Regel in einer blazor Webassembly-App gepuffert, um die Unterstützung für Synchronisierungs Lesevorgänge im Antwort Inhalt zu aktivieren Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die `SetBrowserResponseStreamingEnabled`-Erweiterungsmethode für die Anforderung.

Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die `SetBrowserRequestCredentials`-Erweiterungsmethode:

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

Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).

## <a name="handle-token-request-errors"></a>Behandeln von tokenanforderungs-Fehlern

Wenn eine Single-Page-Anwendung (Single-Page Application, Spa) einen Benutzer mithilfe von Open ID Connect (oidc) authentifiziert, wird der Authentifizierungs Status lokal innerhalb der Spa und im Identitäts Anbieter (IP) in Form eines Sitzungs Cookies verwaltet, das als Ergebnis des Benutzers festgelegt wird, der seine Anmelde Informationen bereitstellt.

Die Token, die die IP-Adresse für den Benutzer ausgibt, sind in der Regel für kurze Zeiträume (etwa eine Stunde normal) gültig, sodass die Client-App regelmäßig neue Token abrufen muss. Andernfalls wird der Benutzer nach Ablauf der gewährten Token abgemeldet. In den meisten Fällen sind die oidc-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer aufgrund des Authentifizierungs Zustands oder der Sitzung innerhalb der IP-Adresse erneut authentifizieren muss.

Es gibt Fälle, in denen der Client ein Token ohne Benutzerinteraktion nicht erhalten kann, z. b. wenn sich der Benutzer aus irgendeinem Grund explizit bei der IP-Adresse anmeldet. Dieses Szenario tritt auf, wenn ein Benutzer die Benutzer besucht `https://login.microsoftonline.com` und abmeldet. In diesen Szenarien weiß die APP nicht sofort, ob sich der Benutzer abgemeldet hat. Jedes Token, das der Client enthält, ist möglicherweise nicht mehr gültig. Außerdem kann der Client kein neues Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.

Diese Szenarien sind nicht spezifisch für die tokenbasierte Authentifizierung. Sie sind Teil der Natur von Spas. Bei einer Spa mit Cookies kann auch eine Server-API nicht aufgerufen werden, wenn das Authentifizierungs Cookie entfernt wurde.

Wenn eine APP API-Aufrufe an geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:

* Um ein neues Zugriffs Token zum Aufrufen der API bereitzustellen, muss sich der Benutzer möglicherweise erneut authentifizieren.
* Auch wenn der Client über ein Token verfügt, das als gültig erscheint, schlägt der Server Fehler möglicherweise fehl, da das Token vom Benutzer widerrufen wurde.

Wenn die APP ein Token anfordert, gibt es zwei mögliche Ergebnisse:

* Die Anforderung ist erfolgreich, und die APP verfügt über ein gültiges Token.
* Die Anforderung schlägt fehl, und die APP muss den Benutzer erneut authentifizieren, um ein neues Token zu erhalten.

Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen. Es gibt mehrere Ansätze mit zunehmender Komplexität:

* Speichert den aktuellen Seiten Status im Sitzungs Speicher. `OnInitializeAsync`Überprüfen Sie während des weiteren, ob der Zustand wieder hergestellt werden kann.
* Fügen Sie einen Abfrage Zeichenfolgen-Parameter hinzu, und verwenden Sie diesen als Möglichkeit, um der APP zu signalisieren, dass Sie den zuvor gespeicherten Zustand erneut aktivieren muss.
* Fügen Sie einen Abfrage Zeichen folgen Parameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungs Speicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.

Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Status vor der Umleitung zur Anmeldeseite beibehalten.
* Stellen Sie den vorherigen Zustand nach der Authentifizierung mit dem Abfrage Zeichen folgen Parameter wieder her.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Speichern des App-Zustands vor einem Authentifizierungs Vorgang

Während eines Authentifizierungs Vorgangs sollten Sie den App-Status speichern, bevor der Browser an die IP-Adresse umgeleitet wird. Dies kann der Fall sein, wenn Sie etwas ähnliches wie einen Zustands Container verwenden und den Status nach erfolgreicher Authentifizierung wiederherstellen möchten. Sie können ein benutzerdefiniertes Authentifizierungs Zustands Objekt verwenden, um den App-spezifischen Zustand oder einen Verweis darauf beizubehalten und diesen Zustand wiederherzustellen, sobald der Authentifizierungs Vorgang erfolgreich abgeschlossen wurde.

`Authentication`Komponente (*pages/Authentication. Razor*):

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

## <a name="customize-app-routes"></a>Anpassen von App-Routen

Standardmäßig verwendet die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek die Routen, die in der folgenden Tabelle aufgeführt sind, zum Darstellen verschiedener Authentifizierungs Zustände.

| Route                            | Zweck |
| -------------------------------- | ------- |
| `authentication/login`           | Löst einen Anmeldevorgang aus. |
| `authentication/login-callback`  | Behandelt das Ergebnis eines beliebigen Anmeldevorgangs. |
| `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus irgendeinem Grund fehlschlägt. |
| `authentication/logout`          | Löst einen Abmeldevorgang aus. |
| `authentication/logout-callback` | Behandelt das Ergebnis eines Abmelde Vorgangs. |
| `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus irgendeinem Grund fehlschlägt. |
| `authentication/logged-out`      | Gibt an, dass der Benutzer sich erfolgreich abgemeldet hat. |
| `authentication/profile`         | Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten. |
| `authentication/register`        | Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren. |

Die in der vorangehenden Tabelle gezeigten Routen können über konfiguriert werden `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` . Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die APP über eine Route verfügt, die die einzelnen Pfade verarbeitet

Im folgenden Beispiel wird allen Pfaden ein Präfix vorangestellt `/security` .

`Authentication`Komponente (*pages/Authentication. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Wenn die Anforderung vollkommen unterschiedliche Pfade erfordert, legen Sie die Routen wie zuvor beschrieben fest, und erstellen Sie `RemoteAuthenticatorView` mit einem expliziten Aktionsparameter:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Wenn Sie sich dafür entscheiden, können Sie die Benutzeroberfläche in verschiedene Seiten unterbrechen.

## <a name="customize-the-authentication-user-interface"></a>Anpassen der Benutzeroberfläche für die Authentifizierung

`RemoteAuthenticatorView`enthält einen Standardsatz von Benutzeroberflächen Elementen für jeden Authentifizierungs Status. Jeder Zustand kann durch Übergeben eines benutzerdefinierten angepasst werden `RenderFragment` . Wenn Sie den angezeigten Text während des anfänglichen Anmelde Prozesses anpassen möchten, können Sie den `RemoteAuthenticatorView` wie folgt ändern.

`Authentication`Komponente (*pages/Authentication. Razor*):

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

Der `RemoteAuthenticatorView` verfügt über ein Fragment, das in der folgenden Tabelle pro Authentifizierungs Route verwendet werden kann.

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

## <a name="customize-the-user"></a>Benutzer anpassen

Benutzer, die an die APP gebunden sind, können angepasst werden. Im folgenden Beispiel erhalten alle authentifizierten Benutzer einen `amr` Anspruch für jede Authentifizierungsmethode des Benutzers.

Erstellen Sie eine Klasse, die die- `RemoteUserAccount` Klasse erweitert:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Erstellen Sie eine Factory, die Folgendes erweitert `AccountClaimsPrincipalFactory<TAccount>` :

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

Registrieren Sie `CustomAccountFactory` für den verwendeten Authentifizierungs Anbieter. Jede der folgenden Registrierungen ist gültig: 

* `AddOidcAuthentication`:

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

* `AddMsalAuthentication`:

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
  
* `AddApiAuthorization`:

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

Schreiben Sie in der `Program`-Klasse der Client-App (*Program.cs*) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):

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

Ersetzen Sie in der Methode `Startup.Configure` der Server-App `endpoints.MapFallbackToFile("index.html")` durch `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Erstellen Sie in der Server-App einen Ordner *Seiten*, falls dieser nicht vorhanden ist. Erstellen Sie in der Server-App innerhalb des Ordners *Seiten* die Seite *_Host.cshtml*. Fügen Sie den Inhalt der Datei *wwwroot/index.html* der Client-App in die Datei *Pages/_Host.cshtml* ein. Aktualisieren Sie den Inhalt der Datei:

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

Konfigurieren Sie Identity mit einem Drittanbieter-Anmelde Anbieter. Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.

Wenn sich ein Benutzer anmeldet, Identity sammelt Zugriffs-und Aktualisierungs Token im Rahmen des Authentifizierungs Vorgangs. An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters

Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen. Verwenden Sie von dort aus das Drittanbieter-Zugriffs Token, um API-Ressourcen von Drittanbietern direkt von Identity auf dem Client aus aufzurufen.

Dieser Ansatz wird nicht empfohlen. Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden. In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden. Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.

* Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.
* Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs

Führen Sie einen API-Aufruf auf dem Client für die Server-API durch. Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.

Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:

* Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.
* Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.
