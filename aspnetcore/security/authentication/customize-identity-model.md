---
title: Anpassung der Identität des in ASP.NET Core
author: ajcvickers
description: Dieser Artikel beschreibt, wie Sie das zugrunde liegende Datenmodell von Entity Framework Core für ASP.NET Core Identity anpassen.
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500475"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Anpassung der Identität des in ASP.NET Core

Durch [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity stellt ein Framework zum Verwalten und Speichern von Benutzerkonten in ASP.NET Core-apps bereit. Identität zu Ihrem Projekt hinzugefügt wird bei der **einzelne Benutzerkonten** als der Authentifizierungsmechanismus ausgewählt ist. In der Standardeinstellung Identität nutzt ein Entity Framework (EF) Core-Datenmodells. Dieser Artikel beschreibt, wie Sie das Identitätsmodell anpassen.

## <a name="identity-and-ef-core-migrations"></a>Identität und EF Core-Migrationen

Unverzichtbare Voraussetzung zur Untersuchung des Modells, es ist sinnvoll, das Verständnis der Funktionsweise der Identität mit [EF Core-Migrationen](/ef/core/managing-schemas/migrations/) erstellen und Aktualisieren einer Datenbank. Ist auf der obersten Ebene der Prozess:

1. Definieren oder Aktualisieren einer [Datenmodells in Code](/ef/core/modeling/).
1. Fügen Sie eine Migration aus, um dieses Modell in der Änderungen zu übersetzen, die auf die Datenbank angewendet werden können.
1. Überprüfen Sie, dass die Migration ordnungsgemäß Ihre Absichten darstellt.
1. Wenden Sie die Migration zum Aktualisieren der Datenbank mit dem Modell synchronisiert werden.
1. Wiederholen Sie die Schritte 1 bis 4, um die Ergebnisse weiter verfeinern das Modell und die Datenbank synchron halten.

Verwenden Sie eine der folgenden Ansätze, hinzufügen und Anwenden von Migrationen:

* Die **-Paket-Manager-Konsole** (PMC) im Fenster, wenn Visual Studio verwenden. Weitere Informationen finden Sie unter [EF Core PMC-Tools](/ef/core/miscellaneous/cli/powershell).
* Die .NET Core-CLI, wenn die Befehlszeile verwenden. Weitere Informationen finden Sie unter [.NET von EF Core-Befehlszeilentools](/ef/core/miscellaneous/cli/dotnet).
* Klicken auf die **Migrationen anwenden** Schaltfläche auf der Fehlerseite, wenn die app ausgeführt wird.

ASP.NET Core verfügt über einen Fehler während der Entwicklung-Seite-Handler. Der Handler kann Migrationen anwenden, wenn die app ausgeführt wird. Produktions-apps in der Regel von Migrationen zu SQL-Skripts generieren und Bereitstellen von Änderungen in der Datenbank als Teil einer gesteuerten app und die Bereitstellung der Datenbank.

Wenn eine neue app mit Identität erstellt wird, wurden bereits obigen Schritte 1 und 2 abgeschlossen. D. h. das ursprüngliche Datenmodell ist bereits vorhanden, und die ursprüngliche Migration wurde dem Projekt hinzugefügt. Die ursprüngliche Migration muss immer noch auf die Datenbank angewendet werden. Die ursprüngliche Migration kann über eine der folgenden Methoden angewendet werden:

* Führen Sie `Update-Database` in PMC.
* Führen Sie `dotnet ef database update` in einer Befehlsshell.
* Klicken Sie auf die **Migrationen anwenden** Schaltfläche auf der Fehlerseite, wenn die app ausgeführt wird.

Wiederholen Sie die vorherigen Schritte aus, wie Änderungen am Modell vorgenommen werden.

## <a name="the-identity-model"></a>Das Identitätsmodell

### <a name="entity-types"></a>Entitätstypen

Das Identitätsmodell besteht aus den folgenden Entitätstypen.

|Entitätstyp|Beschreibung                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Stellt den Benutzer dar.                                         |
|`Role`     |Stellt eine Rolle dar.                                           |
|`UserClaim`|Stellt einen Anspruch, den ein Benutzer besitzt.                    |
|`UserToken`|Stellt einen Authentifizierungstoken für einen Benutzer dar.               |
|`UserLogin`|Ordnet eine Anmeldung ein Benutzers zu.                              |
|`RoleClaim`|Stellt einen Anspruch, der für alle Benutzer innerhalb einer Rolle gewährt wird.|
|`UserRole` |Eine verknüpfte Entität, die Benutzer und Rollen zuordnen.               |

### <a name="entity-type-relationships"></a>Typ von entitätsbeziehungen

Die [Entitätstypen](#entity-types) sind miteinander verknüpft, auf folgende Weise:

* Jede `User` haben viele `UserClaims`.
* Jede `User` haben viele `UserLogins`.
* Jede `User` haben viele `UserTokens`.
* Jede `Role` haben viele verknüpfte `RoleClaims`.
* Jede `User` haben viele verknüpfte `Roles`, und jede `Role` kann viele zugeordnet werden `Users`. Dies ist eine m: n Beziehung, die eine Jointabelle in der Datenbank erforderlich sind. Die verknüpften Tabelle wird dargestellt, durch die `UserRole` Entität.

### <a name="default-model-configuration"></a>Standardkonfiguration für Modell

Identity definiert viele *Kontextklassen* , die von erben ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) konfigurieren und verwenden das Modell. Diese Konfiguration erfolgt mithilfe der [EF Core Code First Fluent-API](/ef/core/modeling/) in die ["onmodelcreating"](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) Methode der Context-Klasse. Die Standardkonfiguration ist:

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

### <a name="model-generic-types"></a>Generische Typen des Modells

Identity definiert standardmäßig [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR)-Typen für jeden der Entitätstypen, die oben aufgeführten. Diese Typen sind alle mit dem Präfix *Identität*:

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Anstatt diese Typen direkt verwenden, können die Typen als Basisklassen für die der app-Typen verwendet werden. Die `DbContext` von Identität definierte Klassen sind generisch, andere CLR-Typen für eine oder mehrere Entitätstypen im Modell verwendet werden können. Erlauben Sie dieser generischen Typen auch die `User` primären Schlüssel (PK) Datentyp geändert werden.

Bei Verwendung der Identität mit Unterstützung für Rollen, einem <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> Klasse sollte verwendet werden. Zum Beispiel:

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

Es ist auch möglich, in diesem Fall mit Identität ohne Rollen (nur Ansprüche), ein <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> Klasse sollte verwendet werden:

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

## <a name="customize-the-model"></a>Anpassen des Modells

Der Ausgangspunkt für die Anpassung des ist von den entsprechenden Kontexttyp abgeleitet. Finden Sie unter den [generische Typen im Modell](#model-generic-types) Abschnitt. Dieser Kontext wird üblicherweise bezeichnet `ApplicationDbContext` und wird vom ASP.NET Core-Vorlagen erstellt.

Der Kontext wird verwendet, um das Modell auf zwei Arten konfigurieren:

* Entität "und" Key-Typen für die generischen Typparameter angeben.
* Überschreiben von `OnModelCreating` so ändern Sie die Zuordnung dieser Typen.

Beim Überschreiben von `OnModelCreating`, `base.OnModelCreating` zuerst aufgerufen werden soll; die überschreibende Konfiguration als Nächstes aufgerufen werden soll. EF Core hat normalerweise eine Last-1-Wins-Richtlinie für die Konfiguration. Z. B. wenn die `ToTable` Methode für einen Entitätstyp mit einem Tabellennamen zuerst aufgerufen wird, und klicken Sie dann erneut später mit einem anderen Tabellennamen an, der Tabellennamen im zweiten Aufruf wird verwendet.

### <a name="custom-user-data"></a>Benutzerdefinierte Daten

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Benutzerdefinierte Benutzerdaten](xref:security/authentication/add-user-data) wird unterstützt durch Erben von `IdentityUser`. Es ist üblich, nennen Sie diese Art `ApplicationUser`:

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Verwenden der `ApplicationUser` Typ als generisches Argument für den Kontext:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

Besteht keine Notwendigkeit zum Überschreiben `OnModelCreating` in die `ApplicationDbContext` Klasse. EF Core ordnet die `CustomTag` Eigenschaft gemäß der Konvention. Allerdings muss die Datenbank aktualisiert werden, zum Erstellen eines neuen `CustomTag` Spalte. Zum Erstellen der Spalte, eine Migration hinzufügen und aktualisieren Sie die Datenbank an, wie in beschrieben [Identitäts- und EF Core-Migrationen](#identity-and-ef-core-migrations).

Update *Pages/Shared/_LoginPartial.cshtml* , und Ersetzen Sie `IdentityUser` mit `ApplicationUser`:

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Update *Areas/Identity/IdentityHostingStartup.cs* oder `Startup.ConfigureServices` , und Ersetzen Sie `IdentityUser` mit `ApplicationUser`.

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`. Weitere Informationen finden Sie unter:

* [Gerüst für Identität](xref:security/authentication/scaffold-identity)
* [Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten-Identität](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Ändern Sie den Typ des primären Schlüssels

Eine Änderung an der PS-Spalte, Datentyp, nach der Erstellung der Datenbank ist auf vielen Datenbanksystemen problematisch. Änderungen der Primärschlüssel in der Regel umfasst das Löschen und Neuerstellen der Tabelle. Aus diesem Grund sollten Schlüsseltypen in der ursprünglichen Migration angegeben werden, wenn die Datenbank erstellt wird.

Um den Primärschlüssel ändern, gehen Sie wie folgt vor:

1. Wenn es sich bei der Erstellung der Datenbank führen Sie vor der Umstellung PS `Drop-Database` (PMC) oder `dotnet ef database drop` (.NET Core CLI), um ihn zu löschen.
2. Entfernen Sie nach der Bestätigung der Datenbank gelöscht, die ursprüngliche Migration mit `Remove-Migration` (PMC) oder `dotnet ef migrations remove` (.NET Core-CLI).
3. Update der `ApplicationDbContext` Klasse abgeleitet <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>. Geben Sie den neuen Schlüssel für `TKey`. Beispielsweise verwendet ein `Guid` Schlüsseltyp:

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

    Im obigen Code, die generischen Klassen <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> muss angegeben werden, um den neuen Schlüssel verwenden.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    Im obigen Code, die generischen Klassen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> muss angegeben werden, um den neuen Schlüssel verwenden.

    ::: moniker-end

    `Startup.ConfigureServices` müssen aktualisiert werden, um den generischen Benutzer verwenden:

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

4. Wenn eine benutzerdefinierte `ApplicationUser` Klasse verwendet wird, aktualisieren Sie die Klasse zu erben `IdentityUser`. Zum Beispiel:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Update `ApplicationDbContext` auf die benutzerdefinierte `ApplicationUser` Klasse:

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

    Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) Objekt.

    In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) Objekt.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert eine `TKey` Typ, der angibt, die Primärschlüssel-Datentyp.

    ::: moniker-end

5. Wenn eine benutzerdefinierte `ApplicationRole` Klasse verwendet wird, aktualisieren Sie die Klasse zu erben `IdentityRole<TKey>`. Zum Beispiel:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Update `ApplicationDbContext` auf die benutzerdefinierte `ApplicationRole` Klasse. Die folgende Klasse verweist beispielsweise eine benutzerdefinierte `ApplicationUser` und eine benutzerdefinierte `ApplicationRole`:

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) Objekt.

    In ASP.NET Core 2.1 oder höher, wird die Identität als eine Razor-Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Der vorangehende Code erfordert daher einen Aufruf von <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Wenn der gerüstbauer Identität verwendet wurde, Identity-Dateien zum Projekt hinzufügen, entfernen Sie den Aufruf `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Der Primärschlüssel-Datentyp abgeleitet wird, durch die Analyse der ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) Objekt.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren die benutzerdefinierten Datenbank Context-Klasse, beim Hinzufügen der Identitätsdienst in `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert eine `TKey` Typ, der angibt, die Primärschlüssel-Datentyp.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Hinzufügen von Navigationseigenschaften

Der konfigurationsänderung Modells für die Beziehung zwischen kann viel schwieriger als das andere Änderungen sein. Muss darauf geachtet werden, Ersetzen der vorhandenen Beziehungen, anstatt neu erstellen, zusätzliche Beziehungen. Insbesondere muss die geänderte Beziehung, über die gleichen Fremdschlüssel (FS) Schlüsseleigenschaft als die vorhandene Beziehung angeben. Z. B. die Beziehung zwischen `Users` und `UserClaims` ist standardmäßig wie folgt angegeben:

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

Fremdschlüssel für diese Beziehung angegeben ist, als die `UserClaim.UserId` Eigenschaft. `HasMany` und `WithOne` ohne Argumente zum Erstellen der Beziehung ohne Navigationseigenschaften aufgerufen werden.

Fügen Sie eine Navigationseigenschaft für `ApplicationUser` , mit der verknüpften `UserClaims` des Benutzers verwiesen werden:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

Die `TKey` für `IdentityUserClaim<TKey>` für Primärschlüssel von Benutzern angegebenen Typ entspricht. In diesem Fall `TKey` ist `string` da die Standardwerte verwendet werden. Sie verfügt über **nicht** der PS-Typ für die `UserClaim` Entitätstyp.

Nun, dass die Navigationseigenschaft vorhanden ist, müssen Sie konfigurieren `OnModelCreating`:

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

Beachten Sie, dass die Vertrauensstellung konfiguriert ist, genau wie zuvor nur mit einer Navigationseigenschaft, die im Aufruf angegeben wurde, `HasMany`.

Die Navigationseigenschaften sind nur in der EF-Modell, nicht in der Datenbank vorhanden. Da der Fremdschlüssel für die Beziehung nicht geändert hat, erfordern nicht diese Art von modelländerung die Datenbank aktualisiert werden. Dies kann durch Hinzufügen einer Migrations nach der Änderung überprüft werden. Die `Up` und `Down` Methoden sind leer.

### <a name="add-all-user-navigation-properties"></a>Fügen Sie alle Navigationseigenschaften des Benutzers hinzu.

Im folgenden Beispiel wird im oben stehenden Abschnitt als Leitfaden verwenden, konfiguriert die unidirektionale Navigationseigenschaften für alle Beziehungen für Benutzer:

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

### <a name="add-user-and-role-navigation-properties"></a>Navigationseigenschaften für Benutzer und Rollen hinzufügen

Im oben stehenden Abschnitt als Leitfaden verwenden, wird im folgende Beispiel Navigationseigenschaften für alle Beziehungen auf Benutzer und die Rolle konfiguriert:

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

Notizen:

* Dieses Beispiel enthält auch die `UserRole` Joinentität, die benötigt wird, um die m: n Beziehung von Benutzern zu Rollen zu navigieren.
* Denken Sie daran, die Typen von Navigationseigenschaften entsprechend ändern `ApplicationXxx` Typen verwendet werden nun anstelle von `IdentityXxx` Typen.
* Denken Sie daran, verwenden Sie die `ApplicationXxx` in der allgemeinen `ApplicationContext` Definition.

### <a name="add-all-navigation-properties"></a>Alle Navigationseigenschaften hinzufügen

Im oben stehenden Abschnitt als Leitfaden verwenden, wird im folgende Beispiel Navigationseigenschaften für alle Beziehungen für alle Entitätstypen konfiguriert:

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

### <a name="use-composite-keys"></a>Zusammengesetzte Schlüssel

In den vorherigen Abschnitten wird gezeigt, ändern den Typ des Schlüssels im Identitätsmodell verwendet. Ändern das Identitätsmodell-Schlüssel zur Verwendung von zusammengesetzten Schlüsseln wird nicht unterstützt oder empfohlen. Verwenden einen zusammengesetzten Schlüssel, mit der Identität umfasst eine Änderung, wie der Identity Manager-Code mit dem Modell interagiert. Diese Anpassung ist über den Rahmen dieses Dokuments hinaus.

### <a name="change-tablecolumn-names-and-facets"></a>Die Datenbanktabelle oder-Spalte Namen ändern und facets

Um die Namen von Tabellen und Spalten zu ändern, rufen `base.OnModelCreating`. Fügen Sie dann die Konfiguration, um die Standardwerte außer Kraft setzen. Um beispielsweise den Namen aller Tabellen für die Identität ändern:

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

Diese Beispiele verwenden die Identity-Standardtypen. Wenn einen app-Typ verwenden, z. B. `ApplicationUser`, konfigurieren Sie diesen Typ anstelle der Standardtyp.

Im folgende Beispiel ändert einige Spaltennamen:

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

Einige Typen von Datenbankspalten können konfiguriert werden, mit bestimmten *Facets* (z. B. die maximale `string` zulässige Länge). Im folgenden Beispiel wird die maximale Länge der Spalte für mehrere `string` Eigenschaften im Modell:

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

### <a name="map-to-a-different-schema"></a>Ordnen Sie zu einem anderen Schema zu

Schemas können über Datenbankanbieter unterschiedlich Verhalten. Für SQL Server, der Standardwert ist die Erstellung von allen Tabellen in der *Dbo* Schema. Die Tabellen können in einem anderen Schema erstellt werden. Zum Beispiel:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Lazy Loading

In diesem Abschnitt wird die Unterstützung von lazy Loading-Proxys im Identitätsmodell hinzugefügt. Lazy-Loading ist nützlich, da die Navigationseigenschaften verwendet werden, ohne zuerst um sicherzustellen, dass sie geladen werden können.

Entitätstypen können erfolgen, für die geeignet ist lazy Loading auf verschiedene Weise wie in beschrieben die [EF Core-Dokumentation](/ef/core/querying/related-data#lazy-loading). Der Einfachheit halber verwenden Sie lazy Loading-Proxys erfordert:

* Installation von der [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) Paket.
* Ein Aufruf von <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> in [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Öffentliche Entitätstypen mit `public virtual` Navigationseigenschaften.

Das folgende Beispiel veranschaulicht den Aufruf `UseLazyLoadingProxies` in `Startup.ConfigureServices`:

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Finden Sie in den vorherigen Beispielen Anleitungen dazu, die Entitätstypen Navigationseigenschaften hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authentication/scaffold-identity>

::: moniker-end
