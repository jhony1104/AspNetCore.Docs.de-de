## <a name="multiple-authentication-providers"></a><span data-ttu-id="4b815-101">Mehrere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="4b815-101">Multiple authentication providers</span></span>

<span data-ttu-id="4b815-102">Wenn die App mehrere Anbieter benötigt, verketten Sie die Erweiterungsmethoden für Anbieter hinter [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="4b815-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
