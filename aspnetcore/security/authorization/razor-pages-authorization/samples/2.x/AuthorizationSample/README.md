# <a name="aspnet-core-authorization-sample"></a>Beispiel für ASP.net Core Autorisierung

In diesem Beispiel wird die Verwendung von Razor Pages Autorisierung nach Konventionen veranschaulicht. In diesem Beispiel werden die im Thema [Razor Pages Autorisierungs Konventionen](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) beschriebenen Funktionen veranschaulicht.

Die Benutzer Autorisierung in diesem Beispiel verwendet die Cookie-Authentifizierungs Features, die im Thema [Verwenden der Cookie-Authentifizierung ohne ASP.net Core Identität](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) beschrieben werden. Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen. Weitere Informationen zur Verwendung ASP.net Core Identität finden Sie unter [Einführung in die Identität auf ASP.net Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Verwenden Sie die **maria.rodriguez@contoso.com** e-Mail-Adresse zum Authentifizieren des Benutzers mit einem beliebigen Kennwort. Der Benutzer wird in der `AuthenticateUser`-Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert. In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.

## <a name="examples-in-this-sample"></a>Beispiele

| Funktion | BESCHREIBUNG |
| --- | --- |
| [Autorität](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Fügt der Seite einen [autorizefilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) mit dem angegebenen Pfad hinzu. |
| [Autorizefolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Fügt einen [autorizefilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) zu allen Seiten in einem Ordner mit dem angegebenen Pfad hinzu. |
| [Zuordnung von "Zuweisung"](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Fügt einer Seite mit dem angegebenen Pfad einen " [depwanonymousfilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) " hinzu. |
| ["Ordwanonymoustofolder"](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Fügt allen Seiten in einem Ordner mit dem angegebenen Pfad einen " [depwanonymousfilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) " hinzu. |
