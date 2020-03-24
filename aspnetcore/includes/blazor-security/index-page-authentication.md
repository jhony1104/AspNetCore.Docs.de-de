<span data-ttu-id="2a3ba-101">Die Seite Index Seite (*wwwroot/Index.html*) enthält ein Skript, das die `AuthenticationService` in JavaScript definiert.</span><span class="sxs-lookup"><span data-stu-id="2a3ba-101">The Index page (*wwwroot/index.html*) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="2a3ba-102">`AuthenticationService` behandelt die Details des oidc-Protokolls auf niedriger Ebene.</span><span class="sxs-lookup"><span data-stu-id="2a3ba-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="2a3ba-103">Die APP ruft intern die im Skript definierten Methoden auf, um die Authentifizierungs Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2a3ba-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
