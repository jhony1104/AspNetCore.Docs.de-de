<span data-ttu-id="d5117-101">Die `RedirectToLogin` Komponente (*Shared/redirecttologin. Razor*):</span><span class="sxs-lookup"><span data-stu-id="d5117-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="d5117-102">Verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="d5117-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="d5117-103">Behält die aktuelle URL bei, auf die der Benutzer zugreifen möchte, damit Sie an diese Seite zurückgegeben werden können, wenn die Authentifizierung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="d5117-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
