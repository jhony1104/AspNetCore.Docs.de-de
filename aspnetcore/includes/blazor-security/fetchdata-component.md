<span data-ttu-id="bcb3d-101">Die `FetchData`-Komponente zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bcb3d-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="bcb3d-102">Stellen Sie ein Zugriffs Token bereit.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-102">Provision an access token.</span></span>
* <span data-ttu-id="bcb3d-103">Verwenden Sie das Zugriffs Token, um eine geschützte Ressourcen-API in der *Server* -App aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="bcb3d-104">Die `@attribute [Authorize]`-Direktive gibt dem blazor Webassembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente zu besuchen.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="bcb3d-105">Das vorhanden sein des-Attributs in der *Client* -App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmelde Informationen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="bcb3d-106">Die *Server* -app muss auch `[Authorize]` auf den entsprechenden Endpunkten verwenden, um Sie ordnungsgemäß zu schützen.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="bcb3d-107">`AuthenticationService.RequestAccessToken();` übernimmt die Anforderung eines Zugriffs Tokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="bcb3d-108">Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffs Token ohne Benutzerinteraktion bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="bcb3d-109">Andernfalls schlägt die Tokenanforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="bcb3d-110">Um das tatsächliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die APP überprüfen, ob die Anforderung erfolgreich war, indem `tokenResult.TryGetToken(out var token)`aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="bcb3d-111">Wenn die Anforderung erfolgreich ausgeführt wurde, wird die tokenvariable mit dem Zugriffs Token aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="bcb3d-112">Die `Value`-Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die im `Authorization`-Anforderungs Header enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="bcb3d-113">Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das tokenergebnis eine Umleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="bcb3d-114">Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite.</span><span class="sxs-lookup"><span data-stu-id="bcb3d-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
...
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

<span data-ttu-id="bcb3d-115">Weitere Informationen finden Sie unter [Speichern des App-Zustands vor einem Authentifizierungs Vorgang](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="bcb3d-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
