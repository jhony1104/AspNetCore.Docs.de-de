## <a name="multiple-authentication-providers"></a>Mehrere Authentifizierungsanbieter

Wenn die App mehrere Anbieter benötigt, verketten Sie die Erweiterungsmethoden für Anbieter hinter [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
