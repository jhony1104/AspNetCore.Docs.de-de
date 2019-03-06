# <a name="aspnet-core-authorization-sample"></a>Beispiel: ASP.NET Core-Autorisierung

Dieses Beispiel veranschaulicht die Verwendung von Razor Pages-Autorisierung durch Konventionen. In diesem Beispiel wird veranschaulicht, die in beschriebenen Features der [autorisierungskonventionen für Razor Pages](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) Thema.

Benutzerautorisierung in diesem Beispiel verwendet die Cookie-Authentifizierung, die Funktionen, in beschrieben der [verwenden der Cookieauthentifizierung ohne ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) Thema. Die Konzepte und die Beispiele in diesem Thema gelten auch für apps, die ASP.NET Core Identity verwenden. Weitere Informationen zur Verwendung von ASP.NET Core Identity finden Sie unter [Einführung in die Identität in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Verwenden Sie die e-Mail-Adresse **maria.rodriguez@contoso.com** zum Authentifizieren des Benutzers mit der ein Kennwort. Der Benutzer wird authentifiziert, der `AuthenticateUser` -Methode in der die *Pages/Account/Login.cshtml.cs* Datei. Im Real-World-Beispiel würde der Benutzer für eine Datenbank authentifiziert werden.

## <a name="examples-in-this-sample"></a>Beispiele

| Feature | Beschreibung |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Fügt eine [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf der Seite mit dem angegebenen Pfad. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Fügt eine [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) für alle Seiten in einem Ordner mit dem angegebenen Pfad. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Fügt eine [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) auf eine Seite mit dem angegebenen Pfad. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Fügt eine [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) für alle Seiten in einem Ordner mit dem angegebenen Pfad. |
