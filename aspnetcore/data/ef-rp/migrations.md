---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Migrationen (4 von 8)'
author: rick-anderson
description: In diesem Tutorial verwenden Sie zunächst das EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen in einer ASP.NET Core MVC-App.
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 5848e5e1e45708c3ab5c2a79614111662701aa77
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320159"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="4f8e3-103">Razor-Seiten mit EF Core in ASP.NET Core: Migrationen (4 von 8)</span><span class="sxs-lookup"><span data-stu-id="4f8e3-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4f8e3-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4f8e3-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="4f8e3-105">In diesem Tutorial wird das EF Core-Migrationsfeature zur Verwaltung von Datenmodelländerungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="4f8e3-106">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="4f8e3-107">Wenn eine neue App entwickelt wird, ändert sich das Datenmodell häufig.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="4f8e3-108">Nach jeder Modelländerung ist das Modell nicht mehr synchron mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="4f8e3-109">In diesem Tutorial wurde zunächst Entity Framework für die Erstellung der Datenbank konfiguriert, falls diese nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="4f8e3-110">Bei jeder Datenmodelländerung:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-110">Each time the data model changes:</span></span>

* <span data-ttu-id="4f8e3-111">Wird die Datenbank gelöscht.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-111">The DB is dropped.</span></span>
* <span data-ttu-id="4f8e3-112">Erstellt EF eine neue, dem Modell entsprechende Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="4f8e3-113">Füllt die App die Datenbank mit Testdaten auf.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="4f8e3-114">Dieser Ansatz, bei dem die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die App für die Produktion bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="4f8e3-115">Wenn die App in der Produktionsumgebung ausgeführt wird, speichert sie in der Regel Daten, die verwaltet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="4f8e3-116">Jedes Mal, wenn eine Änderung vorgenommen wird (z.B. wenn eine neue Spalte hinzugefügt wird), kann die App nicht mit einer Testdatenbank starten.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="4f8e3-117">Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF Core das Datenbankschema aktualisiert, anstatt eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="4f8e3-118">Statt die Datenbank bei den Datenmodelländerungen zu löschen und neu zu erstellen, wird bei der Migration eine Aktualisierung des Schemas und eine Beibehaltung vorhandener Daten angestrebt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="4f8e3-119">Löschen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="4f8e3-119">Drop the database</span></span>

<span data-ttu-id="4f8e3-120">Verwenden Sie den **SQL Server-Objekt-Explorer** (SSOX) oder den Befehl `database drop`:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4f8e3-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f8e3-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f8e3-122">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="4f8e3-123">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4f8e3-124">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4f8e3-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4f8e3-125">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="4f8e3-126">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="4f8e3-127">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="4f8e3-128">Erstellen einer ursprünglichen Migration und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="4f8e3-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="4f8e3-129">Erstellen Sie das Projekt und die erste Migration.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4f8e3-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f8e3-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4f8e3-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4f8e3-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="4f8e3-132">Überprüfen der Methoden „Up“ und „Down“</span><span class="sxs-lookup"><span data-stu-id="4f8e3-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="4f8e3-133">Der EF Core-Befehl `migrations add` hat Code generiert, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="4f8e3-134">Dieser Migrationscode ist in der Datei *Migrations\<timestamp>_InitialCreate.cs* enthalten.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4f8e3-135">Die Methode `Up` der Klasse `InitialCreate` erstellt die Datenbanktabellen, die den Datenmodellentitätenmengen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="4f8e3-136">Die Methode `Down` löscht diese, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="4f8e3-137">Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="4f8e3-138">Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="4f8e3-139">Der vorangehende Code ist für die ursprüngliche Migration bestimmt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="4f8e3-140">Dieser Code wurde bei der Ausführung des Befehls `migrations add InitialCreate` erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="4f8e3-141">Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="4f8e3-142">Der Migrationsname kann ein beliebiger gültiger Dateiname sein.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="4f8e3-143">Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="4f8e3-144">Eine Migration, bei der eine Tabelle mit Fachbereichen hinzugefügt wurde, könnte beispielsweise als „AddDepartmentTable“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="4f8e3-145">Wenn die ursprüngliche Migration erstellt wird und die Datenbank vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="4f8e3-146">Wird der Code für die Datenbankerstellung generiert.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="4f8e3-147">Muss der Code für die Datenbankerstellung nicht ausgeführt werden, da die Datenbank bereits mit dem Datenmodell übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="4f8e3-148">Wenn der Code für die Datenbankerstellung ausgeführt wird, werden keine Änderungen vorgenommen, da die Datenbank bereits mit dem Datenmodell übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="4f8e3-149">Wenn die App in einer neuen Umgebung bereitgestellt wird, muss der Code für die Datenbankerstellung ausgeführt werden, damit die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="4f8e3-150">Die Datenbank wurde zuvor gelöscht und ist daher nicht vorhanden, darum erstellt die Migration eine neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="4f8e3-151">Die Momentaufnahme des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="4f8e3-151">The data model snapshot</span></span>

