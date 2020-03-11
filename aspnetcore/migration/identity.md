---
title: Migrieren von Authentifizierung und Identität zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie die Authentifizierung und Identität von einem ASP.NET MVC-Projekt zu einem ASP.net Core MVC-Projekt migrieren.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653011"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="22d29-103">Migrieren von Authentifizierung und Identität zu ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="22d29-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="22d29-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="22d29-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="22d29-105">Im vorherigen Artikel haben wir die [Konfiguration von einem ASP.NET MVC-Projekt zu ASP.net Core MVC migriert](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="22d29-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="22d29-106">In diesem Artikel migrieren wir die Funktionen "Registrierung", "Anmeldung" und "Benutzerverwaltung".</span><span class="sxs-lookup"><span data-stu-id="22d29-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="22d29-107">Identität und Mitgliedschaft konfigurieren</span><span class="sxs-lookup"><span data-stu-id="22d29-107">Configure Identity and Membership</span></span>

<span data-ttu-id="22d29-108">In ASP.NET MVC werden Authentifizierungs-und Identitäts Funktionen mithilfe von ASP.net Identity in *Startup.auth.cs* und *IdentityConfig.cs*konfiguriert, die sich im Ordner *App_Start* befinden.</span><span class="sxs-lookup"><span data-stu-id="22d29-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="22d29-109">In ASP.net Core MVC sind diese Funktionen in *Startup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="22d29-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="22d29-110">Installieren Sie die `Microsoft.AspNetCore.Identity.EntityFrameworkCore` und `Microsoft.AspNetCore.Authentication.Cookies` nuget-Pakete.</span><span class="sxs-lookup"><span data-stu-id="22d29-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="22d29-111">Öffnen Sie dann *Startup.cs* , und aktualisieren Sie die `Startup.ConfigureServices`-Methode, um Entity Framework und Identitäts Dienste zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="22d29-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="22d29-112">An diesem Punkt gibt es zwei Typen, auf die im obigen Code verwiesen wird, die noch nicht aus dem ASP.NET MVC-Projekt migriert wurden: `ApplicationDbContext` und `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="22d29-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="22d29-113">Erstellen Sie im ASP.net Core-Projekt einen neuen *Modell* Ordner, und fügen Sie ihm zwei Klassen entsprechend diesen Typen hinzu.</span><span class="sxs-lookup"><span data-stu-id="22d29-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="22d29-114">Sie finden die ASP.NET MVC-Versionen dieser Klassen in */Models/IdentityModels.cs*, aber wir verwenden eine Datei pro Klasse im migrierten Projekt, da dies noch deutlicher ist.</span><span class="sxs-lookup"><span data-stu-id="22d29-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="22d29-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="22d29-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="22d29-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="22d29-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
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

<span data-ttu-id="22d29-117">Das ASP.net Core MVC-Starter-Webprojekt enthält keine große Anpassung der Benutzer oder des `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="22d29-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="22d29-118">Beim Migrieren einer echten App müssen Sie auch alle benutzerdefinierten Eigenschaften und Methoden der Benutzer-und `DbContext` Klassen der APP sowie alle anderen Modellklassen migrieren, die Ihre APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="22d29-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="22d29-119">Wenn Ihr `DbContext` beispielsweise über eine `DbSet<Album>`verfügt, müssen Sie die `Album`-Klasse migrieren.</span><span class="sxs-lookup"><span data-stu-id="22d29-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="22d29-120">Wenn diese Dateien vorhanden sind, kann die *Startup.cs* -Datei für die Kompilierung erstellt werden, indem die `using`-Anweisungen aktualisiert werden:</span><span class="sxs-lookup"><span data-stu-id="22d29-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="22d29-121">Unsere APP ist jetzt bereit zur Unterstützung von Authentifizierungs-und Identitäts Diensten.</span><span class="sxs-lookup"><span data-stu-id="22d29-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="22d29-122">Diese Features müssen nur Benutzern zur Verfügung gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="22d29-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="22d29-123">Migrieren von Registrierungs-und Anmelde Logik</span><span class="sxs-lookup"><span data-stu-id="22d29-123">Migrate registration and login logic</span></span>

<span data-ttu-id="22d29-124">Wenn Identitäts Dienste für die APP und den Datenzugriff konfiguriert sind, die mit Entity Framework und SQL Server konfiguriert wurden, können wir die Unterstützung für die Registrierung und Anmeldung bei der APP hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="22d29-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="22d29-125">Beachten Sie, dass wir [zuvor im Migrationsprozess](xref:migration/mvc#migrate-the-layout-file) einen Verweis auf *_LoginPartial* in *_Layout. cshtml*auskommentiert haben.</span><span class="sxs-lookup"><span data-stu-id="22d29-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="22d29-126">Nun ist es an der Zeit, zu diesem Code zurückzukehren, ihn zu entfernen und die erforderlichen Controller und Sichten hinzuzufügen, um die Anmelde Funktionalität zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="22d29-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="22d29-127">Entfernen Sie die Auskommentierung der `@Html.Partial` Zeile in *_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="22d29-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="22d29-128">Fügen Sie nun der Ansicht/dem frei *gegebenen* Ordner eine neue Razor-Ansicht mit dem Namen *_LoginPartial* hinzu:</span><span class="sxs-lookup"><span data-stu-id="22d29-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="22d29-129">Aktualisieren Sie *_LoginPartial. cshtml* mit folgendem Code (ersetzen Sie den gesamten Inhalt):</span><span class="sxs-lookup"><span data-stu-id="22d29-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="22d29-130">An diesem Punkt sollten Sie in der Lage sein, die Website in Ihrem Browser zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="22d29-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="22d29-131">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="22d29-131">Summary</span></span>

<span data-ttu-id="22d29-132">ASP.net Core führt Änderungen an den ASP.net Identity Features ein.</span><span class="sxs-lookup"><span data-stu-id="22d29-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="22d29-133">In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierungs-und Benutzer Verwaltungsfunktionen von ASP.net Identity zu ASP.net Core migrieren.</span><span class="sxs-lookup"><span data-stu-id="22d29-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
