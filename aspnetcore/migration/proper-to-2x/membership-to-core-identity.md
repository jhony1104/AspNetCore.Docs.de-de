---
title: Migrieren von ASP.NET Membership-Authentifizierung zu ASP.NET Core 2.0-Identitätsanbieter
author: isaac2004
description: Informationen Sie zum Migrieren von vorhandener ASP.NET-Anwendungen mithilfe von Mitgliedschaft-Authentifizierung für ASP.NET Core 2.0-Identitätsanbieter.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084875"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrieren von ASP.NET Membership-Authentifizierung zu ASP.NET Core 2.0-Identitätsanbieter

Von [Isaac Levin](https://isaaclevin.com)

In diesem Artikel wird das Datenbankschema für ASP.NET-Apps mithilfe von Mitgliedschaft-Authentifizierung für ASP.NET Core 2.0-Identität migrieren.

> [!NOTE]
> Dieses Dokument enthält die erforderlichen Schritte zum Migrieren des Datenbankschemas für ASP.NET Membership-basierte apps des Datenbankschemas für ASP.NET Core Identity verwendet. Weitere Informationen zum Migrieren von ASP.NET Membership-basierte Authentifizierung zu ASP.NET Identity finden Sie unter [Migrieren einer vorhandenen app aus SQL-Mitgliedschaftsanbieter nach ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Weitere Informationen zu ASP.NET Core Identity, finden Sie unter [Einführung in die Identität in ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Überprüfung des mitgliedschaftsschemas

Vor ASP.NET 2.0 wurden Entwickler damit beauftragt, den Gesamtprozess für Authentifizierung und Autorisierung für ihre apps zu erstellen. Mitgliedschaft wurde mit ASP.NET 2.0 eingeführt und Bereitstellung einer Lösung Codebausteine für die Behandlung von Sicherheit in ASP.NET-Anwendungen. Entwickler konnten für das Bootstrapping eines Schemas in SQL Server-Datenbank mit der [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) Befehl. Nach der Ausführung dieses Befehls wurden in den folgenden Tabellen in der Datenbank erstellt.

  ![Mitgliedertabellen](identity/_static/membership-tables.png)

Zum Migrieren von vorhandenen apps in ASP.NET Core 2.0-Identitätsanbieter müssen die Daten in diesen Tabellen in die von der neuen Identität Schemas verwendeten Tabellen migriert werden.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Identity-2.0-schema

ASP.NET Core 2.0 folgt die [Identität](/aspnet/identity/index) Prinzip, die in ASP.NET 4.5 eingeführt. Aber das Prinzip freigegeben wird, die Implementierung zwischen den Frameworks unterscheidet auch zwischen Versionen von ASP.NET Core (finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).

Die schnellste Möglichkeit zum Anzeigen des Schemas für ASP.NET Core 2.0-Identitätsanbieter ist die Erstellung eine neue ASP.NET Core 2.0-app. In Visual Studio 2017, gehen Sie wie folgt vor:

1. Wählen Sie **Datei** > **Neu** > **Projekt** aus.
1. Erstellen Sie ein neues **ASP.NET Core-Webanwendung** Projekt mit dem Namen *CoreIdentitySample*.
1. Wählen Sie **ASP.NET Core 2.0** in der Dropdownliste und wählen Sie dann **Webanwendung**. Diese Vorlage erzeugt eine [Razor-Seiten](xref:razor-pages/index) app. Bevor Sie auf **OK**, klicken Sie auf **Authentifizierung ändern**.
1. Wählen Sie **einzelne Benutzerkonten** für die Identity-Vorlagen. Klicken Sie abschließend auf **OK**, klicken Sie dann **OK**. Visual Studio erstellt ein Projekt mithilfe der ASP.NET Core Identity-Vorlage.
1. Wählen Sie **Tools** > **NuGet Package Manager** > **-Paket-Manager-Konsole** zum Öffnen der **-Paket-Manager-Konsole** Fenster "(PMC).
1. Navigieren Sie zu das Stammverzeichnis des Projekts in PMC ein, und führen die [Entity Framework (EF) Core](/ef/core) `Update-Database` Befehl.

    EF Core wird von ASP.NET Core 2.0-Identität verwendet, um die Interaktion mit der Datenbank, die Speichern der Authentifizierungsdaten. In der Reihenfolge für die neu erstellte app funktioniert muss es als Datenbank zum Speichern dieser Daten. Nach dem Erstellen einer neuen app, die schnellste Möglichkeit, die das Schema in einer datenbankumgebung zu überprüfen ist die Erstellung der Datenbank mithilfe [EF Core-Migrationen](/ef/core/managing-schemas/migrations/). Dieser Vorgang erstellt eine Datenbank, entweder lokal oder an anderer Stelle die imitiert dieses Schema. Überprüfen Sie die obige Dokumentation für Weitere Informationen.

    EF Core-Befehle verwenden die Verbindungszeichenfolge für die Datenbank, die im angegebenen *"appSettings.JSON"*. Die folgende Verbindungszeichenfolge wird eine Datenbank auf *"localhost"* mit dem Namen *Asp-Net-Core-Identity*. Mit dieser Einstellung ist EF Core zur Verwendung konfiguriert die `DefaultConnection` Verbindungszeichenfolge.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Wählen Sie **Ansicht** > **Objekt-Explorer von SQL Server**. Erweitern Sie den Knoten für den Datenbanknamen angegeben werden, der `ConnectionStrings:DefaultConnection` Eigenschaft *"appSettings.JSON"*.

    Die `Update-Database` Befehl erstellt mit dem Schema der Datenbank und alle Daten, die für app-Initialisierung erforderlich sind. Die folgende Abbildung zeigt die Struktur der Tabelle, die mit den vorherigen Schritten erstellt wird.

    ![Identity-Tabellen](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrieren des Schemas

Es gibt feine Unterschiede in den Feldern für Mitgliedschafts- und ASP.NET Core Identity "und" Tabellenstrukturen. Das Muster für die Authentifizierung/Autorisierung in ASP.NET- und ASP.NET Core-apps wesentlich geändert. Sind die wichtigsten Objekte, die immer noch mit der Identität verwendet werden *Benutzer* und *Rollen*. Nachfolgend finden Sie Mapping-Tabellen für *Benutzer*, *Rollen*, und *UserRoles*.

### <a name="users"></a>Benutzer

|*Identity<br>(dbo.AspNetUsers)*        ||*Mitgliedschaft<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Name des Felds**                 |**Type**|**Name des Felds**                                    |**Type**|
|`Id`                           |Zeichenfolge  |`aspnet_Users.UserId`                             |Zeichenfolge  |
|`UserName`                     |Zeichenfolge  |`aspnet_Users.UserName`                           |Zeichenfolge  |
|`Email`                        |Zeichenfolge  |`aspnet_Membership.Email`                         |Zeichenfolge  |
|`NormalizedUserName`           |Zeichenfolge  |`aspnet_Users.LoweredUserName`                    |Zeichenfolge  |
|`NormalizedEmail`              |Zeichenfolge  |`aspnet_Membership.LoweredEmail`                  |Zeichenfolge  |
|`PhoneNumber`                  |Zeichenfolge  |`aspnet_Users.MobileAlias`                        |Zeichenfolge  |
|`LockoutEnabled`               |Bit     |`aspnet_Membership.IsLockedOut`                   |Bit     |

> [!NOTE]
> Nicht alle die feldzuordnungen ähneln 1: 1-Beziehungen aus der Mitgliedschaft in ASP.NET Core Identity. Die obigen Tabelle wird das Standardschema für den Mitgliedschaftsbenutzer und ordnet es dem ASP.NET Core Identity-Schema. Andere benutzerdefinierte Felder, die für die Mitgliedschaft verwendet wurden, müssen manuell zugeordnet werden soll. In dieser Zuordnung ist keine Zuordnung für Kennwörter, wie sowohl Kriterien für ein Kennwort und Kennwort Salze nicht zwischen den beiden migrieren. **Es wird das Kennwort als Null lassen und Benutzer dazu auffordern, ihre Kennwörter zurücksetzen, empfohlen.** In ASP.NET Core Identity kann `LockoutEnd` sollte auf einen Zeitpunkt in der Zukunft festgelegt werden, wenn der Benutzer gesperrt ist. Dies wird in das Migrationsskript gezeigt.

### <a name="roles"></a>Rollen

|*Identity<br>(dbo.AspNetRoles)*        ||*Membership<br>(dbo.aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Name des Felds**                 |**Type**|**Name des Felds**   |**Type**         |
|`Id`                           |Zeichenfolge  |`RoleId`         | Zeichenfolge          |
|`Name`                         |Zeichenfolge  |`RoleName`       | Zeichenfolge          |
|`NormalizedName`               |Zeichenfolge  |`LoweredRoleName`| Zeichenfolge          |

### <a name="user-roles"></a>Benutzerrollen

|*Identity<br>(dbo.AspNetUserRoles)*||*Membership<br>(dbo.aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Name des Felds**           |**Type**  |**Name des Felds**|**Type**                   |
|`RoleId`                 |Zeichenfolge    |`RoleId`      |Zeichenfolge                     |
|`UserId`                 |Zeichenfolge    |`UserId`      |Zeichenfolge                     |

Verweisen Sie die vorhergehenden Zuordnungstabellen aus, wenn ein Skript zur schemamigration für erstellen *Benutzer* und *Rollen*. Im folgende Beispiel wird davon ausgegangen, dass Sie zwei Datenbanken auf einem Datenbankserver verfügen. Eine Datenbank enthält, die vorhandenen ASP.NET Membership-Schema und Daten. Die andere *CoreIdentitySample* Datenbank erstellt wurde, verwenden die oben beschriebene Schritte ausführen. Kommentare sind Inlineschemainformationen enthält weitere Details.

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

Nach Abschluss des Vorgangs des vorherigen Skripts wird die zuvor erstellten ASP.NET Core Identity-app mit Mitgliedschaftsbenutzern aufgefüllt. Benutzer müssen ihre Kennwörter zu ändern, vor der Anmeldung.

> [!NOTE]
> Hätte das Mitgliedschaftssystem für Benutzer mit Benutzernamen, die nicht ihre e-Mail-Adresse übereinstimmt, sind Änderungen erforderlich, um die app erstellt haben weiter oben, um dies zu berücksichtigen. Die Standardvorlage erwartet `UserName` und `Email` identisch sein. Melden Sie sich erneut für Situationen, in denen sie verschiedene sind, muss geändert werden, um verwenden `UserName` anstelle von `Email`.

In der `PageModel` der Anmeldeseite, am *Pages\Account\Login.cshtml.cs*, entfernen Sie die `[EmailAddress]` -Attribut aus der *-e-Mail* Eigenschaft. Benennen Sie sie in *Benutzername*. Dies erfordert eine Änderung immer `EmailAddress` erwähnt wird, in der *Ansicht* und *"pagemodel"*. Das Ergebnis sieht folgendermaßen aus:

 ![Fester Benutzername](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Benutzer aus der SQL-Mitgliedschaft in ASP.NET Core 2.0-Identitätsanbieter zu portieren. Weitere Informationen zu ASP.NET Core Identity finden Sie unter [Einführung in die Identität](xref:security/authentication/identity).
