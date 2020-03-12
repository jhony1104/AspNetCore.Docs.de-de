<span data-ttu-id="47da2-101">Die Seite Index Seite (*wwwroot/Index.html*) enthält ein Skript, das die `AuthenticationService` in JavaScript definiert.</span><span class="sxs-lookup"><span data-stu-id="47da2-101">The Index page (*wwwroot/index.html*) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="47da2-102">`AuthenticationService` behandelt die Details des oidc-Protokolls auf niedriger Ebene.</span><span class="sxs-lookup"><span data-stu-id="47da2-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="47da2-103">Die APP ruft intern die im Skript definierten Methoden auf, um die Authentifizierungs Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="47da2-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
