---
title: Migrieren von der ASP.net-Mitgliedschafts Authentifizierung zu ASP.net Core 2,0Identity
author: isaac2004
description: Erfahren Sie, wie Sie vorhandene ASP.net-apps mithilfe der Mitgliedschafts Authentifizierung zu ASP.net Core 2,0 migrieren Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: f039772f4276d0e8bcec2629350eba2ec0e7418c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399685"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="b6590-103">Migrieren von der ASP.net-Mitgliedschafts Authentifizierung zu ASP.net Core 2,0Identity</span><span class="sxs-lookup"><span data-stu-id="b6590-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="b6590-104">Von [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="b6590-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="b6590-105">In diesem Artikel wird das Migrieren des Datenbankschemas für ASP.net-apps mithilfe der Mitgliedschafts Authentifizierung zum Identity ASP.net Core 2,0</span><span class="sxs-lookup"><span data-stu-id="b6590-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="b6590-106">Dieses Dokument enthält die erforderlichen Schritte zum Migrieren des Datenbankschemas für ASP.net-Mitgliedschafts basierte apps zu dem für ASP.net Core verwendeten Datenbankschema Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="b6590-107">Weitere Informationen zum Migrieren von der ASP.net-Mitgliedschafts basierten Authentifizierung zu ASP.net finden Sie unter Identity [Migrieren einer vorhandenen APP aus Identity einer SQL-Mitgliedschaft zu ASP.net ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="b6590-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="b6590-108">Weitere Informationen zu ASP.net Core Identity finden Sie unter [Introduction to Identity on ASP.net Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="b6590-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="b6590-109">Überprüfen des Mitgliedschafts Schemas</span><span class="sxs-lookup"><span data-stu-id="b6590-109">Review of Membership schema</span></span>

<span data-ttu-id="b6590-110">Vor ASP.NET 2,0 waren die Entwickler dafür zuständig, den gesamten Authentifizierungs-und Autorisierungs Prozess für Ihre apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b6590-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="b6590-111">Mit ASP.NET 2,0 wurde die Mitgliedschaft eingeführt, die eine Bausteine-Lösung für die Handhabung der Sicherheit innerhalb von ASP.net-apps bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b6590-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="b6590-112">Entwickler waren nun in der Lage, ein Schema mit dem [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) -Befehl in eine SQL Server Datenbank zu bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b6590-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="b6590-113">Nachdem Sie diesen Befehl ausgeführt haben, wurden die folgenden Tabellen in der-Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="b6590-113">After running this command, the following tables were created in the database.</span></span>

  ![Mitgliedschafts Tabellen](identity/_static/membership-tables.png)

<span data-ttu-id="b6590-115">Zum Migrieren vorhandener apps zu ASP.net Core 2,0 müssen Identity die Daten in diesen Tabellen zu den vom neuen Schema verwendeten Tabellen migriert werden Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="b6590-116">IdentitySchema ASP.net Core 2,0</span><span class="sxs-lookup"><span data-stu-id="b6590-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="b6590-117">ASP.net Core 2,0 befolgt das [Identity](/aspnet/identity/index) in ASP.NET 4,5 eingeführte Prinzip.</span><span class="sxs-lookup"><span data-stu-id="b6590-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="b6590-118">Obwohl das Prinzip freigegeben ist, ist die Implementierung zwischen den Frameworks anders, auch bei ASP.net Core Versionen (siehe [Migrieren der Authentifizierung und Identity ASP.net Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="b6590-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="b6590-119">Die schnellste Möglichkeit, das Schema für ASP.net Core 2,0 anzuzeigen Identity , besteht darin, eine neue ASP.net Core 2,0-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b6590-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="b6590-120">Führen Sie diese Schritte in Visual Studio 2017 aus:</span><span class="sxs-lookup"><span data-stu-id="b6590-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="b6590-121">Klicken Sie auf **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b6590-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b6590-122">Erstellen Sie ein neues **ASP.net Core Webanwendungs** Projekt mit dem Namen *coreidentitysample*.</span><span class="sxs-lookup"><span data-stu-id="b6590-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="b6590-123">Wählen Sie in der Dropdown Liste **ASP.net Core 2,0** aus, und wählen Sie dann **Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="b6590-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="b6590-124">Diese Vorlage erzeugt eine [ Razor pages](xref:razor-pages/index) -app.</span><span class="sxs-lookup"><span data-stu-id="b6590-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b6590-125">Bevor Sie auf **OK**klicken, klicken Sie auf **Authentifizierung ändern**.</span><span class="sxs-lookup"><span data-stu-id="b6590-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="b6590-126">Wählen Sie **einzelne Benutzerkonten** für die Identity Vorlagen aus.</span><span class="sxs-lookup"><span data-stu-id="b6590-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="b6590-127">Klicken Sie abschließend auf **OK**und dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b6590-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="b6590-128">Visual Studio erstellt mithilfe der ASP.net Core Vorlage ein Projekt Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="b6590-129">Klicken **Sie**  >  auf Extras**nuget-Paket-Manager**  >  **Paket-Manager-Konsole** , um das Fenster der Paket-Manager- **Konsole** (PMC)</span><span class="sxs-lookup"><span data-stu-id="b6590-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="b6590-130">Navigieren Sie in der PMC zum Stammverzeichnis des Projekts, und führen Sie den Befehl [Entity Framework (EF) Core](/ef/core) aus `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="b6590-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="b6590-131">ASP.net Core 2,0 Identity verwendet EF Core, um mit der Datenbank zu interagieren, in der die Authentifizierungsdaten gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b6590-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="b6590-132">Damit die neu erstellte APP funktioniert, muss eine Datenbank vorhanden sein, um diese Daten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b6590-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="b6590-133">Nach dem Erstellen einer neuen App besteht die schnellste Möglichkeit zum Überprüfen des Schemas in einer Datenbankumgebung darin, die Datenbank mithilfe [EF Core Migrationen](/ef/core/managing-schemas/migrations/)zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b6590-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="b6590-134">Bei diesem Vorgang wird eine Datenbank erstellt, entweder lokal oder an anderer Stelle, die das Schema imitiert.</span><span class="sxs-lookup"><span data-stu-id="b6590-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="b6590-135">Weitere Informationen finden Sie in der vorangehenden Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="b6590-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="b6590-136">EF Core Befehle verwenden die Verbindungs Zeichenfolge für die in *appsettings.json*angegebene Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b6590-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="b6590-137">Die folgende Verbindungs Zeichenfolge bezieht sich auf eine Datenbank auf dem *localhost* mit dem Namen *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="b6590-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="b6590-138">In dieser Einstellung ist EF Core für die Verwendung der `DefaultConnection` Verbindungs Zeichenfolge konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b6590-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="b6590-139">Wählen **View**Sie  >  **SQL Server-Objekt-Explorer**anzeigen aus.</span><span class="sxs-lookup"><span data-stu-id="b6590-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="b6590-140">Erweitern Sie den Knoten, der dem Datenbanknamen entspricht, der in der- `ConnectionStrings:DefaultConnection` Eigenschaft von *appsettings.json*angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="b6590-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="b6590-141">Der `Update-Database` Befehl hat die mit dem Schema angegebene Datenbank und alle Daten, die für die APP-Initialisierung erforderlich sind, erstellt.</span><span class="sxs-lookup"><span data-stu-id="b6590-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="b6590-142">Die folgende Abbildung zeigt die Tabellenstruktur, die mit den vorherigen Schritten erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b6590-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    <span data-ttu-id="b6590-143">![IdentitySpielern](identity/_static/identity-tables.png)</span><span class="sxs-lookup"><span data-stu-id="b6590-143">![Identity Tables](identity/_static/identity-tables.png)</span></span>

## <a name="migrate-the-schema"></a><span data-ttu-id="b6590-144">Migrieren des Schemas</span><span class="sxs-lookup"><span data-stu-id="b6590-144">Migrate the schema</span></span>

<span data-ttu-id="b6590-145">Es gibt feine Unterschiede in den Tabellenstrukturen und-Feldern sowohl für Mitgliedschaften als auch für ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="b6590-146">Das Muster wurde für die Authentifizierung/Autorisierung mit ASP.net-und ASP.net Core-apps erheblich geändert.</span><span class="sxs-lookup"><span data-stu-id="b6590-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="b6590-147">Die Schlüssel Objekte, die weiterhin mit verwendet werden, Identity sind *Benutzer* und *Rollen*.</span><span class="sxs-lookup"><span data-stu-id="b6590-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="b6590-148">Hier sind die Zuordnung von Tabellen für *Benutzer*, *Rollen*und *Benutzer Rollen*.</span><span class="sxs-lookup"><span data-stu-id="b6590-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="b6590-149">Benutzer</span><span class="sxs-lookup"><span data-stu-id="b6590-149">Users</span></span>

|<span data-ttu-id="b6590-150">*Identity<br>dbo. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="b6590-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="b6590-151">*Mitgliedschaft <br> (dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="b6590-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="b6590-152">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-152">**Field Name**</span></span>                 |<span data-ttu-id="b6590-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-153">**Type**</span></span>|<span data-ttu-id="b6590-154">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-154">**Field Name**</span></span>                                    |<span data-ttu-id="b6590-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="b6590-156">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="b6590-157">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="b6590-158">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="b6590-159">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="b6590-160">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="b6590-161">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="b6590-162">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="b6590-163">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="b6590-164">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="b6590-165">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="b6590-166">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="b6590-167">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="b6590-168">bit</span><span class="sxs-lookup"><span data-stu-id="b6590-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="b6590-169">bit</span><span class="sxs-lookup"><span data-stu-id="b6590-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="b6590-170">Nicht alle Feld Zuordnungen ähneln eins-zu-eins-Beziehungen von der Mitgliedschaft zum ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="b6590-171">In der vorangehenden Tabelle wird das Standardschema für Mitgliedschafts Benutzer und das Schema der ASP.net Core Identity Schema zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b6590-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="b6590-172">Alle anderen benutzerdefinierten Felder, die für die Mitgliedschaft verwendet wurden, müssen manuell zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="b6590-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="b6590-173">In dieser Zuordnung gibt es keine Zuordnung für Kenn Wörter, da sowohl Kenn Wort Kriterien als auch Kenn Wort Salze nicht zwischen den beiden migriert werden.</span><span class="sxs-lookup"><span data-stu-id="b6590-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="b6590-174">**Es wird empfohlen, das Kennwort als Null zu belassen und Benutzer aufzufordern, ihre Kenn Wörter zurückzusetzen.**</span><span class="sxs-lookup"><span data-stu-id="b6590-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="b6590-175">In ASP.net Core Identity `LockoutEnd` sollte in der Zukunft auf ein Datum festgelegt werden, wenn der Benutzer gesperrt ist. Dies wird im Migrations Skript gezeigt.</span><span class="sxs-lookup"><span data-stu-id="b6590-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="b6590-176">Rollen</span><span class="sxs-lookup"><span data-stu-id="b6590-176">Roles</span></span>

|<span data-ttu-id="b6590-177">*Identity<br>dbo. Aspnettroles)*</span><span class="sxs-lookup"><span data-stu-id="b6590-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="b6590-178">*Mitgliedschaft <br> (dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="b6590-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="b6590-179">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-179">**Field Name**</span></span>                 |<span data-ttu-id="b6590-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-180">**Type**</span></span>|<span data-ttu-id="b6590-181">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-181">**Field Name**</span></span>   |<span data-ttu-id="b6590-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="b6590-183">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-183">string</span></span>  |`RoleId`         | <span data-ttu-id="b6590-184">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="b6590-185">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-185">string</span></span>  |`RoleName`       | <span data-ttu-id="b6590-186">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="b6590-187">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="b6590-188">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="b6590-189">Benutzerrollen</span><span class="sxs-lookup"><span data-stu-id="b6590-189">User Roles</span></span>

|<span data-ttu-id="b6590-190">*Identity<br>dbo. AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="b6590-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="b6590-191">*Mitgliedschaft <br> (dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="b6590-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="b6590-192">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-192">**Field Name**</span></span>           |<span data-ttu-id="b6590-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-193">**Type**</span></span>  |<span data-ttu-id="b6590-194">**Feldname**</span><span class="sxs-lookup"><span data-stu-id="b6590-194">**Field Name**</span></span>|<span data-ttu-id="b6590-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="b6590-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="b6590-196">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-196">string</span></span>    |`RoleId`      |<span data-ttu-id="b6590-197">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="b6590-198">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-198">string</span></span>    |`UserId`      |<span data-ttu-id="b6590-199">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b6590-199">string</span></span>                     |

<span data-ttu-id="b6590-200">Verweisen Sie auf die vorangehenden Mapping-Tabellen, wenn Sie ein Migrations Skript für *Benutzer* und *Rollen*erstellen.</span><span class="sxs-lookup"><span data-stu-id="b6590-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="b6590-201">Im folgenden Beispiel wird davon ausgegangen, dass Sie zwei Datenbanken auf einem Datenbankserver haben.</span><span class="sxs-lookup"><span data-stu-id="b6590-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="b6590-202">Eine Datenbank enthält das vorhandene ASP.net-Mitgliedschafts Schema und die Daten.</span><span class="sxs-lookup"><span data-stu-id="b6590-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="b6590-203">Die andere *coreidentitysample* -Datenbank wurde mithilfe der zuvor beschriebenen Schritte erstellt.</span><span class="sxs-lookup"><span data-stu-id="b6590-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="b6590-204">Kommentare sind Inline enthalten, um weitere Informationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="b6590-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="b6590-205">Nachdem das vorherige Skript abgeschlossen wurde, wird die Identity zuvor erstellte ASP.net Core-App mit Mitgliedschafts Benutzern aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="b6590-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="b6590-206">Benutzer müssen ihre Kenn Wörter ändern, bevor Sie sich anmelden.</span><span class="sxs-lookup"><span data-stu-id="b6590-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="b6590-207">Wenn das Mitgliedschaftssystem Benutzer mit Benutzernamen enthielt, die nicht mit der e-Mail-Adresse übereinstimmen, sind Änderungen an der zuvor erstellten App erforderlich, um dies zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="b6590-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="b6590-208">Die Standardvorlage erwartet `UserName` `Email` , dass und identisch sind.</span><span class="sxs-lookup"><span data-stu-id="b6590-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="b6590-209">In Situationen, in denen Sie sich unterscheiden, muss der Anmeldevorgang so geändert werden, dass `UserName` anstelle von verwendet wird `Email` .</span><span class="sxs-lookup"><span data-stu-id="b6590-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="b6590-210">Entfernen Sie auf der `PageModel` Anmeldeseite, die sich unter *Pages\Account\Login.cshtml.cs*befindet, das `[EmailAddress]` -Attribut aus der *Email-* Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b6590-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="b6590-211">Benennen Sie ihn in *username*um.</span><span class="sxs-lookup"><span data-stu-id="b6590-211">Rename it to *UserName*.</span></span> <span data-ttu-id="b6590-212">Dies erfordert eine Änderung, wo immer `EmailAddress` erwähnt wird, in der *Ansicht* und im Seiten *Modell*.</span><span class="sxs-lookup"><span data-stu-id="b6590-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="b6590-213">Das Ergebnis sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="b6590-213">The result looks like the following:</span></span>

 ![Anmeldung korrigiert](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="b6590-215">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b6590-215">Next steps</span></span>

<span data-ttu-id="b6590-216">In diesem Tutorial haben Sie erfahren, wie Sie Benutzer aus der SQL-Mitgliedschaft in ASP.net Core 2,0 portieren Identity .</span><span class="sxs-lookup"><span data-stu-id="b6590-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="b6590-217">Weitere Informationen zu ASP.net Core Identity finden Sie unter [Einführung in Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="b6590-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
