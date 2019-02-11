---
title: Einführung in die Identität in ASP.NET Core
author: rick-anderson
description: Verwenden Sie Identität mit einer ASP.NET Core-app. Erfahren Sie, wie Anforderungen für Kennwörter (RequireDigit, RequiredLength, RequiredUniqueChars usw.) festgelegt.
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: 1a4e7fb3ac6a767ca17127dd58a9b9e65ed9a00b
ms.sourcegitcommit: b72bbc9ae91e4bd37c9ea9b2d09ebf47afb25dd7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55739682"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Einführung in die Identität in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity handelt es sich um ein Mitgliedschaftssystem, das ASP.NET Core-apps Anmeldefunktionalität hinzufügt. Benutzer können ein Konto erstellen, mit der Anmeldung Informationen in die Identität oder einem externen Anmeldeanbieter können. Einschließen von unterstützten externen anmeldungsanbietern [Facebook, Google, Twitter und Microsoft Account](xref:security/authentication/social/index).

Identität kann mithilfe einer SQL Server-Datenbank zum Speichern von Benutzernamen und Kennwörter, Profildaten konfiguriert werden. Alternativ kann einem anderen permanenten Speicher verwendet werden, z. B. Azure Table Storage.

[Anzeigen oder Herunterladen der Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([zum Herunterladen)](xref:index#how-to-download-a-sample)).

In diesem Thema erfahren Sie, wie Identität verwenden, um zu registrieren, melden Sie sich, und melden Sie sich ein Benutzer. Ausführlichere Anweisungen zum Erstellen von apps, die Identität verwenden, finden Sie im Abschnitt "Nächste Schritte" am Ende dieses Artikels.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>
## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity und AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) wurde in ASP.NET Core 2.1 eingeführt. Aufrufen von `AddDefaultIdentity` entspricht dem folgenden Aufruf:

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

Finden Sie unter [AddDefaultIdentity Quelle](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) für Weitere Informationen.

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>Erstellen einer Web-app mit Authentifizierung

Erstellen Sie ein Projekt für die ASP.NET Core-Webanwendung mit einzelnen Benutzerkonten.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Datei** > **Neu** > **Projekt** aus.
* Wählen Sie **ASP.NET Core-Webanwendung** aus. Nennen Sie das Projekt **"WebApp1"** haben denselben Namespace aufweist wie das Projekt zum Herunterladen. Klicken Sie auf **OK**.
* Wählen Sie eine ASP.NET Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**.
* Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

Enthält das generierte Projekt [ASP.NET Core Identity](xref:security/authentication/identity) als eine [Razor-Klassenbibliothek](xref:razor-pages/ui-class). Die Identity-Razor-Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich. Zum Beispiel:

* / Identity/Konto/anmelden
* / Identity/Account/Abmeldung
* / / Account/Identitätsverwaltung

### <a name="test-register-and-login"></a>Test-Registrierung und Anmeldung

Führen Sie die app aus, und registrieren Sie einen Benutzer. Je nach Bildschirmgröße, Sie müssen ggf. auf die Navigationsschaltfläche für die ein-/ausschalten, finden Sie unter den **registrieren** und **Anmeldung** Links.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>
### <a name="configure-identity-services"></a>Konfigurieren der Identitätsdienste

Dienste hinzugefügt werden, `ConfigureServices`. Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Der vorangehende Code konfiguriert Identität mit Standardwerten für die Option. Dienste, die app über den zur Verfügung gestellt [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

   Identität ist aktiviert, durch den Aufruf [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` Fügt der Authentifizierung [Middleware](xref:fundamentals/middleware/index) zu der Anforderungspipeline.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Dienste stehen der Anwendung über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

   Identität für die Anwendung aktiviert ist, durch den Aufruf `UseAuthentication` in die `Configure` Methode. `UseAuthentication` Fügt der Authentifizierung [Middleware](xref:fundamentals/middleware/index) zu der Anforderungspipeline.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Diese Dienste stehen der Anwendung über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

   Identität für die Anwendung aktiviert ist, durch den Aufruf `UseIdentity` in die `Configure` Methode. `UseIdentity` Fügt der cookiebasierte Authentifizierung [Middleware](xref:fundamentals/middleware/index) zu der Anforderungspipeline.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Weitere Informationen finden Sie unter den [IdentityOptions Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Erstellen des Gerüsts für registrieren, Anmeldung und Abmeldung

Führen Sie die [Gerüst Identity in einer Razor-Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen zum Generieren der der Code in diesem Abschnitt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie die Dateien registrieren, Anmeldung und Abmeldung hinzu.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das Projekt mit dem Namen erstellt **"WebApp1"**, führen Sie die folgenden Befehle. Verwenden Sie andernfalls den richtigen Namespace für die `ApplicationDbContext`:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell arbeitet mit Semikolon als Befehlstrennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons aus der Datei mit Escapezeichen, oder fügen Sie die Liste der Dateien in doppelte Anführungszeichen, wie im vorherigen Beispiel gezeigt.

---

### <a name="examine-register"></a>Überprüfen Sie die Registrierung

::: moniker range=">= aspnetcore-2.1"

   Wenn ein Benutzer klickt der **registrieren** Link die `RegisterModel.OnPostAsync` Aktion aufgerufen wird. Der Benutzer wird erstellt, indem [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) auf die `_userManager` Objekt. `_userManager` wird durch Dependency Injection bereitgestellt):

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Wenn ein Benutzer klickt der **registrieren** Link die `Register` Aktion wird aufgerufen, auf `AccountController`. Die `Register` Aktion wird den Benutzer erstellt, durch den Aufruf `CreateAsync` auf die `_userManager` Objekt (bereitgestellt, um `AccountController` durch Dependency Injection):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Wenn der Benutzer erfolgreich erstellt wurde, der angemeldete Benutzer wird durch den Aufruf von `_signInManager.SignInAsync`.

   **Hinweis**: Finden Sie unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) Schritte, um sofortige Anmeldung bei der Registrierung zu verhindern.

### <a name="log-in"></a>Anmelden

::: moniker range=">= aspnetcore-2.1"

Das Anmeldeformular wird angezeigt, wenn:

* Die **melden Sie sich bei** Link ausgewählt wird.
* Ein Benutzer versucht, eine zugriffsbeschränkte Seite zugreifen, die sie autorisiert sind nicht, den Zugriff auf **oder** Wenn dies nicht getan haben sie vom System authentifiziert wurde.

Wenn das Formular auf der Anmeldeseite gesendet wird, die `OnPostAsync` Aktion aufgerufen wird. `PasswordSignInAsync` wird aufgerufen, auf die `_signInManager` Objekt (bereitgestellt durch Dependency Injection).

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   Die Basis `Controller` -Klasse macht eine `User` -Eigenschaft, die Sie von Controllermethoden aus zugreifen können. Sie können z. B. auflisten `User.Claims` und autorisierungsentscheidungen treffen. Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Das Anmeldeformular wird angezeigt, wenn ein Benutzer auf die **melden Sie sich bei** verknüpfen oder umgeleitet werden, wenn eine Seite zugreifen, die eine Authentifizierung erforderlich ist. Wenn der Benutzer das Formular auf die Anmeldeseite, sendet der `AccountController` `Login` Aktion aufgerufen wird.

Die `Login` Aktionsaufrufen `PasswordSignInAsync` auf die `_signInManager` Objekt (bereitgestellt, um `AccountController` durch Dependency Injection).

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

Die Basis (`Controller` oder `PageModel`)-Klasse macht eine `User` Eigenschaft. Z. B. `User.Claims` aufgelistet werden kann, um autorisierungsentscheidungen zu treffen.

::: moniker-end

### <a name="log-out"></a>Melden Sie sich ab

::: moniker range=">= aspnetcore-2.1"

Die **Abmelden** Link Ruft die `LogoutModel.OnPost` Aktion. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht der Ansprüche des Benutzers in einem Cookie gespeichert. Nicht nach dem Aufruf umleiten `SignOutAsync` oder der Benutzer wird **nicht** abgemeldet werden.

POST wird angegeben, der *Pages/Shared/_LoginPartial.cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Auf der **Abmelden** verknüpfen Aufrufe der `LogOut` Aktion.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   Der vorangehende Code Ruft die `_signInManager.SignOutAsync` Methode. Die `SignOutAsync` Methode löscht der Ansprüche des Benutzers in einem Cookie gespeichert.

::: moniker-end

## <a name="test-identity"></a>Testidentität

Die Standardprojektvorlagen für Web zulassen anonymen Zugriff auf den Startseiten. Um die Identität zu testen, fügen [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , die "Datenschutz".

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die app, und wählen Sie die **Datenschutz** Link. Sie werden zur Anmeldeseite umgeleitet.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Untersuchen Sie die Identität

Identität noch ausführlicher untersuchen zu können:

* [Erstellen Sie die vollständige Benutzeroberfläche identitätsquelle](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Überprüfen Sie die Quelle der einzelnen Seiten und durchlaufen Sie den Debugger an.

::: moniker-end

## <a name="identity-components"></a>Identitätskomponenten

::: moniker range=">= aspnetcore-2.1"

Alle Identity abhängigen NuGet-Pakete sind in enthalten die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).

::: moniker-end

Das primäre Paket für die Identität ist [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Dieses Paket enthält die Kerngruppe von Schnittstellen für ASP.NET Core Identity und befindet sich durch `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrieren zu ASP.NET Core-Identität

Weitere Informationen und Anleitungen zum Migrieren von vorhandenen Identitätsspeicher, finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen](xref:migration/identity).

## <a name="setting-password-strength"></a>Festlegen der kennwortsicherheit

Finden Sie unter [Konfiguration](#pw) für ein Beispiel, das die Anforderungen für die Mindestlänge für Kennwörter legt diese fest.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
