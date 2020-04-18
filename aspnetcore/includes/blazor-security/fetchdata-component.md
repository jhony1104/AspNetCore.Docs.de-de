<span data-ttu-id="388e5-101">Die `FetchData` Komponente zeigt, wie:</span><span class="sxs-lookup"><span data-stu-id="388e5-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="388e5-102">Stellen Sie ein Zugriffstoken zur Zeit ein.</span><span class="sxs-lookup"><span data-stu-id="388e5-102">Provision an access token.</span></span>
* <span data-ttu-id="388e5-103">Verwenden Sie das Zugriffstoken, um eine geschützte Ressourcen-API in der *Server-App* aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="388e5-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="388e5-104">Die `@attribute [Authorize]` Direktive weist das Blazor WebAssembly-Autorisierungssystem darauf hin, dass der Benutzer autorisiert werden muss, um diese Komponente besuchen zu können.</span><span class="sxs-lookup"><span data-stu-id="388e5-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="388e5-105">Das Vorhandensein des Attributs in der *Client-App* verhindert nicht, dass die API auf dem Server ohne die richtigen Anmeldeinformationen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="388e5-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="388e5-106">Die *Server-App* `[Authorize]` muss auch auf den entsprechenden Endpunkten verwenden, um sie korrekt zu schützen.</span><span class="sxs-lookup"><span data-stu-id="388e5-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="388e5-107">`AuthenticationService.RequestAccessToken();`kümmert sich um das Anfordern eines Zugriffstokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="388e5-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="388e5-108">Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffstoken ohne Benutzerinteraktion bereitstellen kann, ist die Tokenanforderung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="388e5-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="388e5-109">Andernfalls schlägt die Tokenanforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="388e5-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="388e5-110">Um das tatsächliche Token zu erhalten, das in die Anforderung aufgenommen `tokenResult.TryGetToken(out var token)`werden soll, muss die App überprüfen, ob die Anforderung durch Aufrufen erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="388e5-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="388e5-111">Wenn die Anforderung erfolgreich war, wird die Tokenvariable mit dem Zugriffstoken aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="388e5-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="388e5-112">Die `Value` Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die in den `Authorization` Anforderungsheader eingeschlossen werden soll.</span><span class="sxs-lookup"><span data-stu-id="388e5-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="388e5-113">Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das Tokenergebnis eine Umleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="388e5-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="388e5-114">Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="388e5-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="388e5-115">Weitere Informationen finden Sie unter Speichern des [App-Status vor einem Authentifizierungsvorgang](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="388e5-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
