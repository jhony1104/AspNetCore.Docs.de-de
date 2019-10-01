---
title: Gerüst Identität in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie die Identität in ein ASP.net Core Projekt eingerüstbau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703770"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Gerüst Identität in ASP.net Core Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.net Core 2,1 und höher stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit. Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“. Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.

Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden. Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.

Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen. In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.

Wenn das Identitäts Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt. Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Durchführen der Aktualisierung des Identitäts Gerüsts erforderlich ist. Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".

Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen. Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.

> [!NOTE]
> Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden. Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden. Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)

## <a name="scaffold-identity-into-an-empty-project"></a>Gerüst der Identität in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Fügen Sie die folgenden hervorgehobenen Aufrufe der `Startup`-Klasse hinzu:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung

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

Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und Layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Aktivieren der Authentifizierung

In der `Configure`-Methode der `Startup`-Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles` aufgerufen:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Layoutänderungen

Optional: Fügen Sie die Anmelde partielle (`_LoginPartial`) der Layoutdatei hinzu:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Gerüst der Identität in ein Razor-Projekt mit Autorisierung

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
Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

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

Optional: Fügen Sie der Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell (`_LoginPartial`) hinzu:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml* .

Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert. Weitere Informationen finden Sie unter ihostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles` aufzurufen:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Gerüst der Identität in ein MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Benutzeroberflächen Quelle für vollständige Identität erstellen

Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**

Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen. Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Die Standard Identität wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren Sie eine `IEmailSender`-Implementierung, z. b.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher](xref:migration/20_21#changes-to-authentication-code)
