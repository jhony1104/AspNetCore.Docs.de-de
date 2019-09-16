---
title: Einführung in die Identität auf ASP.net Core
author: rick-anderson
description: Verwenden Sie die Identität mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 325a61e6038e79b9a0db72c8360a5cbff2c8ddae
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011206"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Einführung in die Identität auf ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.net Core Identität ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt. Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden. Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).

Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern. Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.

[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)).

In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden. Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>Adddefaultidentity und addidentity

[Adddefaultidentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) wurde in ASP.net Core 2,1 eingeführt. Das `AddDefaultIdentity` Aufrufen von ähnelt dem Aufrufen der folgenden:

* [Addidentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>Erstellen einer Web-App mit Authentifizierung

Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Datei** > **Neu** > **Projekt** aus.
* Wählen Sie **ASP.NET Core-Webanwendung** aus. Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen. Klicken Sie auf **OK**.
* Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.
* Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit. Die Razor-Klassenbibliothek für die Identität macht Endpunkte mit dem `Identity` Bereich verfügbar. Beispiel:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Anwenden von Migrationen

Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test Registrierung und Anmeldung

Führen Sie die APP aus, und registrieren Sie einen Benutzer. Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Konfigurieren von Identitäts Diensten

Dienste werden in `ConfigureServices`hinzugefügt. Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Der vorangehende Code konfiguriert die Identität mit Standard Options Werten. Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.

   Die Identität wird durch Aufrufen von " [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)" aktiviert. `UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Dienste werden der Anwendung über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)zur Verfügung gestellt.

   Die Identität wird für die Anwendung aktiviert, `UseAuthentication` indem in `Configure` der-Methode aufgerufen wird. `UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Diese Dienste werden der Anwendung über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)zur Verfügung gestellt.

   Die Identität wird für die Anwendung aktiviert, `UseIdentity` indem in `Configure` der-Methode aufgerufen wird. `UseIdentity`Fügt der Anforderungs Pipeline cookiebasierte Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Gerüst: registrieren, anmelden und Abmelden

Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus. Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext`:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.

---

### <a name="examine-register"></a>Register überprüfen

::: moniker range=">= aspnetcore-2.1"

   Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen. Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für `_userManager` das-Objekt erstellt. `_userManager`wird durch Abhängigkeitsinjektion bereitgestellt):

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Wenn ein Benutzer auf den Link **registrieren** klickt, `Register` wird die Aktion für `AccountController`aufgerufen. Die `Register` Aktion erstellt den Benutzer durch Aufrufen `CreateAsync` von für `_userManager` `AccountController` das-Objekt (bereitgestellt von von der Abhängigkeitsinjektion):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`-Befehl angemeldet.

   **Hinweis**: Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.

### <a name="log-in"></a>Anmelden

::: moniker range=">= aspnetcore-2.1"

Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:

* Der Link " **Anmelden** " ist ausgewählt.
* Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.

Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen. `PasswordSignInAsync`wird für das `_signInManager` -Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   Die- `Controller` Basisklasse macht `User` eine-Eigenschaft verfügbar, auf die Sie über Controller Methoden zugreifen können. Beispielsweise können `User.Claims` Sie Autorisierungs Entscheidungen auflisten und treffen. Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Das Anmeldeformular wird angezeigt, wenn Benutzer den Link Anmelden auswählen oder umgeleitet werden, wenn Sie auf eine Seite zugreifen, **für** die eine Authentifizierung erforderlich ist. Wenn der Benutzer das Formular auf der Anmeldeseite sendet, wird `AccountController` die `Login` Aktion aufgerufen.

Die `Login` Aktion ruft `PasswordSignInAsync` `_signInManager` für`AccountController` das-Objekt auf (bereitgestellt von von der Abhängigkeitsinjektion).

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

Die Basisklasse`Controller` ( `PageModel`oder) macht eine `User` -Eigenschaft verfügbar. `User.Claims` Kann z. b. aufgelistet werden, um Autorisierungs Entscheidungen zu treffen.

::: moniker-end

### <a name="log-out"></a>Abmelden

::: moniker range=">= aspnetcore-2.1"

Der Link " **Abmelden** " ruft `LogoutModel.OnPost` die Aktion auf. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.

Post wird in den *Seiten "pages/Shared/_LoginPartial. cshtml*" angegeben:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Wenn Sie auf den Link **Abmelden** klicken `LogOut` , wird die Aktion aufgerufen.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   Der vorangehende Code ruft `_signInManager.SignOutAsync` die-Methode auf. Die `SignOutAsync` -Methode löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.

::: moniker-end

## <a name="test-identity"></a>Test Identität

Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten. Fügen Sie zum Testen der [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Identität der Seite Datenschutz hinzu.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link. Sie werden zur Anmeldeseite umgeleitet.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Identität erkunden

So untersuchen Sie die Identität ausführlicher:

* [Benutzeroberflächen Quelle für vollständige Identität erstellen](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.

::: moniker-end

## <a name="identity-components"></a>Identitäts Komponenten

::: moniker range=">= aspnetcore-2.1"

Alle Identitäts abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.

::: moniker-end

Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrieren zu ASP.net Core Identität

Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).

## <a name="setting-password-strength"></a>Festlegen der Kenn Wort Stärke

Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
