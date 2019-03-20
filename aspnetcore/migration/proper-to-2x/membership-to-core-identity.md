---
title: Migrieren von ASP.NET Membership-Authentifizierung zu ASP.NET Core 2.0-Identitätsanbieter
author: isaac2004
description: Informationen Sie zum Migrieren von vorhandener ASP.NET-Anwendungen mithilfe von Mitgliedschaft-Authentifizierung für ASP.NET Core 2.0-Identitätsanbieter.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264725"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="a9c28-103">Migrieren von ASP.NET Membership-Authentifizierung zu ASP.NET Core 2.0-Identitätsanbieter</span><span class="sxs-lookup"><span data-stu-id="a9c28-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="a9c28-104">Von [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="a9c28-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="a9c28-105">In diesem Artikel wird das Datenbankschema für ASP.NET-Apps mithilfe von Mitgliedschaft-Authentifizierung für ASP.NET Core 2.0-Identität migrieren.</span><span class="sxs-lookup"><span data-stu-id="a9c28-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="a9c28-106">Dieses Dokument enthält die erforderlichen Schritte zum Migrieren des Datenbankschemas für ASP.NET Membership-basierte apps des Datenbankschemas für ASP.NET Core Identity verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9c28-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="a9c28-107">Weitere Informationen zum Migrieren von ASP.NET Membership-basierte Authentifizierung zu ASP.NET Identity finden Sie unter [Migrieren einer vorhandenen app aus SQL-Mitgliedschaftsanbieter nach ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="a9c28-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="a9c28-108">Weitere Informationen zu ASP.NET Core Identity, finden Sie unter [Einführung in die Identität in ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="a9c28-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="a9c28-109">Überprüfung des mitgliedschaftsschemas</span><span class="sxs-lookup"><span data-stu-id="a9c28-109">Review of Membership schema</span></span>

<span data-ttu-id="a9c28-110">Vor ASP.NET 2.0 wurden Entwickler damit beauftragt, den Gesamtprozess für Authentifizierung und Autorisierung für ihre apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="a9c28-111">Mitgliedschaft wurde mit ASP.NET 2.0 eingeführt und Bereitstellung einer Lösung Codebausteine für die Behandlung von Sicherheit in ASP.NET-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="a9c28-112">Entwickler konnten für das Bootstrapping eines Schemas in SQL Server-Datenbank mit der [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) Befehl.</span><span class="sxs-lookup"><span data-stu-id="a9c28-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="a9c28-113">Nach der Ausführung dieses Befehls wurden in den folgenden Tabellen in der Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="a9c28-113">After running this command, the following tables were created in the database.</span></span>

  ![Mitgliedertabellen](identity/_static/membership-tables.png)

<span data-ttu-id="a9c28-115">Zum Migrieren von vorhandenen apps in ASP.NET Core 2.0-Identitätsanbieter müssen die Daten in diesen Tabellen in die von der neuen Identität Schemas verwendeten Tabellen migriert werden.</span><span class="sxs-lookup"><span data-stu-id="a9c28-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="a9c28-116">ASP.NET Core Identity-2.0-schema</span><span class="sxs-lookup"><span data-stu-id="a9c28-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="a9c28-117">ASP.NET Core 2.0 folgt die [Identität](/aspnet/identity/index) Prinzip, die in ASP.NET 4.5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="a9c28-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="a9c28-118">Aber das Prinzip freigegeben wird, die Implementierung zwischen den Frameworks unterscheidet auch zwischen Versionen von ASP.NET Core (finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="a9c28-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="a9c28-119">Die schnellste Möglichkeit zum Anzeigen des Schemas für ASP.NET Core 2.0-Identitätsanbieter ist die Erstellung eine neue ASP.NET Core 2.0-app.</span><span class="sxs-lookup"><span data-stu-id="a9c28-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="a9c28-120">In Visual Studio 2017, gehen Sie wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a9c28-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="a9c28-121">Wählen Sie **Datei** > **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="a9c28-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="a9c28-122">Erstellen Sie ein neues **ASP.NET Core-Webanwendung** Projekt mit dem Namen *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="a9c28-123">Wählen Sie **ASP.NET Core 2.0** in der Dropdownliste und wählen Sie dann **Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="a9c28-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="a9c28-124">Diese Vorlage erzeugt eine [Razor-Seiten](xref:razor-pages/index) app.</span><span class="sxs-lookup"><span data-stu-id="a9c28-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="a9c28-125">Bevor Sie auf **OK**, klicken Sie auf **Authentifizierung ändern**.</span><span class="sxs-lookup"><span data-stu-id="a9c28-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="a9c28-126">Wählen Sie **einzelne Benutzerkonten** für die Identity-Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="a9c28-127">Klicken Sie abschließend auf **OK**, klicken Sie dann **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9c28-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="a9c28-128">Visual Studio erstellt ein Projekt mithilfe der ASP.NET Core Identity-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a9c28-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="a9c28-129">Wählen Sie **Tools** > **NuGet Package Manager** > **-Paket-Manager-Konsole** zum Öffnen der **-Paket-Manager-Konsole** Fenster "(PMC).</span><span class="sxs-lookup"><span data-stu-id="a9c28-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="a9c28-130">Navigieren Sie zu das Stammverzeichnis des Projekts in PMC ein, und führen die [Entity Framework (EF) Core](/ef/core) `Update-Database` Befehl.</span><span class="sxs-lookup"><span data-stu-id="a9c28-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="a9c28-131">EF Core wird von ASP.NET Core 2.0-Identität verwendet, um die Interaktion mit der Datenbank, die Speichern der Authentifizierungsdaten.</span><span class="sxs-lookup"><span data-stu-id="a9c28-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="a9c28-132">In der Reihenfolge für die neu erstellte app funktioniert muss es als Datenbank zum Speichern dieser Daten.</span><span class="sxs-lookup"><span data-stu-id="a9c28-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="a9c28-133">Nach dem Erstellen einer neuen app, die schnellste Möglichkeit, die das Schema in einer datenbankumgebung zu überprüfen ist die Erstellung der Datenbank mithilfe [EF Core-Migrationen](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="a9c28-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="a9c28-134">Dieser Vorgang erstellt eine Datenbank, entweder lokal oder an anderer Stelle die imitiert dieses Schema.</span><span class="sxs-lookup"><span data-stu-id="a9c28-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="a9c28-135">Überprüfen Sie die obige Dokumentation für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="a9c28-136">EF Core-Befehle verwenden die Verbindungszeichenfolge für die Datenbank, die im angegebenen *"appSettings.JSON"*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="a9c28-137">Die folgende Verbindungszeichenfolge wird eine Datenbank auf *"localhost"* mit dem Namen *Asp-Net-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="a9c28-138">Mit dieser Einstellung ist EF Core zur Verwendung konfiguriert die `DefaultConnection` Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="a9c28-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="a9c28-139">Wählen Sie **Ansicht** > **Objekt-Explorer von SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="a9c28-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="a9c28-140">Erweitern Sie den Knoten für den Datenbanknamen angegeben werden, der `ConnectionStrings:DefaultConnection` Eigenschaft *"appSettings.JSON"*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="a9c28-141">Die `Update-Database` Befehl erstellt mit dem Schema der Datenbank und alle Daten, die für app-Initialisierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a9c28-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="a9c28-142">Die folgende Abbildung zeigt die Struktur der Tabelle, die mit den vorherigen Schritten erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a9c28-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Identity-Tabellen](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="a9c28-144">Migrieren des Schemas</span><span class="sxs-lookup"><span data-stu-id="a9c28-144">Migrate the schema</span></span>

<span data-ttu-id="a9c28-145">Es gibt feine Unterschiede in den Feldern für Mitgliedschafts- und ASP.NET Core Identity "und" Tabellenstrukturen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="a9c28-146">Das Muster für die Authentifizierung/Autorisierung in ASP.NET- und ASP.NET Core-apps wesentlich geändert.</span><span class="sxs-lookup"><span data-stu-id="a9c28-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="a9c28-147">Sind die wichtigsten Objekte, die immer noch mit der Identität verwendet werden *Benutzer* und *Rollen*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="a9c28-148">Nachfolgend finden Sie Mapping-Tabellen für *Benutzer*, *Rollen*, und *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="a9c28-149">Benutzer</span><span class="sxs-lookup"><span data-stu-id="a9c28-149">Users</span></span>

|<span data-ttu-id="a9c28-150">*Identity<br>(dbo.AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="a9c28-151">*Mitgliedschaft<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="a9c28-152">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-152">**Field Name**</span></span>                 |<span data-ttu-id="a9c28-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-153">**Type**</span></span>|<span data-ttu-id="a9c28-154">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-154">**Field Name**</span></span>                                    |<span data-ttu-id="a9c28-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="a9c28-156">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="a9c28-157">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="a9c28-158">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="a9c28-159">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="a9c28-160">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="a9c28-161">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="a9c28-162">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="a9c28-163">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="a9c28-164">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="a9c28-165">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="a9c28-166">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="a9c28-167">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="a9c28-168">Bit</span><span class="sxs-lookup"><span data-stu-id="a9c28-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="a9c28-169">Bit</span><span class="sxs-lookup"><span data-stu-id="a9c28-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="a9c28-170">Nicht alle die feldzuordnungen ähneln 1: 1-Beziehungen aus der Mitgliedschaft in ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="a9c28-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="a9c28-171">Die obigen Tabelle wird das Standardschema für den Mitgliedschaftsbenutzer und ordnet es dem ASP.NET Core Identity-Schema.</span><span class="sxs-lookup"><span data-stu-id="a9c28-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="a9c28-172">Andere benutzerdefinierte Felder, die für die Mitgliedschaft verwendet wurden, müssen manuell zugeordnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="a9c28-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="a9c28-173">In dieser Zuordnung ist keine Zuordnung für Kennwörter, wie sowohl Kriterien für ein Kennwort und Kennwort Salze nicht zwischen den beiden migrieren.</span><span class="sxs-lookup"><span data-stu-id="a9c28-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="a9c28-174">**Es wird das Kennwort als Null lassen und Benutzer dazu auffordern, ihre Kennwörter zurücksetzen, empfohlen.**</span><span class="sxs-lookup"><span data-stu-id="a9c28-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="a9c28-175">In ASP.NET Core Identity kann `LockoutEnd` sollte auf einen Zeitpunkt in der Zukunft festgelegt werden, wenn der Benutzer gesperrt ist. Dies wird in das Migrationsskript gezeigt.</span><span class="sxs-lookup"><span data-stu-id="a9c28-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="a9c28-176">Rollen</span><span class="sxs-lookup"><span data-stu-id="a9c28-176">Roles</span></span>

|<span data-ttu-id="a9c28-177">*Identity<br>(dbo.AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="a9c28-178">*Membership<br>(dbo.aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="a9c28-179">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-179">**Field Name**</span></span>                 |<span data-ttu-id="a9c28-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-180">**Type**</span></span>|<span data-ttu-id="a9c28-181">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-181">**Field Name**</span></span>   |<span data-ttu-id="a9c28-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="a9c28-183">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-183">string</span></span>  |`RoleId`         | <span data-ttu-id="a9c28-184">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="a9c28-185">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-185">string</span></span>  |`RoleName`       | <span data-ttu-id="a9c28-186">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="a9c28-187">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="a9c28-188">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="a9c28-189">Benutzerrollen</span><span class="sxs-lookup"><span data-stu-id="a9c28-189">User Roles</span></span>

|<span data-ttu-id="a9c28-190">*Identity<br>(dbo.AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="a9c28-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="a9c28-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="a9c28-192">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-192">**Field Name**</span></span>           |<span data-ttu-id="a9c28-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-193">**Type**</span></span>  |<span data-ttu-id="a9c28-194">**Name des Felds**</span><span class="sxs-lookup"><span data-stu-id="a9c28-194">**Field Name**</span></span>|<span data-ttu-id="a9c28-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="a9c28-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="a9c28-196">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-196">string</span></span>    |`RoleId`      |<span data-ttu-id="a9c28-197">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="a9c28-198">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-198">string</span></span>    |`UserId`      |<span data-ttu-id="a9c28-199">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a9c28-199">string</span></span>                     |

<span data-ttu-id="a9c28-200">Verweisen Sie die vorhergehenden Zuordnungstabellen aus, wenn ein Skript zur schemamigration für erstellen *Benutzer* und *Rollen*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="a9c28-201">Im folgende Beispiel wird davon ausgegangen, dass Sie zwei Datenbanken auf einem Datenbankserver verfügen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="a9c28-202">Eine Datenbank enthält, die vorhandenen ASP.NET Membership-Schema und Daten.</span><span class="sxs-lookup"><span data-stu-id="a9c28-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="a9c28-203">Die andere *CoreIdentitySample* Datenbank erstellt wurde, verwenden die oben beschriebene Schritte ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="a9c28-204">Kommentare sind Inlineschemainformationen enthält weitere Details.</span><span class="sxs-lookup"><span data-stu-id="a9c28-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="a9c28-205">Nach Abschluss des Vorgangs des vorherigen Skripts wird die zuvor erstellten ASP.NET Core Identity-app mit Mitgliedschaftsbenutzern aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="a9c28-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="a9c28-206">Benutzer müssen ihre Kennwörter zu ändern, vor der Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="a9c28-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="a9c28-207">Hätte das Mitgliedschaftssystem für Benutzer mit Benutzernamen, die nicht ihre e-Mail-Adresse übereinstimmt, sind Änderungen erforderlich, um die app erstellt haben weiter oben, um dies zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="a9c28-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="a9c28-208">Die Standardvorlage erwartet `UserName` und `Email` identisch sein.</span><span class="sxs-lookup"><span data-stu-id="a9c28-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="a9c28-209">Melden Sie sich erneut für Situationen, in denen sie verschiedene sind, muss geändert werden, um verwenden `UserName` anstelle von `Email`.</span><span class="sxs-lookup"><span data-stu-id="a9c28-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="a9c28-210">In der `PageModel` der Anmeldeseite, am *Pages\Account\Login.cshtml.cs*, entfernen Sie die `[EmailAddress]` -Attribut aus der *-e-Mail* Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="a9c28-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="a9c28-211">Benennen Sie sie in *Benutzername*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-211">Rename it to *UserName*.</span></span> <span data-ttu-id="a9c28-212">Dies erfordert eine Änderung immer `EmailAddress` erwähnt wird, in der *Ansicht* und *"pagemodel"*.</span><span class="sxs-lookup"><span data-stu-id="a9c28-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="a9c28-213">Das Ergebnis sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="a9c28-213">The result looks like the following:</span></span>

 ![Fester Benutzername](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="a9c28-215">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="a9c28-215">Next steps</span></span>

<span data-ttu-id="a9c28-216">In diesem Tutorial haben Sie gelernt, wie Sie Benutzer aus der SQL-Mitgliedschaft in ASP.NET Core 2.0-Identitätsanbieter zu portieren.</span><span class="sxs-lookup"><span data-stu-id="a9c28-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="a9c28-217">Weitere Informationen zu ASP.NET Core Identity finden Sie unter [Einführung in die Identität](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="a9c28-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
