---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten auf Identität in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte Benutzerdaten Identität in einem ASP.NET Core-Projekt hinzugefügt. Löschen von Daten pro DSGVO.
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: e08c02e2e5d4a429aae10c59e7ae3ea48c975067
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885548"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten die Identität in einem ASP.NET Core-Projekt

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel zeigt, wie Sie:

* Fügen Sie benutzerdefinierter Benutzerdaten in einer ASP.NET Core-Web-app hinzu.
* Markieren Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann. Sodass die Daten, die heruntergeladen und gelöscht werden kann hilft bei der Erfüllung [DSGVO](xref:security/gdpr) Anforderungen.

Die Project-Beispiels aus einer Razor Pages-Web-app erstellt wird, aber die Anweisungen ähneln denen für eine ASP.NET Core MVC-Web-app.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Erstellen einer Razor-Web-App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus. Nennen Sie das Projekt **"WebApp1"** Wenn Sie möchten einen übereinstimmenden Namespace die [Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Code.
* Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.
* Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.
* **Webanwendung** auswählen > **OK**
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus. Nennen Sie das Projekt **"WebApp1"** Wenn Sie möchten einen übereinstimmenden Namespace die [Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Code.
* Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.
* Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.
* **Webanwendung** auswählen > **OK**
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Führen Sie die Identity-gerüstbauer

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.
* Im Dialogfeld **Identität hinzufügen** werden die folgenden Optionen angezeigt:
  * Wählen Sie die vorhandenen Layoutdatei *~/Pages/Shared/_Layout.cshtml*
  * Wählen Sie die folgenden Dateien überschreiben:
    * **Konto/registrieren**
    * **Konto / / Index verwalten**
  * Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**. Akzeptieren Sie den Typ (**WebApp1.Models.WebApp1Context** Wenn das Projekt den Namen **"WebApp1"** ).
  * Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Benutzerklasse**. Akzeptieren Sie den Typ (**WebApp1User** Wenn das Projekt den Namen **"WebApp1"** ) > **hinzufügen**.
* Wählen Sie **Hinzufügen** aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie einen Paketverweis auf [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) zur Projektdatei (.csproj). Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner der Identity-gerüstbauer:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Führen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) die folgenden Schritte ausführen:

* Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.
* Fügen Sie `UseAuthentication` zu `Startup.Configure` hinzu.
* Hinzufügen `<partial name="_LoginPartial" />` zur Layoutdatei.
* Testen der App:
  * Registrieren eines Benutzers
  * Wählen Sie den neuen Benutzernamen ein (neben der **Logout** Link). Möglicherweise müssen Sie das Fenster erweitern, oder wählen Sie das Navigationssymbol Leiste, um den Benutzernamen und anderen Links anzuzeigen.
  * Wählen Sie die **personenbezogene Daten** Registerkarte.
  * Wählen Sie die **herunterladen** Schaltfläche und untersucht die *PersonalData.json* Datei.
  * Testen der **löschen** Schaltfläche, die der angemeldete Benutzer löscht.

## <a name="add-custom-user-data-to-the-identity-db"></a>Hinzufügen von benutzerdefinierten Benutzerdaten mit der Identity-DB

Update der `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften. Wenn Sie das Projekt "WebApp1" genannt, kann die Datei heißt *Areas/Identity/Data/WebApp1User.cs*. Aktualisieren Sie die Datei mit dem folgenden Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":

* Gelöscht, wenn die *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor-Seite ruft `UserManager.Delete`.
* In der heruntergeladenen Daten enthalten die *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor-Seite.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualisieren Sie die Seite "Account/Manage/Index.cshtml"

Update der `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* mit folgendem hervorgehobenen Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Update der *Areas/Identity/Pages/Account/Manage/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Update der *Areas/Identity/Pages/Account/Manage/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualisiert die Account/Register.cshtml-Seite

Update der `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* mit folgendem hervorgehobenen Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Update der *Areas/Identity/Pages/Account/Register.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Update der *Areas/Identity/Pages/Account/Register.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Erstellen Sie das Projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Fügen Sie eine Migration für die benutzerdefinierten Daten hinzu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio **-Paket-Manager-Konsole**:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Test erstellen, anzeigen, herunterladen und Löschen von benutzerdefinierten Daten

Testen der App:

* Registrieren Sie einen neuen Benutzer.
* Zeigen Sie die benutzerdefinierten Daten, auf die `/Identity/Account/Manage` Seite.
* Herunterladen und Anzeigen der Benutzer persönliche Daten von der `/Identity/Account/Manage/PersonalData` Seite.
