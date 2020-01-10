---
title: Benutzerdefinierte Speicher Anbieter für ASP.net Core Identität
author: ardalis
description: Erfahren Sie, wie Sie benutzerdefinierte Speicher Anbieter für ASP.net Core Identität konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 70951085474d88fd57f1b1496a41adcda520b91f
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829152"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Benutzerdefinierte Speicher Anbieter für ASP.net Core Identität

Von [Steve Smith](https://ardalis.com/)

ASP.net Core Identität ist ein erweiterbares System, mit dem Sie einen benutzerdefinierten Speicher Anbieter erstellen und mit Ihrer APP verbinden können. In diesem Thema wird beschrieben, wie ein angepasster Speicher Anbieter für ASP.net Core Identität erstellt wird. Es behandelt die wichtigsten Konzepte zum Erstellen eines eigenen Speicher Anbieters, aber keine schrittweise exemplarische Vorgehensweise.

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Einführung

Standardmäßig speichert das Identitätssystem ASP.net Core Benutzerinformationen in einer SQL Server Datenbank mithilfe von Entity Framework Core. Bei vielen apps funktioniert dieser Ansatz gut. Es empfiehlt sich jedoch, einen anderen Persistenzmechanismus oder ein anderes Datenschema zu verwenden. Beispiel:

* Sie verwenden [Azure Table Storage](/azure/storage/) oder einen anderen Datenspeicher.
* Die Datenbanktabellen haben eine andere Struktur. 
* Möglicherweise möchten Sie einen anderen Ansatz für den Datenzugriff verwenden, z. b. [dapper.](https://github.com/StackExchange/Dapper) 

In jedem dieser Fälle können Sie einen benutzerdefinierten Anbieter für den Speichermechanismus schreiben und den Anbieter in Ihre APP einbinden.

ASP.net Core Identität ist in Projektvorlagen in Visual Studio mit der Option "einzelne Benutzerkonten" enthalten.

Wenn Sie die .net Core-CLI verwenden, fügen Sie `-au Individual`hinzu:

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>Die ASP.net Core Identitäts Architektur

ASP.net Core Identität besteht aus Klassen, die als Manager und Stores bezeichnet werden. *Manager* sind Klassen auf hoher Ebene, die ein App-Entwickler zum Ausführen von Vorgängen verwendet, z. b. zum Erstellen eines Identitäts Benutzers. *Stores* sind Klassen auf niedrigerer Ebene, die angeben, wie Entitäten, z. b. Benutzer und Rollen, beibehalten werden. Filialen befolgen das Repository-Muster und sind eng mit dem Persistenzmechanismus verknüpft. Manager sind von Geschäften entkoppelt, d. h. Sie können den Persistenzmechanismus ersetzen, ohne den Anwendungscode zu ändern (mit Ausnahme der Konfiguration).

Im folgenden Diagramm wird gezeigt, wie eine Web-App mit den Managern interagiert, während Stores mit der Datenzugriffs Ebene interagieren.

![ASP.net Core-apps funktionieren mit Managern (z. b. "usermanager", "roleManager"). Manager arbeiten mit speichern (z. b. "userstore"), die über eine Bibliothek wie Entity Framework Core mit einer Datenquelle kommunizieren.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Zum Erstellen eines benutzerdefinierten Speicher Anbieters erstellen Sie die Datenquelle, die Datenzugriffs Ebene und die Speicher Klassen, die mit dieser Datenzugriffs Schicht interagieren (die grünen und grauen Felder in der obigen Abbildung). Sie müssen die Vorgesetzten oder Ihren app-Code, der mit Ihnen interagiert (die blauen Felder oben) nicht anpassen.

Beim Erstellen einer neuen Instanz von `UserManager` oder `RoleManager` geben Sie den Typ der Benutzerklasse an und übergeben eine Instanz der Klasse "Store" als Argument. Mit diesem Ansatz können Sie Ihre angepassten Klassen in ASP.net Core einbinden. 

[Neukonfigurieren der APP für die Verwendung eines neuen Speicher Anbieters](#reconfigure-app-to-use-a-new-storage-provider) zeigt, wie `UserManager` und `RoleManager` mit einem angepassten Speicher instanziiert werden.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.net Core Identity speichert Datentypen

[ASP.net Core Identitäts](https://github.com/aspnet/identity) Datentypen werden in den folgenden Abschnitten beschrieben:

### <a name="users"></a>-Benutzer

Registrierte Benutzer Ihrer Website. Der [identityuser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) -Typ kann erweitert oder als Beispiel für Ihren eigenen benutzerdefinierten Typ verwendet werden. Sie müssen nicht von einem bestimmten Typ erben, um eine eigene benutzerdefinierte Identitäts Speicherlösung zu implementieren.

### <a name="user-claims"></a>Benutzeransprüche

Ein Satz von-Anweisungen (oder- [Ansprüchen](/dotnet/api/system.security.claims.claim)) zum Benutzer, die die Identität des Benutzers darstellen. Kann einen größeren Ausdruck der Identität des Benutzers aktivieren, als durch Rollen erreicht werden kann.

### <a name="user-logins"></a>Benutzeranmeldungen

Informationen zum externen Authentifizierungs Anbieter (z. b. Facebook oder eine Microsoft-Konto), die bei der Anmeldung eines Benutzers verwendet werden sollen. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>-Rollen

Autorisierungs Gruppen für Ihre Website. Enthält die Rollen-ID und den Rollennamen (z. b. "admin" oder "Employee"). [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Die Datenzugriffs Ebene

In diesem Thema wird davon ausgegangen, dass Sie mit dem Persistenzmechanismus vertraut sind, den Sie verwenden werden, und wie Sie Entitäten für diesen Mechanismus erstellen. In diesem Thema wird nicht erläutert, wie die-Depots oder Datenzugriffsklassen erstellt werden. Es werden einige Vorschläge zu Entwurfsentscheidungen bei der Arbeit mit ASP.net Core Identität bereitstellt.

Beim Entwerfen der Datenzugriffs Ebene für einen angepassten Speicher Anbieter haben Sie viel Freiheit. Sie müssen nur Persistenzmechanismen für Funktionen erstellen, die Sie in Ihrer APP verwenden möchten. Wenn Sie z. b. keine Rollen in der App verwenden, müssen Sie keinen Speicher für Rollen oder Benutzer Rollen Zuordnungen erstellen. Ihre Technologie und vorhandene Infrastruktur benötigen möglicherweise eine Struktur, die sich stark von der Standard Implementierung ASP.net Core Identität unterscheidet. In der Datenzugriffs Ebene stellen Sie die Logik bereit, um mit der Struktur der Speicher Implementierung zu arbeiten.

Die Datenzugriffs Ebene stellt die Logik bereit, um die Daten aus ASP.net Core Identität in einer Datenquelle zu speichern. Die Datenzugriffs Ebene für Ihren benutzerdefinierten Speicher Anbieter kann die folgenden Klassen enthalten, um Benutzer-und Rollen Informationen zu speichern.

### <a name="context-class"></a>Context-Klasse

Kapselt die Informationen zum Herstellen einer Verbindung mit dem Persistenzmechanismus und zum Ausführen von Abfragen. Mehrere Daten Klassen erfordern eine Instanz dieser Klasse, die in der Regel über die Abhängigkeitsinjektion bereitgestellt wird. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Benutzerspeicher

Speichert Benutzerinformationen (z. b. Benutzername und Kenn Wort Hash) und ruft Sie ab. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Rollen Speicher

Speichert Rollen Informationen (z. b. den Rollennamen) und ruft Sie ab. [Beispiel](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Speicherung von Benutzer Ansprüchen

Speichert Benutzer Anspruchs Informationen (z. b. den Anspruchstyp und-Wert) und ruft Sie ab. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Speicherung von Benutzeranmeldungen

Speichert und ruft Benutzer Anmelde Informationen ab (z. b. einen externen Authentifizierungs Anbieter). [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Benutzer Rollen Speicher

Speichert und ruft ab, welche Rollen welchen Benutzern zugewiesen werden. [Beispiel](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Tipp:** Implementieren Sie nur die Klassen, die Sie in Ihrer APP verwenden möchten.

Stellen Sie in den Datenzugriffsklassen Code zum Ausführen von Daten Vorgängen für den Persistenzmechanismus bereit. Beispielsweise können Sie in einem benutzerdefinierten Anbieter über den folgenden Code verfügen, um einen neuen Benutzer in der *Store* -Klasse zu erstellen:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Die Implementierungs Logik zum Erstellen des Benutzers befindet sich in der `_usersTable.CreateAsync`-Methode, die unten dargestellt ist.

## <a name="customize-the-user-class"></a>Benutzerklasse anpassen

Wenn Sie einen Speicher Anbieter implementieren, erstellen Sie eine Benutzerklasse, die der [identityuser-Klasse](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)entspricht.

Die Benutzerklasse muss mindestens eine `Id` und eine `UserName`-Eigenschaft enthalten.

Die `IdentityUser`-Klasse definiert die Eigenschaften, die vom `UserManager` beim Ausführen angeforderter Vorgänge aufgerufen werden. Der Standardtyp der `Id` Eigenschaft ist eine Zeichenfolge, Sie können jedoch von `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` erben und einen anderen Typ angeben. Das Framework erwartet, dass die Speicher Implementierung Datentyp Konvertierungen verarbeitet.

## <a name="customize-the-user-store"></a>Anpassen des Benutzer Stores

Erstellen Sie eine `UserStore` Klasse, die die Methoden für alle Daten Vorgänge für den Benutzer bereitstellt. Diese Klasse entspricht der Klasse [userstore&lt;tuser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . Implementieren Sie in der `UserStore`-Klasse `IUserStore<TUser>` und die optionalen erforderlichen Schnittstellen. Basierend auf der in der APP bereitgestellten Funktionalität wählen Sie aus, welche optionalen Schnittstellen implementiert werden sollen.

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

Die optionalen Schnittstellen erben von `IUserStore<TUser>`. Sie können einen teilweise implementierten Beispiel Benutzerspeicher in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)sehen.

In der `UserStore`-Klasse verwenden Sie die Datenzugriffsklassen, die Sie erstellt haben, um Vorgänge auszuführen. Diese werden mithilfe von Abhängigkeitsinjektion übermittelt. Beispielsweise verfügt die `UserStore`-Klasse in der SQL Server mit der dapperimplementierung über die `CreateAsync`-Methode, die eine Instanz von `DapperUsersTable` verwendet, um einen neuen Datensatz einzufügen:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Schnittstellen, die beim Anpassen des Benutzer Stores implementiert werden

* **IUserStore**  
 Die [&lt;tuser-&gt;Schnittstelle von iuserstore](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) ist die einzige Schnittstelle, die Sie im Benutzerspeicher implementieren müssen. Es definiert Methoden zum Erstellen, aktualisieren, löschen und Abrufen von Benutzern.
* **IUserClaimStore**  
 Die [iuserclaimstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um Benutzer Ansprüche zu aktivieren. Sie enthält Methoden zum Hinzufügen, entfernen und Abrufen von Benutzer Ansprüchen.
* **IUserLoginStore**  
 Das [iuserloginstore-&lt;tuser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definiert die Methoden, die Sie implementieren, um externe Authentifizierungs Anbieter zu aktivieren. Sie enthält Methoden zum Hinzufügen, entfernen und Abrufen von Benutzeranmeldungen sowie eine Methode zum Abrufen eines Benutzers basierend auf den Anmelde Informationen.
* **IUserRoleStore**  
 Die [iuserrolestore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um einen Benutzer einer Rolle zuzuordnen. Sie enthält Methoden zum Hinzufügen, entfernen und Abrufen der Rollen eines Benutzers sowie eine Methode, um zu überprüfen, ob ein Benutzer einer Rolle zugewiesen ist.
* **IUserPasswordStore**  
 Die [iuserpasswordstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um Hash Kennwörter beizubehalten. Sie enthält Methoden zum erhalten und Festlegen des hashkennworts sowie eine Methode, die angibt, ob der Benutzer ein Kennwort festgelegt hat.
* **IUserSecurityStampStore**  
 Die [iusersecuritystampstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um einen Sicherheits Stempel zu verwenden, der angibt, ob sich die Kontoinformationen des Benutzers geändert haben. Dieser Stempel wird aktualisiert, wenn ein Benutzer das Kennwort ändert oder Anmeldungen hinzufügt oder entfernt. Sie enthält Methoden zum erhalten und Festlegen des Sicherheits Stempels.
* **IUserTwoFactorStore**  
 Die [iusertwofactorstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um die zweistufige Authentifizierung zu unterstützen. Sie enthält Methoden zum erhalten und festlegen, ob die zweistufige Authentifizierung für einen Benutzer aktiviert ist.
* **IUserPhoneNumberStore**  
 Die [iuserphonenumberstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um die Telefonnummern von Benutzern zu speichern. Sie enthält Methoden zum erhalten und Festlegen der Telefonnummer und darüber, ob die Telefonnummer bestätigt ist.
* **IUserEmailStore**  
 Die [iuseremailstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) Schnittstelle definiert die Methoden, die Sie zum Speichern von Benutzer-e-Mail-Adressen implementieren Sie enthält Methoden zum erhalten und Festlegen der e-Mail-Adresse und darüber, ob die e-Mail bestätigt ist.
* **IUserLockoutStore**  
 Die [iuserlockoutstore-&lt;tuser-&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) Schnittstelle definiert die Methoden, die Sie implementieren, um Informationen zum Sperren eines Kontos zu speichern. Sie enthält Methoden zum Nachverfolgen fehlerhafter Zugriffsversuche und Sperr Outs.
* **IQueryableUserStore**  
 Die Schnittstelle [iqueryableuserstore&lt;tuser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) definiert die von Ihnen implementierten Elemente, um einen abfragbaren Benutzerspeicher bereitzustellen.

Sie implementieren nur die Schnittstellen, die in Ihrer APP benötigt werden. Beispiel:

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

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>Identityuserclaim, identityuserlogin und identityuserrole

Der `Microsoft.AspNet.Identity.EntityFramework`-Namespace enthält Implementierungen der Klassen [identityuserclaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [identityuserlogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)und [identityuserrole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Wenn Sie diese Features verwenden, möchten Sie möglicherweise Ihre eigenen Versionen dieser Klassen erstellen und die Eigenschaften für Ihre APP definieren. Manchmal ist es jedoch effizienter, diese Entitäten beim Ausführen von grundlegenden Vorgängen (z. b. hinzufügen oder Entfernen eines Benutzer Anspruchs) nicht in den Arbeitsspeicher zu laden. Die Back-End-Speicher Klassen können diese Vorgänge stattdessen direkt für die Datenquelle ausführen. Die `UserStore.GetClaimsAsync`-Methode kann z. b. die `userClaimTable.FindByUserId(user.Id)`-Methode aufrufen, um eine Abfrage für diese Tabelle direkt auszuführen und eine Liste von Ansprüchen zurückzugeben.

## <a name="customize-the-role-class"></a>Anpassen der Rollen Klasse

Beim Implementieren eines Rollen Speicher Anbieters können Sie einen benutzerdefinierten Rollentyp erstellen. Eine bestimmte Schnittstelle muss nicht implementiert werden, Sie muss jedoch über eine `Id` verfügen, und Sie verfügt in der Regel über eine `Name`-Eigenschaft.

Im folgenden finden Sie ein Beispiel für eine Rollen Klasse:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Anpassen des Rollen Speicher

Sie können eine `RoleStore` Klasse erstellen, die die Methoden für alle Daten Vorgänge für Rollen bereitstellt. Diese Klasse entspricht der [rolestore-&lt;trole-&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) Klasse. In der `RoleStore`-Klasse implementieren Sie die `IRoleStore<TRole>` und optional die `IQueryableRoleStore<TRole>`-Schnittstelle.

* **IRoleStore&lt;TRole&gt;**  
 Die [irolestore-&lt;trole-&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) Schnittstelle definiert die Methoden, die in der Rollen Speicher Klasse implementiert werden. Sie enthält Methoden zum Erstellen, aktualisieren, löschen und Abrufen von Rollen.
* **RoleStore&lt;TRole&gt;**  
 Um `RoleStore`anzupassen, erstellen Sie eine Klasse, die die `IRoleStore<TRole>`-Schnittstelle implementiert. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Neukonfigurieren der APP für die Verwendung eines neuen Speicher Anbieters

Nachdem Sie einen Speicher Anbieter implementiert haben, konfigurieren Sie Ihre APP für die Verwendung. Wenn Ihre APP den Standardanbieter verwendet hat, ersetzen Sie Sie durch Ihren benutzerdefinierten Anbieter.

1. Entfernen Sie das `Microsoft.AspNetCore.EntityFramework.Identity` nuget-Paket.
1. Wenn sich der Speicher Anbieter in einem separaten Projekt oder Paket befindet, fügen Sie einen Verweis darauf hinzu.
1. Ersetzen Sie alle Verweise auf `Microsoft.AspNetCore.EntityFramework.Identity` durch eine using-Anweisung für den Namespace Ihres Speicher Anbieters.
1. Ändern Sie in der `ConfigureServices`-Methode die `AddIdentity`-Methode, um Ihre benutzerdefinierten Typen zu verwenden. Zu diesem Zweck können Sie eigene Erweiterungs Methoden erstellen. Ein Beispiel finden Sie unter [identityservicecollectionextensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Wenn Sie Rollen verwenden, aktualisieren Sie die `RoleManager`, um Ihre `RoleStore`-Klasse zu verwenden.
1. Aktualisieren Sie die Verbindungs Zeichenfolge und die Anmelde Informationen in die Konfiguration Ihrer APP.

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

* [Benutzerdefinierte Speicher Anbieter für die ASP.NET 4. x-Identität](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.net Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; dieses Repository enthält Links zu communityanbietern, die von der Community verwaltet werden.
