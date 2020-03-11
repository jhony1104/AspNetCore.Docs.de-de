---
title: Migrieren von der ASP.net-Mitgliedschafts Authentifizierung zu ASP.net Core 2,0-Identität
author: isaac2004
description: Erfahren Sie, wie Sie vorhandene ASP.net-apps mithilfe der Mitgliedschafts Authentifizierung zum ASP.net Core 2,0-Identität migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651835"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrieren von der ASP.net-Mitgliedschafts Authentifizierung zu ASP.net Core 2,0-Identität

Von [Isaac Levin](https://isaaclevin.com)

In diesem Artikel wird die Migration des Datenbankschemas für ASP.net-apps mithilfe der Mitgliedschafts Authentifizierung zum ASP.net Core 2,0-Identität

> [!NOTE]
> Dieses Dokument enthält die erforderlichen Schritte zum Migrieren des Datenbankschemas für ASP.net-Mitgliedschafts basierte Apps zum Datenbankschema, das für die ASP.net Core Identität verwendet wird. Weitere Informationen zum Migrieren von der ASP.net-Mitgliedschafts basierten Authentifizierung zu ASP.net Identity finden Sie unter [Migrieren einer vorhandenen APP aus einer SQL-Mitgliedschaft zu ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Weitere Informationen zur ASP.net Core Identität finden Sie unter [Einführung in Identity auf ASP.net Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Überprüfen des Mitgliedschafts Schemas

Vor ASP.NET 2,0 waren die Entwickler dafür zuständig, den gesamten Authentifizierungs-und Autorisierungs Prozess für Ihre apps zu erstellen. Mit ASP.NET 2,0 wurde die Mitgliedschaft eingeführt, die eine Bausteine-Lösung für die Handhabung der Sicherheit innerhalb von ASP.net-apps bereitstellt. Entwickler waren nun in der Lage, ein Schema mit dem Befehl [aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) in eine SQL Server-Datenbank zu überarbeiten. Nachdem Sie diesen Befehl ausgeführt haben, wurden die folgenden Tabellen in der-Datenbank erstellt.

  ![Mitgliedschafts Tabellen](identity/_static/membership-tables.png)

Um vorhandene apps zu ASP.net Core 2,0-Identität zu migrieren, müssen die Daten in diesen Tabellen zu den vom neuen Identitäts Schema verwendeten Tabellen migriert werden.

## <a name="aspnet-core-identity-20-schema"></a>Schema für die ASP.net Core Identität 2,0

ASP.net Core 2,0 befolgt das in ASP.NET 4,5 eingeführte [Identitäts](/aspnet/identity/index) Prinzip. Obwohl das Prinzip freigegeben ist, ist die Implementierung zwischen den Frameworks anders, auch bei ASP.net Core Versionen (siehe [Migrieren von Authentifizierung und Identität zu ASP.net Core 2,0](xref:migration/1x-to-2x/index)).

Die schnellste Möglichkeit, das Schema für die ASP.net Core 2,0-Identität anzuzeigen, besteht darin, eine neue ASP.net Core 2,0-App zu erstellen. Führen Sie diese Schritte in Visual Studio 2017 aus:

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.
1. Erstellen Sie ein neues **ASP.net Core Webanwendungs** Projekt mit dem Namen *coreidentitysample*.
1. Wählen Sie in der Dropdown Liste **ASP.net Core 2,0** aus, und wählen Sie dann **Webanwendung**. Diese Vorlage erzeugt eine [Razor Pages](xref:razor-pages/index) -app. Bevor Sie auf **OK**klicken, klicken Sie auf **Authentifizierung ändern**.
1. Wählen Sie **einzelne Benutzerkonten** für die Identitäts Vorlagen aus. Klicken Sie abschließend auf **OK**und dann auf **OK**. Visual Studio erstellt ein Projekt mithilfe der ASP.net Core Identity-Vorlage.
1. Wählen **Sie** Extras > **nuget-Paket-Manager** > Paket-Manager- **Konsole** , um das Fenster Paket- **Manager-Konsole** (PMC) zu öffnen.
1. Navigieren Sie zum Projektstamm in der PMC, und führen Sie den Entity Framework Core-`Update-Database` Befehl [(EF)](/ef/core) aus.

    ASP.net Core 2,0-Identität verwendet EF Core, um mit der Datenbank zu interagieren, in der die Authentifizierungsdaten gespeichert werden. Damit die neu erstellte APP funktioniert, muss eine Datenbank vorhanden sein, um diese Daten zu speichern. Nach dem Erstellen einer neuen App besteht die schnellste Möglichkeit zum Überprüfen des Schemas in einer Datenbankumgebung darin, die Datenbank mithilfe [EF Core Migrationen](/ef/core/managing-schemas/migrations/)zu erstellen. Bei diesem Vorgang wird eine Datenbank erstellt, entweder lokal oder an anderer Stelle, die das Schema imitiert. Weitere Informationen finden Sie in der vorangehenden Dokumentation.

    EF Core Befehle verwenden die Verbindungs Zeichenfolge für die in *appSettings. JSON*angegebene Datenbank. Die folgende Verbindungs Zeichenfolge bezieht sich auf eine Datenbank auf dem *localhost* mit dem Namen *ASP-NET-Core-Identity*. In dieser Einstellung ist EF Core für die Verwendung der `DefaultConnection` Verbindungs Zeichenfolge konfiguriert.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Wählen Sie > **SQL Server-Objekt-Explorer** **anzeigen** aus. Erweitern Sie den Knoten, der dem Datenbanknamen entspricht, der in der `ConnectionStrings:DefaultConnection`-Eigenschaft von *appSettings. JSON*angegeben ist.

    Der `Update-Database`-Befehl hat die mit dem Schema angegebene Datenbank und alle Daten, die für die APP-Initialisierung erforderlich sind, erstellt. Die folgende Abbildung zeigt die Tabellenstruktur, die mit den vorherigen Schritten erstellt wurde.

    ![Identitäts Tabellen](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrieren des Schemas

Es gibt feine Unterschiede in den Tabellenstrukturen und-Feldern für Mitgliedschafts-und ASP.net Core Identität. Das Muster wurde für die Authentifizierung/Autorisierung mit ASP.net-und ASP.net Core-apps erheblich geändert. Die Schlüssel Objekte, die weiterhin mit der Identität verwendet werden, sind *Benutzer* und *Rollen*. Hier sind die Zuordnung von Tabellen für *Benutzer*, *Rollen*und *Benutzer Rollen*.

### <a name="users"></a>Benutzer

|*Identity<br>(dbo). AspNetUsers*        ||*Mitgliedschafts<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Feldname**                 |**Typ**|**Feldname**                                    |**Typ**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> Nicht alle Feld Zuordnungen ähneln eins-zu-eins-Beziehungen von der Mitgliedschaft zur ASP.net Core Identität. In der vorangehenden Tabelle wird das Standardschema für Mitgliedschafts Benutzer und das Schema der ASP.net Core Identität zugeordnet. Alle anderen benutzerdefinierten Felder, die für die Mitgliedschaft verwendet wurden, müssen manuell zugeordnet werden. In dieser Zuordnung gibt es keine Zuordnung für Kenn Wörter, da sowohl Kenn Wort Kriterien als auch Kenn Wort Salze nicht zwischen den beiden migriert werden. **Es wird empfohlen, das Kennwort als Null zu belassen und Benutzer aufzufordern, ihre Kenn Wörter zurückzusetzen.** In ASP.net Core Identity sollten `LockoutEnd` in der Zukunft auf ein Datum festgelegt werden, wenn der Benutzer gesperrt ist. Dies wird im Migrations Skript gezeigt.

### <a name="roles"></a>Rollen

|*Identity<br>(dbo). Aspnettroles)*        ||*Mitgliedschafts<br>(dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Feldname**                 |**Typ**|**Feldname**   |**Typ**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>Benutzerrollen

|*Identity<br>(dbo). AspNetUserRoles*||*Mitgliedschafts<br>(dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Feldname**           |**Typ**  |**Feldname**|**Typ**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

Verweisen Sie auf die vorangehenden Mapping-Tabellen, wenn Sie ein Migrations Skript für *Benutzer* und *Rollen*erstellen. Im folgenden Beispiel wird davon ausgegangen, dass Sie zwei Datenbanken auf einem Datenbankserver haben. Eine Datenbank enthält das vorhandene ASP.net-Mitgliedschafts Schema und die Daten. Die andere *coreidentitysample* -Datenbank wurde mithilfe der zuvor beschriebenen Schritte erstellt. Kommentare sind Inline enthalten, um weitere Informationen zu erhalten.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Nachdem das vorherige Skript abgeschlossen wurde, wird die zuvor erstellte ASP.net Core Identitäts-App mit Mitgliedschafts Benutzern aufgefüllt. Benutzer müssen ihre Kenn Wörter ändern, bevor Sie sich anmelden.

> [!NOTE]
> Wenn das Mitgliedschaftssystem Benutzer mit Benutzernamen enthielt, die nicht mit der e-Mail-Adresse übereinstimmen, sind Änderungen an der zuvor erstellten App erforderlich, um dies zu ermöglichen. Die Standardvorlage erwartet, dass `UserName` und `Email` identisch sind. In Situationen, in denen Sie sich unterscheiden, muss der Anmeldevorgang so geändert werden, dass anstelle von `Email``UserName` verwendet werden.

Entfernen Sie im `PageModel` der Anmeldeseite, die sich unter *Pages\Account\Login.cshtml.cs*befindet, das `[EmailAddress]`-Attribut aus der *Email-* Eigenschaft. Benennen Sie ihn in *username*um. Dies erfordert eine Änderung, wenn `EmailAddress` in der *Ansicht* und im Seiten *Modell*angegeben wird. Das Ergebnis sieht wie folgt aus:

 ![Anmeldung korrigiert](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie Benutzer aus der SQL-Mitgliedschaft in ASP.net Core 2,0-Identität portieren. Weitere Informationen zur ASP.net Core Identität finden Sie unter [Einführung in die Identität](xref:security/authentication/identity).
