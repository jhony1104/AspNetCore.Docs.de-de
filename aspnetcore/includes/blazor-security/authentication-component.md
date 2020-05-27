<span data-ttu-id="ac59c-101">Die von der `Authentication` Komponente (*pages/Authentication. Razor*) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungs Stufen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="ac59c-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="ac59c-102">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Komponente:</span><span class="sxs-lookup"><span data-stu-id="ac59c-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="ac59c-103">Wird vom [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ac59c-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="ac59c-104">Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="ac59c-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
