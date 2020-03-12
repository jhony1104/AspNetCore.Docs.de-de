<span data-ttu-id="dbfd3-101">Die `App` Komponente (*app. Razor*) ähnelt der `App` Komponente in den blazor-Server-apps:</span><span class="sxs-lookup"><span data-stu-id="dbfd3-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="dbfd3-102">Die `CascadingAuthenticationState` Komponente verwaltet das verfügbar machen der `AuthenticationState` für den Rest der app.</span><span class="sxs-lookup"><span data-stu-id="dbfd3-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="dbfd3-103">Die `AuthorizeRouteView` Komponente stellt sicher, dass der aktuelle Benutzer für den Zugriff auf eine bestimmte Seite autorisiert ist oder die `RedirectToLogin` Komponente anderweitig rendert.</span><span class="sxs-lookup"><span data-stu-id="dbfd3-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="dbfd3-104">Die `RedirectToLogin` Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="dbfd3-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
