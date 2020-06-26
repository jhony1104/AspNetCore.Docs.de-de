---
title: Einführung in Identity ASP.net Core
author: rick-anderson
description: Verwenden Sie dies Identity mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 97cbc5f68d20c06e92a56ead85c8377d4e8ef582
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404586"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Einführung in Identity ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity:

* Ist eine API, die die Benutzeroberflächen-Anmelde Funktionalität unterstützt.
* Von werden Benutzer, Kenn Wörter, Profildaten, Rollen, Ansprüche, Token, e-Mail-Bestätigung und mehr verwaltet.

Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden. Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).

Der [ Identity Quellcode](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) ist auf GitHub verfügbar. [Gerüst Identity ](xref:security/authentication/scaffold-identity) und zeigen Sie die generierten Dateien an, um die Vorlagen Interaktion mit zu überprüfen Identity .

Identitywird in der Regel mit einer SQL Server Datenbank konfiguriert, um Benutzernamen, Kenn Wörter und Profildaten zu speichern. Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.

In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden. Hinweis: die Vorlagen behandeln Benutzername und e-Mail-Adresse für Benutzer als identisch. Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.

Die [Microsoft Identity-Plattform](/azure/active-directory/develop/) ist:

* Eine Weiterentwicklung der Azure Active Directory (Azure AD)-Entwicklerplattform.
* Nicht im Zusammenhang mit ASP.net Core Identity .

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen)](xref:index#how-to-download-a-sample)

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Erstellen einer Web-App mit Authentifizierung

Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Datei** > **neu** > **Projekt**aus.
* Wählen Sie **ASP.NET Core-Webanwendung** aus. Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen. Klicken Sie auf **OK**.
* Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.
* Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Der vorherige Befehl erstellt eine Razor Web-App mithilfe von SQLite. Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Das generierte Projekt stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar. Zum Beispiel:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Anwenden von Migrationen

Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird. Führen Sie für localdb den folgenden Befehl aus:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test Registrierung und Anmeldung

Führen Sie die APP aus, und registrieren Sie einen Benutzer. Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>IdentityDienste konfigurieren

Dienste werden in hinzugefügt `ConfigureServices` . Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Der vorangehende markierte Code konfiguriert Identity mit Standard Options Werten. Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.

Identitywird aktiviert, indem aufgerufen wird <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data). `app.UseAuthorization`ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung `UseRouting`, `UseAuthentication` , `UseAuthorization` und `UseEndpoints` müssen in der Reihenfolge aufgerufen werden, die im vorangehenden Code angezeigt wird.

Weitere Informationen zu `IdentityOptions` und `Startup` finden Sie unter <xref:Microsoft.AspNetCore.Identity.IdentityOptions> und [Anwendungsstart](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Gerüst: registrieren, anmelden und Abmelden

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu. Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus. Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.

Weitere Informationen zum Gerüstbau finden Sie unter Identity [Gerüst Identität in einem Razor Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Register überprüfen

Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen. Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync` .

Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.

### <a name="log-in"></a>Anmelden

Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:

* Der Link " **Anmelden** " ist ausgewählt.
* Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.

Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen. `PasswordSignInAsync`wird für das- `_signInManager` Objekt aufgerufen.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .

### <a name="log-out"></a>Abmelden

Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.

Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>TestIdentity

Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten. IdentityFügen Sie zum Testen Folgendes hinzu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link. Sie werden zur Anmeldeseite umgeleitet.

### <a name="explore-identity"></a>EntdeckenIdentity

Weitere Informationen finden Sie unter Identity :

* [Benutzeroberflächen Quelle für vollständige Identität erstellen](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.

## <a name="identity-components"></a>IdentityKomponenten

Alle Identity -abhängigen nuget-Pakete sind im ASP.net Core frei [gegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.

Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrieren zu ASP.net CoreIdentity

Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).

## <a name="setting-password-strength"></a>Festlegen der Kenn Wort Stärke

Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .

## <a name="adddefaultidentity-and-addidentity"></a>Adddefaultidentity und addidentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt. `AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-identity-assets"></a>Veröffentlichen statischer Identity Assets verhindern

Identity Identity Fügen Sie die folgende `ResolveStaticWebAssetsInputsDependsOn` Eigenschaft und das `RemoveIdentityAssets` Ziel der Projektdatei der APP hinzu, um zu verhindern, dass statische Assets (Stylesheets und JavaScript-Dateien für die Benutzeroberfläche) im Webstamm Verzeichnis veröffentlicht werden:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a>Nächste Schritte

* [ASP.net Core Identity Quellcode](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)
* Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .
* [KonfigurierenIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.net Core Identity ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt. Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden. Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).

Identitykann mithilfe einer SQL Server-Datenbank zum Speichern von Benutzernamen, Kenn Wörtern und Profildaten konfiguriert werden. Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.

[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)

In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden. Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>Adddefaultidentity und addidentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt. `AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Erstellen einer Web-App mit Authentifizierung

Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Datei** > **neu** > **Projekt**aus.
* Wählen Sie **ASP.NET Core-Webanwendung** aus. Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen. Klicken Sie auf **OK**.
* Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.
* Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Das generierte Projekt stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar. Zum Beispiel:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Anwenden von Migrationen

Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test Registrierung und Anmeldung

Führen Sie die APP aus, und registrieren Sie einen Benutzer. Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>IdentityDienste konfigurieren

Dienste werden in hinzugefügt `ConfigureServices` . Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Der vorangehende Code konfiguriert Identity mit Standard Options Werten. Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.

Identitywird durch Aufrufen von [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)aktiviert. `UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Gerüst: registrieren, anmelden und Abmelden

Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus. Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.

---

### <a name="examine-register"></a>Register überprüfen

Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen. Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync` .

**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.

### <a name="log-in"></a>Anmelden

Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:

* Der Link " **Anmelden** " ist ausgewählt.
* Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.

Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen. `PasswordSignInAsync`wird für das- `_signInManager` Objekt aufgerufen.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .

### <a name="log-out"></a>Abmelden

Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.

Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>TestIdentity

Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten. IdentityFügen Sie zum Testen [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) der Seite Datenschutz hinzu.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link. Sie werden zur Anmeldeseite umgeleitet.

### <a name="explore-identity"></a>EntdeckenIdentity

Weitere Informationen finden Sie unter Identity :

* [Benutzeroberflächen Quelle für vollständige Identität erstellen](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.

## <a name="identity-components"></a>IdentityKomponenten

Alle Identity abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.

Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrieren zu ASP.net CoreIdentity

Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).

## <a name="setting-password-strength"></a>Festlegen der Kenn Wort Stärke

Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .
* [KonfigurierenIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
