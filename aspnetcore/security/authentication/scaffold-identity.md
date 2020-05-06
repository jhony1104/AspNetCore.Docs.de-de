---
title: Gerüst Identity in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie Identity ein Gerüst in einem ASP.net Core Projekt aufbauen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768389"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Gerüst Identity in ASP.net Core Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Anwendungen, die Identity einschließen, können das Gerüsten anwenden, um den Quellcode, der in Identity Razor der Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in Identity der RCL. Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.

Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL Identity -Pakets anwenden. Sie können Code auswählen Identity , der generiert werden soll.

Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen. In diesem Dokument werden die erforderlichen Schritte zum durch Identity führen eines Gerüstbau Updates erläutert.

Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen. Überprüfen Sie die Änderungen, Identity nachdem Sie das Gerüst ausgeführt haben.

Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen Identitymit verwendet werden. Dienste oder Service-stubdienste werden beim IdentityGerüstbau nicht generiert. Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden. Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)

Beim Gerüstbau Identity mit einem neuen Datenkontext in einem Projekt mit vorhandenen individuellen Konten:

* Entfernen `Startup.ConfigureServices`Sie in die Aufrufe von:
  * `AddDbContext`
  * `AddDefaultIdentity`

Beispielsweise werden `AddDbContext` und `AddDefaultIdentity` im folgenden Code auskommentiert:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Der vorangehende Code kommentiert den Code, der in " *Areas/Identity/IdentityHostingStartup.cs* " dupliziert wird.

Apps, die mit einzelnen Konten erstellt wurden, sollten in der Regel ***keinen*** neuen Datenkontext erstellen.

## <a name="scaffold-identity-into-an-empty-project"></a>Gerüst der Identität in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Gerüst der Identität in ein Razor Projekt ohne vorhandene Autorisierung

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identitywird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und Layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Authentifizierung aktivieren

Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Layoutänderungen

Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Gerüst der Identität in ein Razor Projekt mit Autorisierung

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Einige Identity Optionen werden in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*

Identitywird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter ihostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Gerüst der Identität in ein MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Benutzeroberflächen Quelle für vollständige Identität erstellen

Um die vollständige Kontrolle über Identity die Benutzeroberfläche zu Identity behalten, führen Sie das Gerüst aus, und wählen Sie **alle Dateien überschreiben**

Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche Identity durch in einer ASP.net Core 2,1-Web-App. Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle Identity über die Benutzeroberfläche zu haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Der Standard Identity Wert wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren Sie `IEmailSender` eine Implementierung, z. b.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Registerseite deaktivieren

So deaktivieren Sie die Benutzerregistrierung:

* Gerüstbau Identity. Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein. Beispiel:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualisieren Sie *BereicheIdentity//Pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Kommentieren Sie den Registrierungs Link aus *Bereichen/Identity/Pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualisieren Sie die Seite " *Bereiche/Identity/Pages/Account/RegisterConfirmation* ".

  * Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.
  * Entfernen Sie den Bestätigungscode aus `PageModel`dem:

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Verwenden einer anderen APP zum Hinzufügen von Benutzern

Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit. Zu den Optionen zum Hinzufügen von Benutzern gehören:

* Eine dedizierte admin-Web-App.
* Eine Konsolen-app.

Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:

* Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.
* Für jeden Benutzer wird ein sicheres Kennwort generiert.
* Der Benutzer wird der Identity -Datenbank hinzugefügt.
* Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.

## <a name="prevent-publish-of-static-identity-assets"></a>Veröffentlichen statischer Identity Assets verhindern

Informationen zum Verhindern der Identity Veröffentlichung statischer Assets im Webstamm <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>finden Sie unter.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.net Core 2,1 und höher [ Identity ASP.net Core](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Anwendungen, die Identity einschließen, können das Gerüsten anwenden, um den Quellcode, der in Identity Razor der Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in Identity der RCL. Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.

Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL Identity -Pakets anwenden. Sie können Code auswählen Identity , der generiert werden soll.

Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen. In diesem Dokument werden die erforderlichen Schritte zum durch Identity führen eines Gerüstbau Updates erläutert.

Wenn das Identity Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt. Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Identity Abschluss des Gerüstbau Updates erforderlich ist. Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".

Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen. Überprüfen Sie die Änderungen, Identity nachdem Sie das Gerüst ausgeführt haben.

> [!NOTE]
> Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen Identitymit verwendet werden. Dienste oder Service-stubdienste werden beim IdentityGerüstbau nicht generiert. Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden. Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)

## <a name="scaffold-identity-into-an-empty-project"></a>Gerüst der Identität in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Fügen Sie die folgenden markierten Aufrufe der `Startup` -Klasse hinzu:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Gerüst der Identität in ein Razor Projekt ohne vorhandene Autorisierung

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identitywird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und Layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Authentifizierung aktivieren

In der `Configure` -Methode der `Startup` -Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen aufgerufen:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Layoutänderungen

Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Gerüst der Identität in ein Razor Projekt mit Autorisierung

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Einige Identity Optionen werden in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*

Identitywird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert. Weitere Informationen finden Sie unter ihostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen abrufen:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Gerüst der Identität in ein MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Benutzeroberflächen Quelle für vollständige Identität erstellen

Um die vollständige Kontrolle über Identity die Benutzeroberfläche zu Identity behalten, führen Sie das Gerüst aus, und wählen Sie **alle Dateien überschreiben**

Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche Identity durch in einer ASP.net Core 2,1-Web-App. Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle Identity über die Benutzeroberfläche zu haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Der Standard Identity Wert wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren Sie `IEmailSender` eine Implementierung, z. b.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Registerseite deaktivieren

So deaktivieren Sie die Benutzerregistrierung:

* Gerüstbau Identity. Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein. Beispiel:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualisieren Sie *BereicheIdentity//Pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Kommentieren Sie den Registrierungs Link aus *Bereichen/Identity/Pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualisieren Sie die Seite " *Bereiche/Identity/Pages/Account/RegisterConfirmation* ".

  * Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.
  * Entfernen Sie den Bestätigungscode aus `PageModel`dem:

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Verwenden einer anderen APP zum Hinzufügen von Benutzern

Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit. Zu den Optionen zum Hinzufügen von Benutzern gehören:

* Eine dedizierte admin-Web-App.
* Eine Konsolen-app.

Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:

* Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.
* Für jeden Benutzer wird ein sicheres Kennwort generiert.
* Der Benutzer wird der Identity -Datenbank hinzugefügt.
* Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end