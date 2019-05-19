# <a name="additional-claims-sample-app"></a>Beispiel-App zusätzliche Ansprüche

Die Beispiel-app veranschaulicht, wie Sie:

* Rufen Sie Vorname und Nachname des Benutzers von Google und speichern Sie die Namensansprüche mit den Werten, die von Google bereitgestellt wird.
* Das Google-Zugriffstoken des Benutzers Store `AuthenticationProperties`.

So verwenden Sie die Beispiel-app

1. Registrieren der app, und erhalten Sie eine gültige Client-ID und geheimer Clientschlüssel für die Google-Authentifizierung. Weitere Informationen finden Sie unter [Google externe Anmeldung Setup](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Geben Sie den Client-ID und geheimer Clientschlüssel der App in der [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) von `Startup.ConfigureServices`.
1. Führen Sie die app aus, und fordern Sie die Seite Meine Ansprüche. Wenn der Benutzer angemeldet ist nicht, leitet die app an Google. Melden Sie sich mit Google. Google leitet den Benutzer zurück an die app (`/MyClaims`). Der Benutzer wird authentifiziert, und Laden der Seite Meine Ansprüche. Der Vorname und Nachname Ansprüche befinden sich unter **Benutzeransprüche** mit den Werten, die von Google bereitgestellt wird. Das Zugriffstoken wird angezeigt, unter **Authentifizierungseigenschaften**.
