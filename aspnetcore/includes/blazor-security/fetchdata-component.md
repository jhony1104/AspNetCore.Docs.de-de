Die `FetchData` Komponente zeigt, wie:

* Stellen Sie ein Zugriffstoken zur Zeit ein.
* Verwenden Sie das Zugriffstoken, um eine geschützte Ressourcen-API in der *Server-App* aufzurufen.

Die `@attribute [Authorize]` Direktive weist das Blazor WebAssembly-Autorisierungssystem darauf hin, dass der Benutzer autorisiert werden muss, um diese Komponente besuchen zu können. Das Vorhandensein des Attributs in der *Client-App* verhindert nicht, dass die API auf dem Server ohne die richtigen Anmeldeinformationen aufgerufen wird. Die *Server-App* `[Authorize]` muss auch auf den entsprechenden Endpunkten verwenden, um sie korrekt zu schützen.

`AuthenticationService.RequestAccessToken();`kümmert sich um das Anfordern eines Zugriffstokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann. Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffstoken ohne Benutzerinteraktion bereitstellen kann, ist die Tokenanforderung erfolgreich. Andernfalls schlägt die Tokenanforderung fehl.

Um das tatsächliche Token zu erhalten, das in die Anforderung aufgenommen `tokenResult.TryGetToken(out var token)`werden soll, muss die App überprüfen, ob die Anforderung durch Aufrufen erfolgreich war. 

Wenn die Anforderung erfolgreich war, wird die Tokenvariable mit dem Zugriffstoken aufgefüllt. Die `Value` Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die in den `Authorization` Anforderungsheader eingeschlossen werden soll.

Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das Tokenergebnis eine Umleitungs-URL. Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite weitergeleitet.

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

Weitere Informationen finden Sie unter Speichern des [App-Status vor einem Authentifizierungsvorgang](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
