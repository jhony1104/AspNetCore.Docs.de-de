---
title: 'Tutorial: Verwenden des Migrationsfeatures: ASP.NET MVC mit EF Core'
description: In diesem Tutorial verwenden Sie zunächst die EF Core-Migrationsfeatures für die Verwaltung von Datenmodelländerungen in einer ASP.NET Core MVC-Anwendung.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 6d4ed0e95499c30417e1cfd07f57de824a8a62ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265521"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="796ce-103">Tutorial: Verwenden des Migrationsfeatures: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="796ce-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="796ce-104">In diesem Tutorial verwenden Sie zunächst die EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen.</span><span class="sxs-lookup"><span data-stu-id="796ce-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="796ce-105">In späteren Tutorials fügen Sie weitere Migrationen hinzu, wenn Sie das Datenmodell ändern.</span><span class="sxs-lookup"><span data-stu-id="796ce-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="796ce-106">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="796ce-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="796ce-107">Erfahren Sie mehr über Migrationen</span><span class="sxs-lookup"><span data-stu-id="796ce-107">Learn about migrations</span></span>
> * <span data-ttu-id="796ce-108">Erfahren Sie mehr über NuGet-Migrationspakete</span><span class="sxs-lookup"><span data-stu-id="796ce-108">Learn about NuGet migration packages</span></span>
> * <span data-ttu-id="796ce-109">Ändern der Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="796ce-109">Change the connection string</span></span>
> * <span data-ttu-id="796ce-110">Erstellen einer ursprünglichen Migration</span><span class="sxs-lookup"><span data-stu-id="796ce-110">Create an initial migration</span></span>
> * <span data-ttu-id="796ce-111">Untersuchen Sie Up- und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="796ce-111">Examine Up and Down methods</span></span>
> * <span data-ttu-id="796ce-112">Erfahren Sie mehr über die Datenmodell-Momentaufnahme</span><span class="sxs-lookup"><span data-stu-id="796ce-112">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="796ce-113">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="796ce-113">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="796ce-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="796ce-114">Prerequisites</span></span>

