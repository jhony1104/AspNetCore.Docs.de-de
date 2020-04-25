<span data-ttu-id="24ec9-101">Die von der `Authentication` Komponente (*pages/Authentication. Razor*) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungs Stufen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="24ec9-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="24ec9-102">Die `RemoteAuthenticatorView` Komponente:</span><span class="sxs-lookup"><span data-stu-id="24ec9-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="24ec9-103">Wird vom [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="24ec9-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="24ec9-104">Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="24ec9-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
