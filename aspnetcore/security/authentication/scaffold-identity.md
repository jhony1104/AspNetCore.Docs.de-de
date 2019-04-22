---
title: Gerüst-Identität in ASP.NET Core-Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie die Identität in einem ASP.NET Core-Projekt zu erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 7445db31e461bf61e8a91af7239187a6ece9d011
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705470"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Gerüst-Identität in ASP.NET Core-Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 und höher bietet [ASP.NET Core Identity](xref:security/authentication/identity) als eine [Razor-Klassenbibliothek](xref:razor-pages/ui-class). Anwendungen, die Identität enthalten, können der gerüstbauer um selektiv hinzufügen, den in der Identität Razor Klasse-Bibliothek (RCL) enthaltenen Quellcode anwenden. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“. Um erhalten vollständige Kontrolle über die Benutzeroberfläche, und verwenden Sie nicht die Standardeinstellung RCL, finden Sie im Abschnitt [erstellen vollständige Benutzeroberfläche identitätsquelle](#full).

Anwendungen, die **nicht** gehören Authentifizierung kann der gerüstbauer zum Hinzufügen des Pakets RCL Identität anwenden. Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.

Obwohl der gerüstbauer größte Teil des erforderlichen Codes generiert, müssen Sie Ihr Projekt zum Abschließen des Vorgangs aktualisieren. Dieses Dokument erläutert die Schritte, um eine Identität Gerüstbau Aktualisierung abzuschließen.

Wenn der Identity-gerüstbauer ausgeführt wird, eine *ScaffoldingReadme.txt* Datei wird im Projektverzeichnis erstellt. Die *ScaffoldingReadme.txt* -Datei enthält allgemeine Anweisungen, für welche Anforderungen für die Identity-Gerüstbau-Aktualisierung abgeschlossen ist. Dieses Dokument enthält ausführlichere Anweisungen als die *ScaffoldingReadme.txt* Datei.

Es empfiehlt sich ein Quellcodeverwaltungssystem, die Unterschiede zwischen zeigt und lässt sich aus Änderungen zurück. Überprüfen Sie die Änderungen nach dem Ausführen der gerüstbauer Identität.

> [!NOTE]
> Dienste sind erforderlich, wenn mit [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Wiederherstellung](xref:security/authentication/accconfirm), und andere Sicherheitsfunktionen, mit der Identität. Dienste oder Dienst-Stubs werden nicht generiert werden, wenn Gerüstbau für Identität. Dienste zum Aktivieren dieser Funktionen müssen manuell hinzugefügt werden. Beispielsweise finden Sie unter [müssen e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Gerüst-Identität in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Fügen Sie folgenden hervorgehobenen Aufrufe der `Startup` Klasse:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Gerüst-Identität in einer Razor-Projekt ohne vorhandene Autorisierung

<!--
set projNam=RPnoAuth
set projType=webapp
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

Identität wird im konfiguriert *Areas/Identity/IdentityHostingStartup.cs*. Weitere Informationen finden Sie unter [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Aktivieren der Authentifizierung

In der `Configure` Methode der `Startup` Klasse, rufen Sie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Änderungen am Layout

Optional: Hinzufügen die Anmeldung, die teilweise (`_LoginPartial`) zur Layoutdatei:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Gerüst-Identität in einer Razor-Projekt mit Autorisierung

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Einige identitätsoptionen in konfiguriert *Areas/Identity/IdentityHostingStartup.cs*. Weitere Informationen finden Sie unter [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Gerüst-Identität in einem MVC-Projekt ohne vorhandene Autorisierung

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

Optional: Hinzufügen die Anmeldung, die teilweise (`_LoginPartial`) auf die *Views/Shared/_Layout.cshtml* Datei:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Verschieben der *Pages/Shared/_LoginPartial.cshtml* Datei *Views/Shared/_LoginPartial.cshtml*

Identität wird im konfiguriert *Areas/Identity/IdentityHostingStartup.cs*. Weitere Informationen finden Sie unter IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Rufen Sie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Gerüst-Identität in einem MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Löschen der *Seiten/Shared* Ordner und die Dateien in diesem Ordner.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Erstellen Sie die vollständige Benutzeroberfläche identitätsquelle

Um vollständige Kontrolle über die Identity-Benutzeroberfläche gewährleisten zu können, führen Sie die Identity-gerüstbauer, und wählen Sie **alle Dateien überschreiben**.

Der folgende hervorgehobene Code zeigt die Änderungen an die Identity-Standardbenutzeroberfläche mit Identity in einer ASP.NET Core 2.1-Web-app zu ersetzen. Möglicherweise möchten diese Option, um die vollständige Kontrolle über die Benutzeroberfläche der Identität haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Der Standard-Identität wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Die folgenden legt der Code die [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath für](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), und [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren einer `IEmailSender` Implementierung, z.B.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen an Code für die Authentifizierung für ASP.NET Core 2.1 und höher](xref:migration/20_21#changes-to-authentication-code)
