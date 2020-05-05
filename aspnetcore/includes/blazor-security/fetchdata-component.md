<span data-ttu-id="ff232-101">Die `FetchData` -Komponente zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ff232-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="ff232-102">Stellen Sie ein Zugriffs Token bereit.</span><span class="sxs-lookup"><span data-stu-id="ff232-102">Provision an access token.</span></span>
* <span data-ttu-id="ff232-103">Verwenden Sie das Zugriffs Token, um eine geschützte Ressourcen-API in der *Server* -App aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ff232-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="ff232-104">Die `@attribute [Authorize]` -Direktive gibt dem blazor Webassembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente zu besuchen.</span><span class="sxs-lookup"><span data-stu-id="ff232-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="ff232-105">Das vorhanden sein des-Attributs in der *Client* -App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmelde Informationen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff232-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="ff232-106">Die *Server* -app muss auch `[Authorize]` auf den geeigneten Endpunkten verwenden, um Sie ordnungsgemäß zu schützen.</span><span class="sxs-lookup"><span data-stu-id="ff232-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="ff232-107">`IAccessTokenProvider.RequestAccessToken();`übernimmt die Anforderung eines Zugriffs Tokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ff232-107">`IAccessTokenProvider.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="ff232-108">Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffs Token ohne Benutzerinteraktion bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ff232-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="ff232-109">Andernfalls schlägt die Tokenanforderung mit einem `AccessTokenNotAvailableException` Fehler fehl, der in einer `try-catch` -Anweisung abgefangen wird.</span><span class="sxs-lookup"><span data-stu-id="ff232-109">Otherwise, the token request fails with an `AccessTokenNotAvailableException` error, which is caught in a `try-catch` statement.</span></span>

<span data-ttu-id="ff232-110">Um das tatsächliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die APP überprüfen, ob die Anforderung `tokenResult.TryGetToken(out var token)`erfolgreich war, indem aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff232-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="ff232-111">Wenn die Anforderung erfolgreich ausgeführt wurde, wird die tokenvariable mit dem Zugriffs Token aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="ff232-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="ff232-112">Die `Value` -Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar `Authorization` , die im Anforderungs Header enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="ff232-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="ff232-113">Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das tokenergebnis eine Umleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="ff232-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="ff232-114">Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite.</span><span class="sxs-lookup"><span data-stu-id="ff232-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
