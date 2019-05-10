---
title: Migrieren von Authentifizierungs- und Identitätseinstellungen zu ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Authentifizierung und Identität in einem ASP.NET MVC-Projekt zu einem ASP.NET Core MVC-Projekt zu migrieren.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: 72e62e78e37325ec47d54abbc11a875ae87fb63a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891877"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Migrieren von Authentifizierungs- und Identitätseinstellungen zu ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

Im vorherigen Artikel wir [Konfiguration eines ASP.NET MVC-Projekts zu ASP.NET Core MVC migriert](xref:migration/configuration). In diesem Artikel migrieren wir die Registrierung, Anmeldung und Benutzer-Verwaltungsfunktionen.

## <a name="configure-identity-and-membership"></a>Konfigurieren der Identitäts- und Mitgliedschaftsinformationen

In ASP.NET MVC-Authentifizierung und Identität Features sind konfiguriert mit ASP.NET Identity in *Startup.Auth.cs* und *IdentityConfig.cs*befindet sich in der *App_Start* Ordner. Diese Funktionen werden in ASP.NET Core MVC in konfiguriert *"Startup.cs"*.

Installieren Sie die `Microsoft.AspNetCore.Identity.EntityFrameworkCore` und `Microsoft.AspNetCore.Authentication.Cookies` NuGet-Pakete.

Öffnen Sie dann *"Startup.cs"* und aktualisieren Sie die `Startup.ConfigureServices` zu Entity Framework und Identitätsdienste zu verwendende Methode:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

An diesem Punkt es gibt zwei Typen, die auf die verwiesen wird im obigen Code, die wir aus dem ASP.NET MVC-Projekt noch nicht noch migriert: `ApplicationDbContext` und `ApplicationUser`. Erstellen Sie ein neues *Modelle* Ordner in die ASP.NET Core-Projekt, und fügen Sie zwei Klassen hinzu, die diese Typen. Sehen Sie die ASP.NET MVC-Versionen dieser Klassen in */Models/IdentityModels.cs*, aber wir werden eine Datei pro Klasse im migrierten Projekt verwenden, da dies klarer ist.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFramework;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Das ASP.NET Core MVC-Starter-Web-Projekt beinhaltet keine anpassungsspielraum von Benutzern oder `ApplicationDbContext`. Wenn eine echte Anwendung migrieren möchten, müssen Sie auch alle benutzerdefinierten Eigenschaften und Methoden von Ihrer app Benutzer zu migrieren und `DbContext` Klassen sowie alle anderen Modellklassen Ihrer app verwendet. Beispielsweise wenn Ihre `DbContext` verfügt über eine `DbSet<Album>`, müssen Sie migrieren die `Album` Klasse.

Mit diesen Dateien vorhanden die *"Startup.cs"* Datei kann auf die Aktualisierung der Kompilierung erfolgen die `using` Anweisungen:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Unsere app ist jetzt bereit für die Authentifizierung und Identitätsdienste zu unterstützen. Es muss lediglich diese Funktionen für Benutzer verfügbar gemacht zu haben.

## <a name="migrate-registration-and-login-logic"></a>Migrieren von Logik für Registrierung und Anmeldung

Identitätsdienste für die app konfiguriert und Datenzugriff mit Entity Framework und SQL Server konfiguriert können wir Unterstützung für die Registrierung und Anmeldung für die app hinzuzufügen. Zur Erinnerung: [weiter oben in der Migrationsprozess](xref:migration/mvc#migrate-the-layout-file) wir einen Verweis auf auskommentiert *_LoginPartial* in *"_Layout.cshtml"*. Jetzt ist es Zeit, die mit diesem Code zurückgeben, kommentieren Sie es aus, und fügen in der erforderlichen Controller und Ansichten zur Unterstützung der Anmeldefunktionalität ermöglichen.

Heben Sie die auskommentierung der `@Html.Partial` in Zeile *"_Layout.cshtml"*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Nun, hinzufügen eine neue Razor-Ansicht namens *_LoginPartial* auf die *Views/Shared* Ordner:

Update *_LoginPartial.cshtml* durch den folgenden Code (ersetzen Sie seinen gesamten Inhalt):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

An diesem Punkt sollten Sie die Website in Ihrem Browser zu aktualisieren können.

## <a name="summary"></a>Zusammenfassung

ASP.NET Core umfasst Änderungen an die ASP.NET Identity-Funktionen. In diesem Artikel wurde gezeigt, wie die Funktionen der Authentifizierung und der Verwaltung von ASP.NET Identity zu ASP.NET Core zu migrieren.
