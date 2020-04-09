---
title: Hinzufügen, Herunterladen und Löschen von Benutzerdaten zu Identity in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie Sie in einem ASP.NET Core-Projekt benutzerdefinierte Benutzerdaten zu Identity hinzufügen. Löschen Sie Daten pro DSGVO.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501232"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Hinzufügen, Herunterladen und Löschen benutzerdefinierter Benutzerdaten zu Identity in einem ASP.NET Core-Projekt

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel werden die folgenden Aufgaben erläutert:

* Fügen Sie einer ASP.NET Core-Web-App benutzerdefinierte Benutzerdaten hinzu.
* Markieren Sie das benutzerdefinierte <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> Benutzerdatenmodell mit dem Attribut, damit es automatisch zum Herunterladen und Löschen verfügbar ist. Die Möglichkeit, die Daten herunterzuladen und zu löschen, trägt dazu bei, die [DSGVO-Anforderungen](xref:security/gdpr) zu erfüllen.

Das Projektbeispiel wird aus einer Razor Pages-Web-App erstellt, aber die Anweisungen sind für eine ASP.NET Core MVC-Web-App ähnlich.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([downloaden](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Erstellen einer Razor-Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**. Benennen Sie das Projekt **WebApp1,** wenn Sie es mit dem Namespace des [Download-Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) übereinstimmen möchten.
* Auswählen **ASP.NET Core Web Application** > **OK**
* Wählen Sie **ASP.NET Core 3.0** in der Dropdown-Liste
* **Webanwendung** > **OK** auswählen
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**. Benennen Sie das Projekt **WebApp1,** wenn Sie es mit dem Namespace des [Download-Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) übereinstimmen möchten.
* Auswählen **ASP.NET Core Web Application** > **OK**
* Wählen Sie **ASP.NET Core 2.2** in der Dropdown-Liste
* **Webanwendung** > **OK** auswählen
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Ausführen des Identitätsgerüsts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Im **Projektmappen-Explorer**klicken Sie mit der rechten Maustaste auf das Projekt > **Neues** > **Gerüstelement**hinzufügen .
* Wählen Sie im linken Bereich des Dialogfelds **Gerüst hinzufügen** die Option **Identität** > **hinzufügen**aus.
* Im Dialogfeld **Identität hinzufügen** die folgenden Optionen:
  * Wählen Sie die vorhandene Layout-Datei *aus: /Pages/Shared/_Layout.cshtml*
  * Wählen Sie die folgenden zu überschreibenden Dateien aus:
    * **Konto/Register**
    * **Konto/Verwaltung/Index**
  * Wählen **+** Sie die Schaltfläche aus, um eine neue **Data-Kontextklasse**zu erstellen. Akzeptieren Sie den Typ (**WebApp1.Models.WebApp1Context,** wenn das Projekt **WebApp1**heißt .
  * Wählen **+** Sie die Schaltfläche aus, um eine neue **Benutzerklasse**zu erstellen. Akzeptieren Sie den Typ (**WebApp1User,** wenn das Projekt den Namen **WebApp1**) > **Hinzufügen .**
* Wählen Sie **Hinzufügen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.NET Core-Gerüst noch nicht installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie der Projektdatei (.csproj) einen Paketverweis zu [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu. Führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl aus, um die Optionen für das Identitätsgerüst aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner das Identitätsgerüst aus:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout,](xref:security/authentication/scaffold-identity#efm) um die folgenden Schritte auszuführen:

* Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.
* `UseAuthentication` wurde `Startup.Configure` hinzugefügt.
* Der `<partial name="_LoginPartial" />` Layoutdatei hinzufügen.
* Testen der App:
  * Registrieren eines Benutzers
  * Wählen Sie den neuen Benutzernamen (neben dem **Abmeldelink)** aus. Möglicherweise müssen Sie das Fenster erweitern oder das Symbol der Navigationsleiste auswählen, um den Benutzernamen und andere Links anzuzeigen.
  * Wählen Sie die Registerkarte **Persönliche Daten** aus.
  * Wählen Sie die **Schaltfläche Herunterladen** aus und haben Sie die Datei *PersonalData.json* untersucht.
  * Testen Sie die Schaltfläche **Löschen,** mit der der angemeldete Benutzer gelöscht wird.

## <a name="add-custom-user-data-to-the-identity-db"></a>Hinzufügen benutzerdefinierter Benutzerdaten zur Identitätsdatenbank

Aktualisieren `IdentityUser` Sie die abgeleitete Klasse mit benutzerdefinierten Eigenschaften. Wenn Sie das Projekt WebApp1 benannt haben, heißt die Datei *Areas/Identity/Data/WebApp1User.cs*. Aktualisieren Sie die Datei mit folgendem Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Eigenschaften mit dem [PersonalData-Attribut](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sind:

* Gelöscht, wenn die *Razor-Seite Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* aufruft. `UserManager.Delete`
* In den heruntergeladenen Daten von der *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page enthalten.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualisieren der Seite Konto/Verwalten/Index.cshtml

Aktualisieren `InputModel` Sie die in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* mit dem folgenden hervorgehobenen Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Verwalten/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Verwalten/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualisieren der Seite Konto/Register.cshtml

Aktualisieren `InputModel` Sie die in *Areas/Identity/Pages/Account/Register.cshtml.cs* mit dem folgenden hervorgehobenen Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Register.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Register.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Erstellen Sie das Projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio **Package Manager-Konsole**:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testen, Anzeigen, Herunterladen, Löschen benutzerdefinierter Benutzerdaten

Testen der App:

* Registrieren Sie einen neuen Benutzer.
* Zeigen Sie die benutzerdefinierten Benutzerdaten auf der `/Identity/Account/Manage` Seite an.
* Laden Sie die persönlichen Daten `/Identity/Account/Manage/PersonalData` der Benutzer herunter und zeigen Sie sie von der Seite an.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Hinzufügen von Ansprüchen zur Identität mithilfe von IUserClaimsPrincipalFactory<ApplicationUser>

Mithilfe der `IUserClaimsPrincipalFactory<T>` Schnittstelle können ASP.NET Core Identity zusätzliche Ansprüche hinzugefügt werden. Diese Klasse kann der App `Startup.ConfigureServices` in der Methode hinzugefügt werden. Fügen Sie die benutzerdefinierte Implementierung der Klasse wie folgt hinzu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Der Democode `ApplicationUser` verwendet die Klasse. Diese Klasse `IsAdmin` fügt eine Eigenschaft hinzu, die zum Hinzufügen des zusätzlichen Anspruchs verwendet wird.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

Die `AdditionalUserClaimsPrincipalFactory` implementiert die `UserClaimsPrincipalFactory`-Schnittstelle. Ein neuer Rollenanspruch wird `ClaimsPrincipal`der hinzugefügt.

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Der zusätzliche Anspruch kann dann in der App verwendet werden. In einer Razor-Seite kann die `IAuthorizationService` Instanz verwendet werden, um auf den Anspruchswert zuzugreifen.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
