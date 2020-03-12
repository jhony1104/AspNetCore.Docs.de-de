Die `FetchData`-Komponente zeigt Folgendes:

* Stellen Sie ein Zugriffs Token bereit.
* Verwenden Sie das Zugriffs Token, um eine geschützte Ressourcen-API in der *Server* -App aufzurufen.

Die `@attribute [Authorize]`-Direktive gibt dem blazor Webassembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente zu besuchen. Das vorhanden sein des-Attributs in der *Client* -App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmelde Informationen aufgerufen wird. Die *Server* -app muss auch `[Authorize]` auf den entsprechenden Endpunkten verwenden, um Sie ordnungsgemäß zu schützen.

`AuthenticationService.RequestAccessToken();` übernimmt die Anforderung eines Zugriffs Tokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann. Wenn das Token zwischengespeichert wird oder der Dienst ein neues Zugriffs Token ohne Benutzerinteraktion bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt. Andernfalls schlägt die Tokenanforderung fehl.

Um das tatsächliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die APP überprüfen, ob die Anforderung erfolgreich war, indem `tokenResult.TryGetToken(out var token)`aufgerufen wird. 

Wenn die Anforderung erfolgreich ausgeführt wurde, wird die tokenvariable mit dem Zugriffs Token aufgefüllt. Die `Value`-Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die im `Authorization`-Anforderungs Header enthalten sein soll.

Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das tokenergebnis eine Umleitungs-URL. Wenn Sie zu dieser URL navigieren, wird der Benutzer nach einer erfolgreichen Authentifizierung zur Anmeldeseite und zurück zur aktuellen Seite.

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

Weitere Informationen finden Sie unter [Speichern des App-Zustands vor einem Authentifizierungs Vorgang](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
