Die `FetchData` -Komponente zeigt Folgendes:

* Stellen Sie ein Zugriffs Token bereit.
* Verwenden Sie das Zugriffs Token, um eine geschützte Ressourcen-API in der *Server* -App aufzurufen.

Die `@attribute [Authorize]` -Direktive gibt dem blazor Webassembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente zu besuchen. Das vorhanden sein des-Attributs in der *Client* -App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmelde Informationen aufgerufen wird. Die *Server* -app muss auch `[Authorize]` auf den geeigneten Endpunkten verwenden, um Sie ordnungsgemäß zu schützen.

`AuthenticationService.RequestAccessToken();`übernimmt die Anforderung eines Zugriffs Tokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann. Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffs Token ohne Benutzerinteraktion bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt. Andernfalls schlägt die Tokenanforderung fehl.

Um das tatsächliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die APP überprüfen, ob die Anforderung `tokenResult.TryGetToken(out var token)`erfolgreich war, indem aufgerufen wird. 

Wenn die Anforderung erfolgreich ausgeführt wurde, wird die tokenvariable mit dem Zugriffs Token aufgefüllt. Die `Value` -Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar `Authorization` , die im Anforderungs Header enthalten sein soll.

Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das tokenergebnis eine Umleitungs-URL. Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite.

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
