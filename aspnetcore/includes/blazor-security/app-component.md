Die `App` Komponente (*app. Razor*) ähnelt der `App` Komponente in den blazor-Server-apps:

* Die `CascadingAuthenticationState` Komponente verwaltet das verfügbar machen der `AuthenticationState` für den Rest der app.
* Die `AuthorizeRouteView` Komponente stellt sicher, dass der aktuelle Benutzer für den Zugriff auf eine bestimmte Seite autorisiert ist oder die `RedirectToLogin` Komponente anderweitig rendert.
* Die `RedirectToLogin` Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
