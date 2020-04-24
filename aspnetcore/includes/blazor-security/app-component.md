<span data-ttu-id="4b2a0-101">Die `App` Komponente (*app. Razor*) ähnelt der in den `App` blazor-Server-apps gefundenen Komponente:</span><span class="sxs-lookup"><span data-stu-id="4b2a0-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="4b2a0-102">Die `CascadingAuthenticationState` -Komponente verwaltet das `AuthenticationState` verfügbar machen von für den Rest der app.</span><span class="sxs-lookup"><span data-stu-id="4b2a0-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="4b2a0-103">Die `AuthorizeRouteView` Komponente stellt sicher, dass der aktuelle Benutzer für den Zugriff auf eine bestimmte Seite autorisiert ist oder `RedirectToLogin` die Komponente anderweitig rendert.</span><span class="sxs-lookup"><span data-stu-id="4b2a0-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="4b2a0-104">Die `RedirectToLogin` Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="4b2a0-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