* [<span data-ttu-id="796ce-115">ASP.NET Core MVC mit EF Core: Sortieren, Filtern, Paging (3 von 10)</span><span class="sxs-lookup"><span data-stu-id="796ce-115">Add sorting, filtering, and paging with EF Core in an ASP.NET Core MVC app</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="796ce-116">Informationen zu Migrationen</span><span class="sxs-lookup"><span data-stu-id="796ce-116">About migrations</span></span>

<span data-ttu-id="796ce-117">Wenn Sie eine neue Anwendung entwickeln, ändert sich Ihr Datenmodell häufig. Jedes Mal, wenn das Datenmodell geändert wird, ist es nicht mehr synchron mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="796ce-117">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="796ce-118">Sie haben zu Beginn dieser Tutorials Entity Framework für die Erstellung der Datenbank konfiguriert, sofern diese nicht vorhanden war.</span><span class="sxs-lookup"><span data-stu-id="796ce-118">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="796ce-119">Anschließend können Sie bei jeder Datenmodelländerung (beim Hinzufügen, Entfernen oder Ändern von Entitätsklassen oder beim Ändern Ihrer DbContext-Klasse) die Datenbank löschen. EF erstellt dann eine neue Datenbank, die dem Modell entspricht, und startet diese mit Testdaten.</span><span class="sxs-lookup"><span data-stu-id="796ce-119">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="796ce-120">Diese Methode, bei der die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die Anwendung für die Produktion bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="796ce-120">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="796ce-121">Wenn sich die Anwendung in der Produktion befindet, speichert sie in der Regel die Daten, die Sie weiter verwenden möchten, da nicht bei jeder Änderung, wie z.B. dem Hinzufügen einer neuen Spalte, alle Daten verloren gehen sollen.</span><span class="sxs-lookup"><span data-stu-id="796ce-121">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="796ce-122">Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF das Datenbankschema aktualisiert, statt eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="796ce-122">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="about-nuget-migration-packages"></a><span data-ttu-id="796ce-123">Informationen zu NuGet-Migrationspaketen</span><span class="sxs-lookup"><span data-stu-id="796ce-123">About NuGet migration packages</span></span>

<span data-ttu-id="796ce-124">Für die Arbeit mit Migrationen können Sie die **Paket-Manager-Konsole** (Package Manager Console, PMC) oder die Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="796ce-124">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="796ce-125">In diesen Tutorials wird die Verwendungsweise von CLI-Befehlen erläutert.</span><span class="sxs-lookup"><span data-stu-id="796ce-125">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="796ce-126">Informationen zur PMC finden Sie am [Ende dieses Tutorials](#pmc).</span><span class="sxs-lookup"><span data-stu-id="796ce-126">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="796ce-127">Die EF-Tools für die Befehlszeilenschnittstelle (CLI) werden unter [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="796ce-127">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="796ce-128">Fügen Sie das Paket zum Installieren wie dargestellt zu der `DotNetCliToolReference`-Sammlung in der *CSPROJ*-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="796ce-128">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="796ce-129">**Hinweis**: Sie müssen dieses Paket installieren, indem Sie die *CSPROJ*-Datei bearbeiten. Sie können den `install-package`-Befehl oder die Paket-Manager-GUI nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="796ce-129">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="796ce-130">Sie können die *CSPROJ*-Datei bearbeiten, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen klicken und dann auf **„ContosoUniversity.csproj“ bearbeiten** klicken.</span><span class="sxs-lookup"><span data-stu-id="796ce-130">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]

<span data-ttu-id="796ce-131">(Die Versionsnummern in diesem Beispiel waren zum Zeitpunkt der Verfassung des Tutorials aktuell.)</span><span class="sxs-lookup"><span data-stu-id="796ce-131">(The version numbers in this example were current when the tutorial was written.)</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="796ce-132">Ändern der Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="796ce-132">Change the connection string</span></span>

<span data-ttu-id="796ce-133">Ändern Sie in der Datei *appsettings.json* in der Verbindungszeichenfolge den Namen der Datenbank in „ContosoUniversity2“ oder in einen anderen Namen, den Sie auf Ihrem Computer noch nicht verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="796ce-133">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="796ce-134">Durch diese Änderung wird das Projekt eingerichtet, sodass bei der ersten Migration eine neue Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-134">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="796ce-135">Dies ist für die ersten Schritte mit Migrationen nicht erforderlich, aber Sie werden später feststellen, weshalb es sich empfiehlt, entsprechend vorzugehen.</span><span class="sxs-lookup"><span data-stu-id="796ce-135">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="796ce-136">Alternativ zum Ändern des Datenbanknamens können Sie die Datenbank auch löschen.</span><span class="sxs-lookup"><span data-stu-id="796ce-136">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="796ce-137">Verwenden Sie den **SQL Server-Objekt-Explorer** (SSOX) oder den CLI-Befehl `database drop`:</span><span class="sxs-lookup"><span data-stu-id="796ce-137">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="796ce-138">Im folgenden Abschnitt wird erläutert, wie CLI-Befehle ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="796ce-138">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="796ce-139">Erstellen einer ursprünglichen Migration</span><span class="sxs-lookup"><span data-stu-id="796ce-139">Create an initial migration</span></span>

<span data-ttu-id="796ce-140">Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="796ce-140">Save your changes and build the project.</span></span> <span data-ttu-id="796ce-141">Öffnen Sie anschließend ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="796ce-141">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="796ce-142">Sie können diesen Vorgang auf folgende Weise beschleunigen:</span><span class="sxs-lookup"><span data-stu-id="796ce-142">Here's a quick way to do that:</span></span>

* <span data-ttu-id="796ce-143">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie aus dem Kontextmenü die Option **Ordner in Datei-Explorer öffnen** aus.</span><span class="sxs-lookup"><span data-stu-id="796ce-143">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Menüelement „In Datei-Explorer öffnen“](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="796ce-145">Geben Sie in die Adressleiste „cmd“ ein, und drücken Sie die EINGABETASTE.</span><span class="sxs-lookup"><span data-stu-id="796ce-145">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Öffnen des Befehlsfensters](migrations/_static/open-command-window.png)

<span data-ttu-id="796ce-147">Geben Sie im Befehlsfenster folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="796ce-147">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="796ce-148">Im Befehlsfenster wird Ihnen eine Ausgabe wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="796ce-148">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="796ce-149">Wenn Ihnen die Fehlermeldung *Es wurde keine ausführbare Datei gefunden, die dem Befehl „dotnet-ef“ entspricht.* angezeigt wird, finden Sie unter [diesem Blogbeitrag](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) Hilfe zur Problembehandlung.</span><span class="sxs-lookup"><span data-stu-id="796ce-149">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="796ce-150">Wenn Ihnen die Fehlermeldung *Auf die Datei „ContosoUniversity.dll“ kann nicht zugegriffen werden, da sie von einem anderen Prozess verwendet wird.* angezeigt wird, suchen Sie in der Windows-Taskleiste nach dem Symbol „IIS Express“, klicken Sie mit der rechten Maustaste darauf, und klicken Sie anschließend auf **ContosoUniversity > Website beenden**.</span><span class="sxs-lookup"><span data-stu-id="796ce-150">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="796ce-151">Untersuchen Sie Up- und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="796ce-151">Examine Up and Down methods</span></span>

<span data-ttu-id="796ce-152">Wenn Sie den Befehl `migrations add` ausgeführt haben, hat EF den Code generiert, mit dem die Datenbank von Grund auf neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-152">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="796ce-153">Dieser Code befindet sich im Ordner *Migrationen* in der Datei *\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="796ce-153">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="796ce-154">Mit der Methode `Up` der `InitialCreate`-Klasse werden die Datenbanktabellen erstellt, die den Datenmodellentitäten entsprechen, und mit der Methode `Down` werden die Datenbanktabellen gelöscht (siehe folgendes Beispiel).</span><span class="sxs-lookup"><span data-stu-id="796ce-154">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="796ce-155">Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="796ce-155">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="796ce-156">Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.</span><span class="sxs-lookup"><span data-stu-id="796ce-156">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="796ce-157">Dieser Code ist für die ursprüngliche Migration vorgesehen, die bei der Eingabe des Befehls `migrations add InitialCreate` erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="796ce-157">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="796ce-158">Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet und kann einen beliebigen Namen haben.</span><span class="sxs-lookup"><span data-stu-id="796ce-158">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="796ce-159">Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-159">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="796ce-160">Sie können einer späteren Migration beispielsweise den Namen „AddDepartmentTable“ geben.</span><span class="sxs-lookup"><span data-stu-id="796ce-160">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="796ce-161">Wenn Sie die ursprüngliche Migration erstellt haben, als die Datenbank bereits vorhanden war, wird der Code für die Datenbankerstellung zwar generiert, allerdings muss er nicht ausgeführt werden, da die Datenbank bereits dem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="796ce-161">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="796ce-162">Wenn Sie die App in einer anderen Umgebung bereitstellen, in der die Datenbank noch nicht vorhanden ist, wird dieser Code ausgeführt, um Ihre Datenbank zu erstellen. Daher sollte dieser zunächst getestet werden.</span><span class="sxs-lookup"><span data-stu-id="796ce-162">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="796ce-163">Aus diesem Grund haben Sie den Namen der Datenbank zuvor in der Verbindungszeichenfolge geändert – damit eine Datenbank bei Migrationen von Grund auf neu erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="796ce-163">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="796ce-164">Die Momentaufnahme des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="796ce-164">The data model snapshot</span></span>

<span data-ttu-id="796ce-165">Die Migrationsfunktion erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="796ce-165">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="796ce-166">Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-166">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="796ce-167">Verwenden Sie den Befehl [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove), wenn Sie eine Migration löschen.</span><span class="sxs-lookup"><span data-stu-id="796ce-167">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="796ce-168">`dotnet ef migrations remove` löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-168">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="796ce-169">Weitere Informationen dazu, wie die Momentaufnahmedatei verwendet wird, finden Sie unter [EF Core Migrations in Team Environments (EF Core-Migrationen in Teamumgebungen)](/ef/core/managing-schemas/migrations/teams).</span><span class="sxs-lookup"><span data-stu-id="796ce-169">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="796ce-170">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="796ce-170">Apply the migration</span></span>

<span data-ttu-id="796ce-171">Geben Sie folgenden Befehl in das Befehlsfenster ein, um die Datenbank mit den darin enthaltenen Tabellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="796ce-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="796ce-172">Die Ausgabe des Befehls ähnelt der des Befehls `migrations add`. Der Unterschied besteht darin, dass Ihnen Protokolle für die SQL-Befehle angezeigt werden, mit denen die Datenbank eingerichtet wird.</span><span class="sxs-lookup"><span data-stu-id="796ce-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="796ce-173">In der folgenden Beispielausgabe werden die meisten Protokolle ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="796ce-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="796ce-174">Wenn diese Detailebene in Protokollnachrichten nicht angezeigt werden soll, können Sie die Protokollebene in der Datei *appsettings.Development.json* ändern.</span><span class="sxs-lookup"><span data-stu-id="796ce-174">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="796ce-175">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="796ce-175">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="796ce-176">Verwenden Sie den **SQL Server-Objekt-Explorer**, um die Datenbank wie im ersten Tutorial zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="796ce-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="796ce-177">Sie werden feststellen, dass die Tabelle „\_\_EFMigrationsHistory“ hinzugefügt wurde, in der nachverfolgt wird, welche Migrationen auf die Datenbank angewendet worden sind.</span><span class="sxs-lookup"><span data-stu-id="796ce-177">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="796ce-178">Wenn Sie die Daten in dieser Tabelle anzeigen, ist eine Zeile für die erste Migration zu sehen.</span><span class="sxs-lookup"><span data-stu-id="796ce-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="796ce-179">(Im letzten Protokoll im vorgehenden Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.)</span><span class="sxs-lookup"><span data-stu-id="796ce-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="796ce-180">Führen Sie die Anwendung aus, um zu überprüfen, ob alles wie gewohnt funktioniert.</span><span class="sxs-lookup"><span data-stu-id="796ce-180">Run the application to verify that everything still works the same as before.</span></span>

![Indexseite „Studenten“](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="796ce-182">Vergleich von CLI und PMC</span><span class="sxs-lookup"><span data-stu-id="796ce-182">Compare CLI and PMC</span></span>

<span data-ttu-id="796ce-183">Die EF-Tools für die Verwaltung von Migrationen sind über .NET Core-CLI-Befehle oder über PowerShell-Cmdlets im Visual Studio-Fenster **Paket-Manager-Console** (PMC) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="796ce-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="796ce-184">In diesem Tutorial wird die Verwendungsweise der CLI erläutert. Sie können aber auch die PMC verwenden, wenn Sie diese bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="796ce-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="796ce-185">Die EF-Befehle für die PMC-Befehle sind im Paket [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) enthalten.</span><span class="sxs-lookup"><span data-stu-id="796ce-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="796ce-186">Dieses Paket ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten, weshalb Sie keinen Paketverweis hinzufügen müssen, wenn Ihre App über einen Paketverweis für `Microsoft.AspNetCore.App` verfügt.</span><span class="sxs-lookup"><span data-stu-id="796ce-186">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="796ce-187">**Wichtig:** Dieses Paket ist nicht mit dem Paket identisch, das Sie für die CLI durch Bearbeitung der *CSPROJ*-Datei installiert haben.</span><span class="sxs-lookup"><span data-stu-id="796ce-187">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="796ce-188">Der Name dieses Pakets endet mit `Tools`, im Gegensatz zum Namen des CLI-Pakets, der mit `Tools.DotNet` endet.</span><span class="sxs-lookup"><span data-stu-id="796ce-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="796ce-189">Weitere Informationen zu CLI-Befehlen finden Sie unter [.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="796ce-189">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="796ce-190">Weitere Informationen zu den PMC-Befehlen finden Sie unter [Paket-Manager-Konsole (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="796ce-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="796ce-191">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="796ce-191">Get the code</span></span>

[<span data-ttu-id="796ce-192">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="796ce-192">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="796ce-193">Nächster Schritt</span><span class="sxs-lookup"><span data-stu-id="796ce-193">Next step</span></span>

<span data-ttu-id="796ce-194">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="796ce-194">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="796ce-195">Haben Sie mehr über Migrationen erfahren</span><span class="sxs-lookup"><span data-stu-id="796ce-195">Learned about migrations</span></span>
> * <span data-ttu-id="796ce-196">Haben Sie mehr über NuGet-Migrationspakete erfahren</span><span class="sxs-lookup"><span data-stu-id="796ce-196">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="796ce-197">Haben Sie die Verbindungszeichenfolge geändert</span><span class="sxs-lookup"><span data-stu-id="796ce-197">Changed the connection string</span></span>
> * <span data-ttu-id="796ce-198">Haben Sie eine ursprüngliche Migration erstellt</span><span class="sxs-lookup"><span data-stu-id="796ce-198">Created an initial migration</span></span>
> * <span data-ttu-id="796ce-199">Haben Sie Up- und Down-Methoden untersucht</span><span class="sxs-lookup"><span data-stu-id="796ce-199">Examined Up and Down methods</span></span>
> * <span data-ttu-id="796ce-200">Haben Sie mehr über die Datenmodell-Momentaufnahme erfahren</span><span class="sxs-lookup"><span data-stu-id="796ce-200">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="796ce-201">Haben Sie die Migration angewandt</span><span class="sxs-lookup"><span data-stu-id="796ce-201">Applied the migration</span></span>

<span data-ttu-id="796ce-202">Fahren Sie mit dem nächsten Artikel fort, um fortgeschrittenere Themen zum Erweitern des Datenmodells kennenzulernen.</span><span class="sxs-lookup"><span data-stu-id="796ce-202">Advance to the next article to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="796ce-203">Dabei werden Sie weitere Migrationen erstellen und anwenden.</span><span class="sxs-lookup"><span data-stu-id="796ce-203">Along the way you'll create and apply additional migrations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="796ce-204">ASP.NET Core MVC mit Entity Framework Core: Datenmodell (5 von 10)</span><span class="sxs-lookup"><span data-stu-id="796ce-204">Create and apply additional migrations</span></span>](complex-data-model.md)
