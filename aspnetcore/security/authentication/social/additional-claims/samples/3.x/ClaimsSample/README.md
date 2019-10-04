# <a name="additional-claims-sample-app"></a>Zusätzliche Anspruchs Beispiel-App

Die Beispiel-App veranschaulicht Folgendes:

* Rufen Sie den Vornamen und Nachnamen des Benutzers aus Google ab, und speichern Sie die namens Ansprüche mit den von Google bereitgestellten Werten.
* Speichern Sie das Google-Zugriffs Token in den `AuthenticationProperties` des Benutzers.

So verwenden Sie die Beispiel-App:

1. Registrieren Sie die APP, und erhalten Sie eine gültige Client-ID und ein Client Geheimnis für die Google-Authentifizierung. Weitere Informationen finden Sie unter [Setup für die externe Google-Anmeldung](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Geben Sie die Client-ID und den geheimen Client Schlüssel für die app in den [googleoptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) -`Startup.ConfigureServices` an.
1. Führen Sie die APP aus, und fordern Sie die Seite meine Ansprüche an. Wenn der Benutzer nicht angemeldet ist, wird die APP an Google umgeleitet. Melden Sie sich mit Google an. Google leitet den Benutzer zurück zur APP (`/MyClaims`). Der Benutzer ist authentifiziert, und die Seite meine Ansprüche wird geladen. Der Vorname und die Ansprüche des Nachnamen sind unter **Benutzer Ansprüchen** mit den von Google bereitgestellten Werten vorhanden. Das Zugriffs Token wird unter **Authentifizierungs Eigenschaften**angezeigt.
