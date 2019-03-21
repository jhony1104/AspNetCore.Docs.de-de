---
title: Anpassung der Identität des in ASP.NET Core
author: ajcvickers
description: Dieser Artikel beschreibt, wie Sie das zugrunde liegende Datenmodell von Entity Framework Core für ASP.NET Core Identity anpassen.
ms.author: avickers
ms.date: 09/24/2018
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 55346c571f180fa17a1108a622d991d15f365bae
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209461"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="69cb3-103">Anpassung der Identität des in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69cb3-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="69cb3-104">Durch [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="69cb3-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="69cb3-105">ASP.NET Core Identity stellt ein Framework zum Verwalten und Speichern von Benutzerkonten in ASP.NET Core-apps bereit.</span><span class="sxs-lookup"><span data-stu-id="69cb3-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="69cb3-106">Identität zu Ihrem Projekt hinzugefügt wird bei der **einzelne Benutzerkonten** als der Authentifizierungsmechanismus ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="69cb3-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="69cb3-107">In der Standardeinstellung Identität nutzt ein Entity Framework (EF) Core-Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="69cb3-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="69cb3-108">Dieser Artikel beschreibt, wie Sie das Identitätsmodell anpassen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="69cb3-109">Identität und EF Core-Migrationen</span><span class="sxs-lookup"><span data-stu-id="69cb3-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="69cb3-110">Unverzichtbare Voraussetzung zur Untersuchung des Modells, es ist sinnvoll, das Verständnis der Funktionsweise der Identität mit [EF Core-Migrationen](/ef/core/managing-schemas/migrations/) erstellen und Aktualisieren einer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="69cb3-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="69cb3-111">Ist auf der obersten Ebene der Prozess:</span><span class="sxs-lookup"><span data-stu-id="69cb3-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="69cb3-112">Definieren oder Aktualisieren einer [Datenmodells in Code](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="69cb3-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="69cb3-113">Fügen Sie eine Migration aus, um dieses Modell in der Änderungen zu übersetzen, die auf die Datenbank angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="69cb3-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="69cb3-114">Überprüfen Sie, dass die Migration ordnungsgemäß Ihre Absichten darstellt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="69cb3-115">Wenden Sie die Migration zum Aktualisieren der Datenbank mit dem Modell synchronisiert werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="69cb3-116">Wiederholen Sie die Schritte 1 bis 4, um die Ergebnisse weiter verfeinern das Modell und die Datenbank synchron halten.</span><span class="sxs-lookup"><span data-stu-id="69cb3-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="69cb3-117">Verwenden Sie eine der folgenden Ansätze, hinzufügen und Anwenden von Migrationen:</span><span class="sxs-lookup"><span data-stu-id="69cb3-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="69cb3-118">Die **-Paket-Manager-Konsole** (PMC) im Fenster, wenn Visual Studio verwenden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="69cb3-119">Weitere Informationen finden Sie unter [EF Core PMC-Tools](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="69cb3-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="69cb3-120">Die .NET Core-CLI, wenn die Befehlszeile verwenden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="69cb3-121">Weitere Informationen finden Sie unter [.NET von EF Core-Befehlszeilentools](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="69cb3-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="69cb3-122">Klicken auf die **Migrationen anwenden** Schaltfläche auf der Fehlerseite, wenn die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="69cb3-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="69cb3-123">ASP.NET Core verfügt über einen Fehler während der Entwicklung-Seite-Handler.</span><span class="sxs-lookup"><span data-stu-id="69cb3-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="69cb3-124">Der Handler kann Migrationen anwenden, wenn die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="69cb3-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="69cb3-125">Für Produktions-apps häufig ist es angebracht, SQL-Skripts über die Migrationen zu generieren und Bereitstellen von Änderungen in der Datenbank als Teil einer gesteuerten Bereitstellung der app und Datenbank.</span><span class="sxs-lookup"><span data-stu-id="69cb3-125">For production apps, it's often more appropriate to generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="69cb3-126">Wenn eine neue app mit Identität erstellt wird, wurden bereits obigen Schritte 1 und 2 abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="69cb3-127">D. h. das ursprüngliche Datenmodell ist bereits vorhanden, und die ursprüngliche Migration wurde dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="69cb3-128">Die ursprüngliche Migration muss immer noch auf die Datenbank angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="69cb3-129">Die ursprüngliche Migration kann über eine der folgenden Methoden angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="69cb3-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="69cb3-130">Führen Sie `Update-Database` in PMC.</span><span class="sxs-lookup"><span data-stu-id="69cb3-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="69cb3-131">Führen Sie `dotnet ef database update` in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="69cb3-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="69cb3-132">Klicken Sie auf die **Migrationen anwenden** Schaltfläche auf der Fehlerseite, wenn die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="69cb3-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="69cb3-133">Wiederholen Sie die vorherigen Schritte aus, wie Änderungen am Modell vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="69cb3-134">Das Identitätsmodell</span><span class="sxs-lookup"><span data-stu-id="69cb3-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="69cb3-135">Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="69cb3-135">Entity types</span></span>

<span data-ttu-id="69cb3-136">Das Identitätsmodell besteht aus den folgenden Entitätstypen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="69cb3-137">Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="69cb3-137">Entity type</span></span>|<span data-ttu-id="69cb3-138">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="69cb3-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="69cb3-139">Stellt den Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="69cb3-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="69cb3-140">Stellt eine Rolle dar.</span><span class="sxs-lookup"><span data-stu-id="69cb3-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="69cb3-141">Stellt einen Anspruch, den ein Benutzer besitzt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="69cb3-142">Stellt einen Authentifizierungstoken für einen Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="69cb3-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="69cb3-143">Ordnet eine Anmeldung ein Benutzers zu.</span><span class="sxs-lookup"><span data-stu-id="69cb3-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="69cb3-144">Stellt einen Anspruch, der für alle Benutzer innerhalb einer Rolle gewährt wird.</span><span class="sxs-lookup"><span data-stu-id="69cb3-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="69cb3-145">Eine verknüpfte Entität, die Benutzer und Rollen zuordnen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="69cb3-146">Typ von entitätsbeziehungen</span><span class="sxs-lookup"><span data-stu-id="69cb3-146">Entity type relationships</span></span>

<span data-ttu-id="69cb3-147">Die [Entitätstypen](#entity-types) sind miteinander verknüpft, auf folgende Weise:</span><span class="sxs-lookup"><span data-stu-id="69cb3-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="69cb3-148">Jede `User` haben viele `UserClaims`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="69cb3-149">Jede `User` haben viele `UserLogins`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="69cb3-150">Jede `User` haben viele `UserTokens`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="69cb3-151">Jede `Role` haben viele verknüpfte `RoleClaims`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="69cb3-152">Jede `User` haben viele verknüpfte `Roles`, und jede `Role` kann viele zugeordnet werden `Users`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="69cb3-153">Dies ist eine m: n Beziehung, die eine Jointabelle in der Datenbank erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="69cb3-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="69cb3-154">Die verknüpften Tabelle wird dargestellt, durch die `UserRole` Entität.</span><span class="sxs-lookup"><span data-stu-id="69cb3-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="69cb3-155">Standardkonfiguration für Modell</span><span class="sxs-lookup"><span data-stu-id="69cb3-155">Default model configuration</span></span>

<span data-ttu-id="69cb3-156">Identity definiert viele *Kontextklassen* , die von erben <xref:Microsoft.EntityFrameworkCore.DbContext> konfigurieren und verwenden das Modell.</span><span class="sxs-lookup"><span data-stu-id="69cb3-156">Identity defines many *context classes* that inherit from <xref:Microsoft.EntityFrameworkCore.DbContext> to configure and use the model.</span></span> <span data-ttu-id="69cb3-157">Diese Konfiguration erfolgt mithilfe der [EF Core Code First Fluent-API](/ef/core/modeling/) in die <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> Methode der Context-Klasse.</span><span class="sxs-lookup"><span data-stu-id="69cb3-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> method of the context class.</span></span> <span data-ttu-id="69cb3-158">Die Standardkonfiguration ist:</span><span class="sxs-lookup"><span data-stu-id="69cb3-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="69cb3-159">Generische Typen des Modells</span><span class="sxs-lookup"><span data-stu-id="69cb3-159">Model generic types</span></span>

<span data-ttu-id="69cb3-160">Identity definiert standardmäßig [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR)-Typen für jeden der Entitätstypen, die oben aufgeführten.</span><span class="sxs-lookup"><span data-stu-id="69cb3-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="69cb3-161">Diese Typen sind alle mit dem Präfix *Identität*:</span><span class="sxs-lookup"><span data-stu-id="69cb3-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="69cb3-162">Anstatt diese Typen direkt verwenden, können die Typen als Basisklassen für die der app-Typen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="69cb3-163">Die `DbContext` von Identität definierte Klassen sind generisch, andere CLR-Typen für eine oder mehrere Entitätstypen im Modell verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="69cb3-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="69cb3-164">Erlauben Sie dieser generischen Typen auch die `User` primären Schlüssel (PK) Datentyp geändert werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="69cb3-165">Bei Verwendung der Identität mit Unterstützung für Rollen, einem <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> Klasse sollte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="69cb3-166">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="69cb3-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="69cb3-167">Es ist auch möglich, in diesem Fall mit Identität ohne Rollen (nur Ansprüche), ein <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1> Klasse sollte verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="69cb3-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="69cb3-168">Anpassen des Modells</span><span class="sxs-lookup"><span data-stu-id="69cb3-168">Customize the model</span></span>

<span data-ttu-id="69cb3-169">Der Ausgangspunkt für die Anpassung des ist von den entsprechenden Kontexttyp abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="69cb3-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="69cb3-170">Finden Sie unter den [generische Typen im Modell](#model-generic-types) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="69cb3-171">Dieser Kontext wird üblicherweise bezeichnet `ApplicationDbContext` und wird vom ASP.NET Core-Vorlagen erstellt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="69cb3-172">Der Kontext wird verwendet, um das Modell auf zwei Arten konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="69cb3-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="69cb3-173">Entität "und" Key-Typen für die generischen Typparameter angeben.</span><span class="sxs-lookup"><span data-stu-id="69cb3-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="69cb3-174">Überschreiben von `OnModelCreating` so ändern Sie die Zuordnung dieser Typen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="69cb3-175">Beim Überschreiben von `OnModelCreating`, `base.OnModelCreating` zuerst aufgerufen werden soll; die überschreibende Konfiguration als Nächstes aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="69cb3-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="69cb3-176">EF Core hat normalerweise eine Last-1-Wins-Richtlinie für die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="69cb3-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="69cb3-177">Z. B. wenn die `ToTable` Methode für einen Entitätstyp mit einem Tabellennamen zuerst aufgerufen wird, und klicken Sie dann erneut später mit einem anderen Tabellennamen an, der Tabellennamen im zweiten Aufruf wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="69cb3-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="69cb3-178">Benutzerdefinierte Daten</span><span class="sxs-lookup"><span data-stu-id="69cb3-178">Custom user data</span></span>

<span data-ttu-id="69cb3-179">[Benutzerdefinierte Benutzerdaten](xref:security/authentication/add-user-data) wird unterstützt durch Erben von `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="69cb3-180">Es ist üblich, nennen Sie diese Art `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="69cb3-181">Verwenden der `ApplicationUser` Typ als generisches Argument für den Kontext:</span><span class="sxs-lookup"><span data-stu-id="69cb3-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

<span data-ttu-id="69cb3-182">Besteht keine Notwendigkeit zum Überschreiben `OnModelCreating` in die `ApplicationDbContext` Klasse.</span><span class="sxs-lookup"><span data-stu-id="69cb3-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="69cb3-183">EF Core ordnet die `CustomTag` Eigenschaft gemäß der Konvention.</span><span class="sxs-lookup"><span data-stu-id="69cb3-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="69cb3-184">Allerdings muss die Datenbank aktualisiert werden, zum Erstellen eines neuen `CustomTag` Spalte.</span><span class="sxs-lookup"><span data-stu-id="69cb3-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="69cb3-185">Zum Erstellen der Spalte, eine Migration hinzufügen und aktualisieren Sie die Datenbank an, wie in beschrieben [Identitäts- und EF Core-Migrationen](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="69cb3-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="69cb3-186">Update `Startup.ConfigureServices` für die Verwendung der neuen `ApplicationUser` Klasse:</span><span class="sxs-lookup"><span data-stu-id="69cb3-186">Update `Startup.ConfigureServices` to use the new `ApplicationUser` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="69cb3-187">In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-187">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="69cb3-188">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-188">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="69cb3-189">Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-189">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="69cb3-190">Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-190">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="69cb3-191">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="69cb3-191">For more information, see:</span></span>

* [<span data-ttu-id="69cb3-192">Gerüst für Identität</span><span class="sxs-lookup"><span data-stu-id="69cb3-192">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="69cb3-193">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten-Identität</span><span class="sxs-lookup"><span data-stu-id="69cb3-193">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
        .AddDefaultTokenProviders();
```

::: moniker-end

### <a name="change-the-primary-key-type"></a><span data-ttu-id="69cb3-194">Ändern Sie den Typ des primären Schlüssels</span><span class="sxs-lookup"><span data-stu-id="69cb3-194">Change the primary key type</span></span>

<span data-ttu-id="69cb3-195">Eine Änderung an der PS-Spalte, Datentyp, nach der Erstellung der Datenbank ist auf vielen Datenbanksystemen problematisch.</span><span class="sxs-lookup"><span data-stu-id="69cb3-195">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="69cb3-196">Änderungen der Primärschlüssel in der Regel umfasst das Löschen und Neuerstellen der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="69cb3-196">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="69cb3-197">Aus diesem Grund sollten Schlüsseltypen in der ursprünglichen Migration angegeben werden, wenn die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="69cb3-197">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="69cb3-198">Um den Primärschlüssel ändern, gehen Sie wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="69cb3-198">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="69cb3-199">Wenn es sich bei der Erstellung der Datenbank führen Sie vor der Umstellung PS `Drop-Database` (PMC) oder `dotnet ef database drop` (.NET Core CLI), um ihn zu löschen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-199">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="69cb3-200">Entfernen Sie nach der Bestätigung der Datenbank gelöscht, die ursprüngliche Migration mit `Remove-Migration` (PMC) oder `dotnet ef migrations remove` (.NET Core-CLI).</span><span class="sxs-lookup"><span data-stu-id="69cb3-200">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="69cb3-201">Update der `ApplicationDbContext` Klasse abgeleitet <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-201">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>.</span></span> <span data-ttu-id="69cb3-202">Geben Sie den neuen Schlüssel für `TKey`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-202">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="69cb3-203">Beispielsweise verwendet ein `Guid` Schlüsseltyp:</span><span class="sxs-lookup"><span data-stu-id="69cb3-203">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="69cb3-204">Im obigen Code, die generischen Klassen <xref:Microsoft.AspNetCore.Identity.IdentityUser`1> und <xref:Microsoft.AspNetCore.Identity.IdentityRole`1> muss angegeben werden, um den neuen Schlüssel verwenden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-204">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="69cb3-205">Im obigen Code, die generischen Klassen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1> und <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1> muss angegeben werden, um den neuen Schlüssel verwenden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="69cb3-206">`Startup.ConfigureServices` müssen aktualisiert werden, um den generischen Benutzer verwenden:</span><span class="sxs-lookup"><span data-stu-id="69cb3-206">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="69cb3-207">Wenn eine benutzerdefinierte `ApplicationUser` Klasse verwendet wird, aktualisieren Sie die Klasse zu erben `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-207">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="69cb3-208">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="69cb3-208">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="69cb3-209">Update `ApplicationDbContext` auf die benutzerdefinierte `ApplicationUser` Klasse:</span><span class="sxs-lookup"><span data-stu-id="69cb3-209">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="69cb3-210">Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-210">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="69cb3-211">Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der <xref:Microsoft.EntityFrameworkCore.DbContext> Objekt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-211">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="69cb3-212">In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-212">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="69cb3-213">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-213">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="69cb3-214">Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-214">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="69cb3-215">Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-215">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="69cb3-216">Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der <xref:Microsoft.EntityFrameworkCore.DbContext> Objekt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-216">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="69cb3-217">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert eine `TKey` Typ, der angibt, die Primärschlüssel-Datentyp.</span><span class="sxs-lookup"><span data-stu-id="69cb3-217">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="69cb3-218">Wenn eine benutzerdefinierte `ApplicationRole` Klasse verwendet wird, aktualisieren Sie die Klasse zu erben `IdentityRole<TKey>`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-218">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="69cb3-219">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="69cb3-219">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="69cb3-220">Update `ApplicationDbContext` auf die benutzerdefinierte `ApplicationRole` Klasse.</span><span class="sxs-lookup"><span data-stu-id="69cb3-220">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="69cb3-221">Die folgende Klasse verweist beispielsweise eine benutzerdefinierte `ApplicationUser` und eine benutzerdefinierte `ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-221">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="69cb3-222">Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-222">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="69cb3-223">Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der <xref:Microsoft.EntityFrameworkCore.DbContext> Objekt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-223">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="69cb3-224">In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-224">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="69cb3-225">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-225">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="69cb3-226">Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-226">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="69cb3-227">Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-227">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="69cb3-228">Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-228">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="69cb3-229">Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der <xref:Microsoft.EntityFrameworkCore.DbContext> Objekt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-229">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="69cb3-230">Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-230">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="69cb3-231">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert eine `TKey` Typ, der angibt, die Primärschlüssel-Datentyp.</span><span class="sxs-lookup"><span data-stu-id="69cb3-231">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="69cb3-232">Hinzufügen von Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="69cb3-232">Add navigation properties</span></span>

<span data-ttu-id="69cb3-233">Der konfigurationsänderung Modells für die Beziehung zwischen kann viel schwieriger als das andere Änderungen sein.</span><span class="sxs-lookup"><span data-stu-id="69cb3-233">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="69cb3-234">Muss darauf geachtet werden, Ersetzen der vorhandenen Beziehungen, anstatt neu erstellen, zusätzliche Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-234">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="69cb3-235">Insbesondere muss die geänderte Beziehung, über die gleichen Fremdschlüssel (FS) Schlüsseleigenschaft als die vorhandene Beziehung angeben.</span><span class="sxs-lookup"><span data-stu-id="69cb3-235">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="69cb3-236">Z. B. die Beziehung zwischen `Users` und `UserClaims` ist standardmäßig wie folgt angegeben:</span><span class="sxs-lookup"><span data-stu-id="69cb3-236">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="69cb3-237">Fremdschlüssel für diese Beziehung angegeben ist, als die `UserClaim.UserId` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="69cb3-237">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="69cb3-238">`HasMany` und `WithOne` ohne Argumente zum Erstellen der Beziehung ohne Navigationseigenschaften aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-238">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="69cb3-239">Fügen Sie eine Navigationseigenschaft für `ApplicationUser` , mit der verknüpften `UserClaims` des Benutzers verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="69cb3-239">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="69cb3-240">Die `TKey` für `IdentityUserClaim<TKey>` für Primärschlüssel von Benutzern angegebenen Typ entspricht.</span><span class="sxs-lookup"><span data-stu-id="69cb3-240">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="69cb3-241">In diesem Fall `TKey` ist `string` da die Standardwerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-241">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="69cb3-242">Sie verfügt über **nicht** der PS-Typ für die `UserClaim` Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="69cb3-242">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="69cb3-243">Nun, dass die Navigationseigenschaft vorhanden ist, müssen Sie konfigurieren `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-243">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="69cb3-244">Beachten Sie, dass die Vertrauensstellung konfiguriert ist, genau wie zuvor nur mit einer Navigationseigenschaft, die im Aufruf angegeben wurde, `HasMany`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-244">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="69cb3-245">Die Navigationseigenschaften sind nur in der EF-Modell, nicht in der Datenbank vorhanden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-245">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="69cb3-246">Da der Fremdschlüssel für die Beziehung nicht geändert hat, erfordern nicht diese Art von modelländerung die Datenbank aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-246">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="69cb3-247">Dies kann durch Hinzufügen einer Migrations nach der Änderung überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-247">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="69cb3-248">Die `Up` und `Down` Methoden sind leer.</span><span class="sxs-lookup"><span data-stu-id="69cb3-248">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="69cb3-249">Fügen Sie alle Navigationseigenschaften des Benutzers hinzu.</span><span class="sxs-lookup"><span data-stu-id="69cb3-249">Add all User navigation properties</span></span>

<span data-ttu-id="69cb3-250">Im folgenden Beispiel wird im oben stehenden Abschnitt als Leitfaden verwenden, konfiguriert die unidirektionale Navigationseigenschaften für alle Beziehungen für Benutzer:</span><span class="sxs-lookup"><span data-stu-id="69cb3-250">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="69cb3-251">Navigationseigenschaften für Benutzer und Rollen hinzufügen</span><span class="sxs-lookup"><span data-stu-id="69cb3-251">Add User and Role navigation properties</span></span>

<span data-ttu-id="69cb3-252">Im oben stehenden Abschnitt als Leitfaden verwenden, wird im folgende Beispiel Navigationseigenschaften für alle Beziehungen auf Benutzer und die Rolle konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="69cb3-252">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="69cb3-253">Notizen:</span><span class="sxs-lookup"><span data-stu-id="69cb3-253">Notes:</span></span>

* <span data-ttu-id="69cb3-254">Dieses Beispiel enthält auch die `UserRole` Joinentität, die benötigt wird, um die m: n Beziehung von Benutzern zu Rollen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="69cb3-254">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="69cb3-255">Denken Sie daran, die Typen von Navigationseigenschaften entsprechend ändern `ApplicationXxx` Typen verwendet werden nun anstelle von `IdentityXxx` Typen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-255">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="69cb3-256">Denken Sie daran, verwenden Sie die `ApplicationXxx` in der allgemeinen `ApplicationContext` Definition.</span><span class="sxs-lookup"><span data-stu-id="69cb3-256">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="69cb3-257">Alle Navigationseigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="69cb3-257">Add all navigation properties</span></span>

<span data-ttu-id="69cb3-258">Im oben stehenden Abschnitt als Leitfaden verwenden, wird im folgende Beispiel Navigationseigenschaften für alle Beziehungen für alle Entitätstypen konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="69cb3-258">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="69cb3-259">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="69cb3-259">Use composite keys</span></span>

<span data-ttu-id="69cb3-260">In den vorherigen Abschnitten wird gezeigt, ändern den Typ des Schlüssels im Identitätsmodell verwendet.</span><span class="sxs-lookup"><span data-stu-id="69cb3-260">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="69cb3-261">Ändern das Identitätsmodell-Schlüssel zur Verwendung von zusammengesetzten Schlüsseln wird nicht unterstützt oder empfohlen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-261">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="69cb3-262">Verwenden einen zusammengesetzten Schlüssel, mit der Identität umfasst eine Änderung, wie der Identity Manager-Code mit dem Modell interagiert.</span><span class="sxs-lookup"><span data-stu-id="69cb3-262">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="69cb3-263">Diese Anpassung ist über den Rahmen dieses Dokuments hinaus.</span><span class="sxs-lookup"><span data-stu-id="69cb3-263">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="69cb3-264">Die Datenbanktabelle oder-Spalte Namen ändern und facets</span><span class="sxs-lookup"><span data-stu-id="69cb3-264">Change table/column names and facets</span></span>

<span data-ttu-id="69cb3-265">Um die Namen von Tabellen und Spalten zu ändern, rufen `base.OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="69cb3-265">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="69cb3-266">Fügen Sie dann die Konfiguration, um die Standardwerte außer Kraft setzen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-266">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="69cb3-267">Um beispielsweise den Namen aller Tabellen für die Identität ändern:</span><span class="sxs-lookup"><span data-stu-id="69cb3-267">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="69cb3-268">Diese Beispiele verwenden die Identity-Standardtypen.</span><span class="sxs-lookup"><span data-stu-id="69cb3-268">These examples use the default Identity types.</span></span> <span data-ttu-id="69cb3-269">Wenn einen app-Typ verwenden, z. B. `ApplicationUser`, konfigurieren Sie diesen Typ anstelle der Standardtyp.</span><span class="sxs-lookup"><span data-stu-id="69cb3-269">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="69cb3-270">Im folgende Beispiel ändert einige Spaltennamen:</span><span class="sxs-lookup"><span data-stu-id="69cb3-270">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="69cb3-271">Einige Typen von Datenbankspalten können konfiguriert werden, mit bestimmten *Facets* (z. B. die maximale `string` zulässige Länge).</span><span class="sxs-lookup"><span data-stu-id="69cb3-271">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="69cb3-272">Im folgenden Beispiel wird die maximale Länge der Spalte für mehrere `string` Eigenschaften im Modell:</span><span class="sxs-lookup"><span data-stu-id="69cb3-272">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="69cb3-273">Ordnen Sie zu einem anderen Schema zu</span><span class="sxs-lookup"><span data-stu-id="69cb3-273">Map to a different schema</span></span>

<span data-ttu-id="69cb3-274">Schemas können über Datenbankanbieter unterschiedlich Verhalten.</span><span class="sxs-lookup"><span data-stu-id="69cb3-274">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="69cb3-275">Für SQL Server, der Standardwert ist die Erstellung von allen Tabellen in der *Dbo* Schema.</span><span class="sxs-lookup"><span data-stu-id="69cb3-275">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="69cb3-276">Die Tabellen können in einem anderen Schema erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="69cb3-276">The tables can be created in a different schema.</span></span> <span data-ttu-id="69cb3-277">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="69cb3-277">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="69cb3-278">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="69cb3-278">Lazy loading</span></span>

<span data-ttu-id="69cb3-279">In diesem Abschnitt wird die Unterstützung von lazy Loading-Proxys im Identitätsmodell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-279">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="69cb3-280">Lazy-Loading ist nützlich, da die Navigationseigenschaften verwendet werden, ohne zuerst um sicherzustellen, dass sie geladen werden können.</span><span class="sxs-lookup"><span data-stu-id="69cb3-280">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="69cb3-281">Entitätstypen können erfolgen, für die geeignet ist lazy Loading auf verschiedene Weise wie in beschrieben die [EF Core-Dokumentation](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="69cb3-281">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="69cb3-282">Der Einfachheit halber verwenden Sie lazy Loading-Proxys erfordert:</span><span class="sxs-lookup"><span data-stu-id="69cb3-282">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="69cb3-283">Installation von der [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) Paket.</span><span class="sxs-lookup"><span data-stu-id="69cb3-283">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="69cb3-284">Ein Aufruf von <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> in <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.</span><span class="sxs-lookup"><span data-stu-id="69cb3-284">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.</span></span>
* <span data-ttu-id="69cb3-285">Öffentliche Entitätstypen mit `public virtual` Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="69cb3-285">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="69cb3-286">Das folgende Beispiel veranschaulicht den Aufruf `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="69cb3-286">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="69cb3-287">Finden Sie in den vorherigen Beispielen Anleitungen dazu, die Entitätstypen Navigationseigenschaften hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="69cb3-287">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69cb3-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="69cb3-288">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
