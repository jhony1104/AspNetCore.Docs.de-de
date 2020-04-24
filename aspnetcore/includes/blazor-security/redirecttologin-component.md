Die `RedirectToLogin` Komponente (*Shared/redirecttologin. Razor*):

* Verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.
* Behält die aktuelle URL bei, auf die der Benutzer zugreifen möchte, damit Sie an diese Seite zurückgegeben werden können, wenn die Authentifizierung erfolgreich ist.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