<span data-ttu-id="4f8e3-152">Die Migration erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="4f8e3-153">Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="4f8e3-154">Verwenden Sie folgenden Befehl, um eine Migration zu löschen:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4f8e3-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f8e3-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f8e3-156">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="4f8e3-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4f8e3-157">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4f8e3-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="4f8e3-158">Weitere Informationen finden Sie unter [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="4f8e3-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="4f8e3-159">Der Befehl „Remove-Migration“ löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="4f8e3-160">Entfernen von EnsureCreated und Testen der App</span><span class="sxs-lookup"><span data-stu-id="4f8e3-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="4f8e3-161">Zu einem frühen Entwicklungszeitpunkt wurde `EnsureCreated` verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="4f8e3-162">In diesem Tutorial werden Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="4f8e3-163">Der Befehl `EnsureCreated` weist folgende Einschränkungen auf:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="4f8e3-164">Er umgeht Migrationen und erstellt die Datenbank und das Schema.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="4f8e3-165">Er erstellt keine Migrationstabelle.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="4f8e3-166">Er kann *nicht* mit Migrationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="4f8e3-167">Er dient zum Testen oder schnellen Erstellen von Prototypen an Positionen, an denen die Datenbank häufig gelöscht und neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="4f8e3-168">Entfernen Sie die folgende Zeile aus `DbInitializer`:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-168">Remove the following line from `DbInitializer`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="4f8e3-169">Führen Sie die App aus, und stellen Sie sicher, dass für die Datenbank ein Seeding durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="4f8e3-170">Untersuchen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="4f8e3-170">Inspect the database</span></span>

<span data-ttu-id="4f8e3-171">Verwenden Sie den **SQL Server-Objekt-Explorer** zur Untersuchung der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="4f8e3-172">Beachten Sie die zusätzliche Tabelle`__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="4f8e3-173">In der Tabelle `__EFMigrationsHistory` wird nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="4f8e3-174">Wenn Sie die Daten in der Tabelle `__EFMigrationsHistory` anzeigen, wird eine Zeile für die erste Migration angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="4f8e3-175">Im letzten Protokoll im vorherigen Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="4f8e3-176">Führen Sie die App aus, und überprüfen Sie, ob alles funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="4f8e3-177">Anwenden von Migrationen in der Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="4f8e3-177">Applying migrations in production</span></span>

<span data-ttu-id="4f8e3-178">Es wird empfohlen, dass Produktions-Apps [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart **nicht** aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="4f8e3-179">`Migrate` sollte in der Serverfarm nicht über eine App aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="4f8e3-180">Beispielsweise wenn die App über eine Cloud mit horizontaler Skalierung bereitgestellt wurde (mehrere Instanzen der App werden ausgeführt).</span><span class="sxs-lookup"><span data-stu-id="4f8e3-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="4f8e3-181">Die Datenbankmigration sollte im Rahmen der Bereitstellung und auf kontrollierte Weise erfolgen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="4f8e3-182">Zu den Ansätzen für die Migration von Produktionsdatenbanken zählen die folgenden:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="4f8e3-183">Verwendung von Migrationen zur Erstellung von SQL-Skripts und Verwendung der SQL-Skripts bei der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="4f8e3-184">Ausführen von `dotnet ef database update` über eine kontrollierte Umgebung.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="4f8e3-185">EF Core kann über die Tabelle `__MigrationsHistory` sehen, ob Migrationen ausgeführt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="4f8e3-186">Wenn die Datenbank auf dem neuesten Stand ist, wird keine Migration ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4f8e3-187">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="4f8e3-187">Troubleshooting</span></span>

<span data-ttu-id="4f8e3-188">Laden Sie die [fertige App](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations) herunter.</span><span class="sxs-lookup"><span data-stu-id="4f8e3-188">Download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="4f8e3-189">Die App generiert folgende Ausnahme:</span><span class="sxs-lookup"><span data-stu-id="4f8e3-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="4f8e3-190">Projektmappe: Ausführen von `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="4f8e3-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="4f8e3-191">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4f8e3-191">Additional resources</span></span>

* [<span data-ttu-id="4f8e3-192">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="4f8e3-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="4f8e3-193">[.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="4f8e3-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="4f8e3-194">Paket-Manager-Konsole (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="4f8e3-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="4f8e3-195">[Zurück](xref:data/ef-rp/sort-filter-page)
> [Weiter](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="4f8e3-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
