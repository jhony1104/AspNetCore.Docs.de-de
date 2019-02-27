---
title: Benutzerdefinierte Speicheranbieter für ASP.NET Core Identity
author: ardalis
description: Erfahren Sie, wie Sie benutzerdefinierte Speicheranbieter für ASP.NET Core Identity zu konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: ccd56d0c15639e1ad29094e947f8055702ee2264
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833669"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Benutzerdefinierte Speicheranbieter für ASP.NET Core Identity

Von [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity handelt es sich um ein erweiterbares System können Sie zum Erstellen eines benutzerdefinierten Speicheranbieters und verbinden sie zu Ihrer app. Dieses Thema beschreibt, wie Sie einen benutzerdefinierte Speicheranbieter für ASP.NET Core Identity zu erstellen. Es behandelt die entscheidenden Konzepte für Ihren eigenen Anbieter erstellen, ist aber eine schrittweise exemplarische Vorgehensweise.

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Einführung

Standardmäßig speichert die ASP.NET Core Identity-System Benutzerinformationen in einer SQL Server-Datenbank mit Entity Framework Core. Bei vielen apps können eignet sich dieser Ansatz gut. Möglicherweise bevorzugen Sie jedoch einen anderen persistenzmechanismus oder Datenschema verwenden. Zum Beispiel:

* Verwenden Sie [Azure Table Storage](/azure/storage/) oder einem anderen Datenspeicher.
* Datenbanktabellen haben eine andere Struktur. 
* Möglicherweise möchten Sie verwenden Sie einen Ansatz für unterschiedliche Daten, z. B. [Dapper](https://github.com/StackExchange/Dapper). 

In jedem dieser Fälle können schreiben ein benutzerdefiniertes Anbieters für den Storage-Mechanismus und schließen Sie diesen Anbieter in Ihrer app.

ASP.NET Core Identity ist in-Projektvorlagen in Visual Studio mit der Option "Einzelne Benutzerkonten" enthalten.

Wenn Sie .NET Core-CLI verwenden möchten, fügen `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>Die ASP.NET Core Identity-Architektur

ASP.NET Core Identity besteht aus Klassen, die aufgerufen wird, Manager und Speicher. *Manager* sind allgemeine Klassen, die app-Entwickler zum Ausführen von Vorgängen, z. B. das Erstellen von eines Identity-Benutzers verwendet. *Speichert* sind Low-Level-Klassen, die angeben, wie Entitäten, z. B. Benutzer und Rollen, beibehalten werden. Speichert das Repository-Muster folgen und den Dauerhaftigkeitsmechanismus eng gekoppelt sind. Manager sind aus Geschäften, entkoppelt, was bedeutet, dass Sie den persistenzmechanismus ersetzen können, ohne Änderung des Codes der Anwendung (mit Ausnahme der Konfiguration).

Das folgende Diagramm zeigt, wie eine Web-app mit Managern, interagiert wird, während Speicher mit der Datenzugriffsebene interagieren.

![ASP.NET Core-Apps arbeiten mit Managern (z. B. "UserManager", "RoleManager"). Manager arbeiten mit speichern (z. B. "UserStore"), kommunizieren mit einer Datenquelle mithilfe einer Bibliothek wie Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Erstellen Sie zum Erstellen eines benutzerdefinierten Speicheranbieters der Datenquelle, die Datenzugriffsebene und den Store-Klassen, die Interaktion mit diesen Datenzugriffsebene (der grüne und die grauen Felder in der Abbildung oben). Sie müssen nicht die-Manager oder Ihren app-Code, mit der interagiert werden (die blauen Felder oben) anpassen.

Beim Erstellen einer neuen Instanz der `UserManager` oder `RoleManager` Sie geben den Typ der Benutzerklasse, und übergeben Sie eine Instanz der Store-Klasse als Argument. Dadurch können Sie Ihre benutzerdefinierten Klassen in ASP.NET Core zu integrieren. 

[Konfigurieren der app zur Verwendung der neuen Speicheranbieter](#reconfigure-app-to-use-a-new-storage-provider) wird gezeigt, wie zum Instanziieren `UserManager` und `RoleManager` mit einem benutzerdefinierten Speicher.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity speichert-Datentypen

[ASP.NET Core Identity](https://github.com/aspnet/identity) -Datentypen werden in den folgenden Abschnitten beschrieben:

### <a name="users"></a>Benutzer

Registrierte Benutzer Ihrer Website. Die ["identityuser"](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) Typ erweitert oder als ein Beispiel für einen eigenen benutzerdefinierten Typ verwendet werden kann. Sie müssen nicht erben, implementieren Ihre eigenen benutzerdefinierten identitätslösung für den Speicher eines bestimmten Typs.

### <a name="user-claims"></a>Benutzeransprüche

Ein Satz von Anweisungen (oder [Ansprüche](/dotnet/api/system.security.claims.claim)) über den Benutzer, die die Identität des Benutzers darstellen. Sie können größer Ausdruck, der die Identität des Benutzers als über Rollen erreicht werden kann.

### <a name="user-logins"></a>Benutzeranmeldungen

Informationen zu den externen Authentifizierungsanbieter (z.B. Facebook oder ein Microsoft-Konto) beim Anmelden eines Benutzers verwenden. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Rollen

Autorisierungsgruppe für Ihre Website. Enthält den Rollennamen Id und der Rolle (z. B. "Admin" oder "Employee"). [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Die Datenzugriffsebene

In diesem Thema wird davon ausgegangen, dass Sie mit der persistenzmechanismus, den Sie beabsichtigen, verwenden Sie "und" Vorgehensweise: Erstellen von Entitäten für diesen Mechanismus vertraut sind. In diesem Thema angeben nicht ausführliche Informationen zum Erstellen des Repositorys oder Datenzugriffsklassen; Es bietet einige Vorschläge, entwurfsentscheidungen, bei der Arbeit mit ASP.NET Core Identity.

Sie haben nur wenige freiheiten beim Entwerfen eines benutzerdefinierten Speicheranbieters für der Datenzugriffsebene. Sie müssen nur die Dauerhaftigkeitsmechanismen für Funktionen zu erstellen, die Sie in Ihrer app verwenden möchten. Wenn Sie keine Rollen in Ihrer app verwenden, müssen Sie z. B. Speicher für Rollen oder Zuordnungen zwischen Benutzer und Rollen zu erstellen. Ihre Technologie und die vorhandene Infrastruktur möglicherweise eine Struktur, die sehr stark von der Standardimplementierung von ASP.NET Core Identity ist. In der Datenzugriffsebene Geben Sie die Logik zum Arbeiten mit der Struktur Ihrer Speicher-Implementierung.

Die Datenzugriffsebene enthält die Logik, um die Daten von ASP.NET Core Identity in einer Datenquelle zu speichern. Die Datenzugriffsebene für Ihre benutzerdefinierte Speicheranbieter sind zum Beispiel die folgenden Klassen zum Speichern von Informationen für Benutzer und die Rolle.

### <a name="context-class"></a>Context-Klasse

Kapselt die Informationen zur Verbindung mit Ihrem persistenzmechanismus aus, und Abfragen ausführen. Mehrere Datenklassen erfordern eine Instanz dieser Klasse, die in der Regel über Dependency Injection bereitgestellt werden. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Benutzerspeicher

Speichert und Benutzerinformationen (z. B. Benutzer und Kennwort-Hash) abruft. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Rolle Speicher

Speichert und ruft Rolleninformationen (z. B. der Role-Name) ab. [Beispiel](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Speicherung von userclaims.

Speichert und Anspruch Benutzerinformationen (z. B. den Anspruchstyp und-Wert) abruft. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Speicherung von userlogins.

Speichert und ruft die Benutzeranmeldeinformationen (z. B. ein externer Authentifizierungsanbieter) ab. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Speicherauslastung

Speichert und ruft Sie ab, welche Benutzer welche Rollen zugewiesen sind. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**TIPP:** Implementieren Sie nur die Klassen, die Sie in Ihrer app verwenden möchten.

Geben Sie in den Klassen der Data Access Code zum Ausführen von Datenvorgängen für den persistenzmechanismus. Z. B. in einen benutzerdefinierten Anbieter, Sie müssen möglicherweise den folgenden Code zum Erstellen eines neuen Benutzers in der *speichern* Klasse:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Die Implementierungslogik für die Erstellung des Benutzers befindet sich in der `_usersTable.CreateAsync` unten gezeigte Methode.

## <a name="customize-the-user-class"></a>Anpassen der-Klasse

Wenn Sie einen Speicheranbieter zu implementieren, erstellen Sie eine Benutzerklasse entspricht dem [Klasse "identityuser"](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Die User-Klasse muss mindestens enthalten eine `Id` und `UserName` Eigenschaft.

Die `IdentityUser` Klasse definiert die Eigenschaften, die die `UserManager` wird aufgerufen, wenn das Ausführen von angeforderten Vorgänge. Der Standardtyp der `Id` Eigenschaft ist eine Zeichenfolge, Sie können jedoch `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` , und geben Sie einen anderen Typ. Das Framework erwartet, dass die speicherimplementierung, die datentypkonvertierungen zu behandeln.

## <a name="customize-the-user-store"></a>Passen Sie den Speicher des Benutzers

Erstellen Sie eine `UserStore` -Klasse, die Methoden für alle Vorgänge für den Benutzer bereitstellt. Diese Klasse ist, entspricht die [UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) Klasse. In Ihrer `UserStore` Klasse und implementieren Sie `IUserStore<TUser>` und die optionalen Schnittstellen, die erforderlich sind. Sie wählen die optionalen Schnittstellen implementieren basierend auf den Funktionen, die in Ihrer app bereitgestellt.

### <a name="optional-interfaces"></a>Optionale Schnittstellen

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

Die optionale Schnittstellen erben von `IUserStore<TUser>`. Sehen Sie einen teilweise implementierte Beispielbenutzer im Speichern der [Beispiel-app](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

In der `UserStore` -Klasse, verwenden Sie die Data Access-Klassen, die Sie erstellt haben, um Vorgänge auszuführen. Diese werden mithilfe der Abhängigkeitsinjektion übergeben. In SQL Server mit Dapper-Implementierung, z. B. die `UserStore` -Klasse verfügt über die `CreateAsync` Methode verwendet eine Instanz von `DapperUsersTable` , einen neuen Datensatz einzufügen:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Schnittstellen zu implementieren, wenn der Speicher des Benutzers anpassen

* **IUserStore**  
 Die ["iuserstore"&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) Schnittstelle ist die einzige Schnittstelle müssen Sie in den Speicher des Benutzers implementieren. Definiert Methoden zum Erstellen, aktualisieren, löschen und Abrufen von Benutzern.
* **IUserClaimStore**  
 Die [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) Schnittstelle definiert die Methoden, die Sie zum Aktivieren von benutzeransprüchen implementieren. Sie enthält Methoden zum Hinzufügen, entfernen und Abrufen von Ansprüchen des Benutzers.
* **IUserLoginStore**  
 Die [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definiert die Methoden, die Sie implementieren, um externe Authentifizierungsanbieter zu aktivieren. Sie enthält Methoden zum Hinzufügen, entfernen und Abrufen von benutzeranmeldungen und eine Methode zum Abrufen von einem Benutzer basierend auf den Anmeldeinformationen.
* **IUserRoleStore**  
 Die [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um einen Benutzer einer Rolle zuzuordnen. Sie enthält Methoden zum Hinzufügen, entfernen, und rufen Sie die Rollen eines Benutzers und eine Methode zum Überprüfen, ob ein Benutzer eine Rolle zugewiesen ist.
* **IUserPasswordStore**  
 Die ["iuserpasswordstore"&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um verschlüsselte Kennwörter beibehalten werden. Sie enthält Methoden zum Abrufen und festlegen, das verschlüsselte Kennwort, und eine Methode, die angibt, ob der Benutzer ein Kennwort festgelegt hat.
* **IUserSecurityStampStore**  
 Die [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um Sie verwenden einen sicherheitsstempel für den, der angibt, ob die Kontoinformationen des Benutzers geändert wurde. Dieser Zeitstempel wird aktualisiert, wenn ein Benutzer ändert das Kennwort ein, oder hinzufügt oder entfernt Anmeldungen. Sie enthält Methoden zum Abrufen und Festlegen des sicherheitsstempels.
* **IUserTwoFactorStore**  
 Die [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um die zweistufige Authentifizierung unterstützen. Sie enthält Methoden zum Abrufen und festlegen, ob die zweistufige Authentifizierung für einen Benutzer aktiviert ist.
* **IUserPhoneNumberStore**  
 Die [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um die Telefonnummern des Benutzers zu speichern. Sie enthält Methoden zum Abrufen und festlegen, die Telefonnummer und gibt an, ob die Telefonnummer bestätigt ist.
* **IUserEmailStore**  
 Die [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um die Benutzer-e-Mail-Adressen zu speichern. Sie enthält Methoden zum Abrufen und festlegen, die e-Mail-Adresse und gibt an, ob die e-Mail bestätigt ist.
* **IUserLockoutStore**  
 Die [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um Informationen zum Sperren ein Konto zu speichern. Sie enthält Methoden zum Nachverfolgen von zugriffsversuchsfehlern und Sperrungen.
* **IQueryableUserStore**  
 Die [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) Schnittstelle definiert die Elemente, die Sie implementieren, um einen abfragbaren benutzerpeicher.

Sie implementieren nur die Schnittstellen, die erforderlich sind, in Ihrer app. Zum Beispiel:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim IdentityUserLogin und IdentityUserRole

Die `Microsoft.AspNet.Identity.EntityFramework` -Namespace enthält die Implementierungen der [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), und [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) Klassen. Wenn Sie diese Funktionen verwenden, empfiehlt es sich um Ihre eigenen Versionen dieser Klassen zu erstellen und definieren Sie die Eigenschaften für Ihre app. Manchmal ist es jedoch sehr viel effizienter, diese Entitäten nicht in den Arbeitsspeicher geladen, beim Ausführen von grundlegenden Vorgängen (z. B. hinzufügen oder Entfernen eines Benutzers Anspruch). Die Back-End-Speicher-Klassen können stattdessen diese Vorgänge direkt auf die Datenquelle ausführen. Z. B. die `UserStore.GetClaimsAsync` Methodenaufruf kann der `userClaimTable.FindByUserId(user.Id)` Methode zum Ausführen einer Abfrage auf, die Tabelle direkt aus, und eine Liste mit Ansprüchen zurückgeben.

## <a name="customize-the-role-class"></a>Anpassen der Role-Klasse

Beim Implementieren eines Rollenanbieters für den Speicher können Sie einen Typ für die benutzerdefinierte Rolle erstellen. Sie müssen eine bestimmte Schnittstelle nicht implementieren, es müssen jedoch eine `Id` und in der Regel verfügt über eine `Name` Eigenschaft.

Im folgenden finden eine Rolle-Beispielklasse:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Anpassen der rollenspeicher

Sie erstellen eine `RoleStore` -Klasse, die Methoden für alle Vorgänge für Rollen bereitstellt. Diese Klasse ist, entspricht die [RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) Klasse. In der `RoleStore` -Klasse, die Sie implementieren die `IRoleStore<TRole>` und optional die `IQueryableRoleStore<TRole>` Schnittstelle.

* **IRoleStore&lt;TRole&gt;**  
 Die [IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) Schnittstelle definiert die Methoden, die in der Rolle Store-Klasse implementieren. Sie enthält Methoden zum Erstellen, aktualisieren, löschen und Rollen werden abgerufen.
* **RoleStore&lt;TRole&gt;**  
 Zum Anpassen `RoleStore`, erstellen Sie eine Klasse, die implementiert die `IRoleStore<TRole>` Schnittstelle. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Konfigurieren der app, um einen neuen Speicheranbieter verwenden

Nachdem Sie einen Speicheranbieter implementiert haben, konfigurieren Sie Ihre app aus, um es zu verwenden. Wenn Ihre app den Standardanbieter verwendet werden, durch den benutzerdefinierten Anbieter ersetzen.

1. Entfernen Sie die `Microsoft.AspNetCore.EntityFramework.Identity` NuGet-Paket.
1. Wenn der Speicheranbieter in einem separaten Projekt oder Paket befindet, fügen Sie einen Verweis darauf hinzu.
1. Ersetzen Sie alle Verweise auf `Microsoft.AspNetCore.EntityFramework.Identity` mit einer using-Anweisung für den Namespace von Ihrem Speicheranbieter.
1. In der `ConfigureServices` -Methode, Ändern der `AddIdentity` Methode, um Ihre benutzerdefinierten Typen verwenden. Sie können Ihre eigenen Erweiterungsmethoden für diesen Zweck erstellen. Finden Sie unter [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) verdeutlicht.
1. Wenn Sie Rollen verwenden, aktualisieren Sie die `RoleManager` verwenden Ihre `RoleStore` Klasse.
1. Aktualisieren Sie die Verbindungszeichenfolge und Anmeldeinformationen zu Ihrer app Konfiguration.

Beispiel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Verweise

* [Benutzerdefinierte Speicheranbieter für ASP.NET 4.x-Identität](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/aspnet/identity) &ndash; dieses Repository enthält Links zu Community-Speicheranbieter verwaltet.
