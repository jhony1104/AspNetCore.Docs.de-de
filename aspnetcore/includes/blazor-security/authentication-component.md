Die von der `Authentication` Komponente (*pages/Authentication. Razor*) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungs Stufen erforderlich sind.

Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Komponente:

* Wird vom [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellt.
* Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
