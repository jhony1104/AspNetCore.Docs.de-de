Die `App` Komponente (*app. Razor*) ähnelt der in den `App` blazor-Server-apps gefundenen Komponente:

* Die- <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> Komponente verwaltet das verfügbar machen <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> von für den Rest der app.
* Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> Komponente stellt sicher, dass der aktuelle Benutzer für den Zugriff auf eine bestimmte Seite autorisiert ist oder die Komponente anderweitig rendert `RedirectToLogin` .
* Die `RedirectToLogin` Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
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
