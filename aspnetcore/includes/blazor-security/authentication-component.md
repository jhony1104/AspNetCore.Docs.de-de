<span data-ttu-id="efa62-101">Die von der `Authentication` Komponente (*pages/Authentication. Razor*) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungs Stufen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="efa62-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="efa62-102">Die `RemoteAuthenticatorView` Komponente:</span><span class="sxs-lookup"><span data-stu-id="efa62-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="efa62-103">Wird vom `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="efa62-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="efa62-104">Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="efa62-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
