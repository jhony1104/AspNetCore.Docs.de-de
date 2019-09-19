---
title: 'Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8'
author: tdykstra
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 107b348b4484301b86eeb5528833914fe4c1eaf7
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080925"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="65a05-103">Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8</span><span class="sxs-lookup"><span data-stu-id="65a05-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="65a05-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65a05-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65a05-105">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core in einer ASP.NET Core Razor Pages-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an ASP.NET Core Razor Pages app.</span></span> <span data-ttu-id="65a05-106">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="65a05-107">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="65a05-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span>

[<span data-ttu-id="65a05-108">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="65a05-108">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="65a05-109">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="65a05-109">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65a05-110">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="65a05-110">Prerequisites</span></span>

* <span data-ttu-id="65a05-111">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialserie [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="65a05-111">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="65a05-114">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="65a05-114">Database engines</span></span>

<span data-ttu-id="65a05-115">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="65a05-115">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="65a05-116">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="65a05-116">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="65a05-117">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="65a05-117">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="65a05-118">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="65a05-118">Troubleshooting</span></span>

<span data-ttu-id="65a05-119">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="65a05-119">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="65a05-120">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="65a05-120">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="65a05-121">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="65a05-121">The sample app</span></span>

<span data-ttu-id="65a05-122">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="65a05-122">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="65a05-123">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="65a05-124">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-124">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="65a05-127">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="65a05-127">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="65a05-128">Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="65a05-128">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="65a05-129">Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-129">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="65a05-130">Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="65a05-130">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="65a05-131">Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="65a05-131">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65a05-133">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-133">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="65a05-134">Löschen Sie drei Dateien und einen Ordner, die *SQLite* im Namen tragen.</span><span class="sxs-lookup"><span data-stu-id="65a05-134">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="65a05-135">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="65a05-135">Build the project.</span></span>
* <span data-ttu-id="65a05-136">Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="65a05-137">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65a05-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="65a05-139">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="65a05-140">Löschen Sie *ContosoUniversity.csproj*, und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.</span><span class="sxs-lookup"><span data-stu-id="65a05-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="65a05-141">Löschen Sie *Startup.cs*, und benennen Sie *StartupSQLite.cs* in *Startup.cs* um.</span><span class="sxs-lookup"><span data-stu-id="65a05-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="65a05-142">Löschen Sie *appSettings.json*, und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.</span><span class="sxs-lookup"><span data-stu-id="65a05-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="65a05-143">Löschen Sie den Ordner *Migrations*, und benennen Sie *MigrationsSQL* in *Migrations* um.</span><span class="sxs-lookup"><span data-stu-id="65a05-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="65a05-144">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="65a05-144">Build the project.</span></span>
* <span data-ttu-id="65a05-145">Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-145">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="65a05-146">Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-146">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="65a05-147">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65a05-147">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="65a05-148">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="65a05-148">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-150">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-150">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="65a05-151">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-151">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="65a05-152">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="65a05-152">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="65a05-153">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="65a05-153">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="65a05-154">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-154">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65a05-156">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="65a05-156">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="65a05-157">Führen Sie die folgenden Befehle aus, um ein Razor Pages `cd`-Projekt und den neuen Projektordner zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="65a05-157">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="65a05-158">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="65a05-158">Set up the site style</span></span>

<span data-ttu-id="65a05-159">Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:</span><span class="sxs-lookup"><span data-stu-id="65a05-159">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="65a05-160">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="65a05-160">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="65a05-161">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="65a05-161">There are three occurrences.</span></span>

* <span data-ttu-id="65a05-162">Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.</span><span class="sxs-lookup"><span data-stu-id="65a05-162">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="65a05-163">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="65a05-163">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="65a05-164">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="65a05-164">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="65a05-165">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-165">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="65a05-166">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="65a05-166">The data model</span></span>

<span data-ttu-id="65a05-167">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="65a05-167">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="65a05-169">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="65a05-169">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="65a05-170">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="65a05-170">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="65a05-172">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="65a05-172">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="65a05-173">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-173">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="65a05-174">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="65a05-174">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="65a05-175">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="65a05-175">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="65a05-176">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="65a05-176">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="65a05-177">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="65a05-177">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="65a05-178">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-178">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="65a05-179">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="65a05-179">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="65a05-180">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="65a05-180">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="65a05-181">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="65a05-181">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="65a05-182">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="65a05-182">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="65a05-183">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="65a05-183">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="65a05-184">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="65a05-184">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="65a05-185">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="65a05-185">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="65a05-186">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="65a05-186">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="65a05-187">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="65a05-187">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="65a05-188">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="65a05-188">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="65a05-190">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-190">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="65a05-191">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="65a05-191">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="65a05-192">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="65a05-192">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="65a05-193">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-193">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="65a05-194">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-194">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="65a05-195">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="65a05-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="65a05-196">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="65a05-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="65a05-197">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="65a05-197">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="65a05-198">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="65a05-199">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="65a05-199">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="65a05-200">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="65a05-201">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="65a05-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="65a05-202">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="65a05-202">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="65a05-203">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-203">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="65a05-204">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="65a05-204">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="65a05-205">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-205">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="65a05-206">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="65a05-206">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="65a05-208">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-208">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="65a05-209">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-209">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="65a05-210">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="65a05-210">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="65a05-211">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="65a05-211">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="65a05-212">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-212">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="65a05-213">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="65a05-213">Scaffold Student pages</span></span>

<span data-ttu-id="65a05-214">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="65a05-214">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="65a05-215">Eine EF Core *context*-Klasse.</span><span class="sxs-lookup"><span data-stu-id="65a05-215">An EF Core *context* class.</span></span> <span data-ttu-id="65a05-216">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="65a05-216">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="65a05-217">Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="65a05-217">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="65a05-218">Razor Pages, die Vorgänge zum Erstellen (Create), Lesen (Read), Aktualisieren (Update) und Löschen (Delete) (CRUD) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="65a05-218">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-220">Erstellen Sie einen Ordner *Students* im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="65a05-220">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="65a05-221">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-221">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="65a05-222">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **HINZUFÜGEN** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-222">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="65a05-223">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="65a05-223">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="65a05-224">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-224">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="65a05-225">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-225">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="65a05-226">Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="65a05-226">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="65a05-227">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-227">Select **Add**.</span></span>

<span data-ttu-id="65a05-228">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="65a05-228">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65a05-230">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="65a05-230">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
  dotnet add package Microsoft.Extensions.Logging.Debug --version 3.0.0-*
  ```

  <span data-ttu-id="65a05-231">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="65a05-231">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="65a05-232">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="65a05-232">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="65a05-233">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="65a05-233">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="65a05-234">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-234">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
  ```

* <span data-ttu-id="65a05-235">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-235">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="65a05-236">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="65a05-236">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="65a05-237">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="65a05-237">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="65a05-238">Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="65a05-238">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="65a05-239">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="65a05-239">The scaffolding process:</span></span>

* <span data-ttu-id="65a05-240">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="65a05-240">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="65a05-241">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-241">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="65a05-242">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-242">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="65a05-243">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-243">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="65a05-244">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-244">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="65a05-245">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-245">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="65a05-246">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="65a05-246">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="65a05-247">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="65a05-247">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="65a05-248">Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="65a05-248">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="65a05-249">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="65a05-249">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65a05-251">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="65a05-251">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="65a05-252">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-252">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="65a05-253">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="65a05-253">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="65a05-255">Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="65a05-255">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="65a05-256">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="65a05-256">Update the database context class</span></span>

<span data-ttu-id="65a05-257">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="65a05-257">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="65a05-258">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="65a05-258">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="65a05-259">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-259">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="65a05-260">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="65a05-260">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="65a05-261">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-261">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="65a05-262">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="65a05-262">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="65a05-263">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="65a05-263">In EF Core terminology:</span></span>

* <span data-ttu-id="65a05-264">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="65a05-264">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="65a05-265">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-265">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="65a05-266">Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen.</span><span class="sxs-lookup"><span data-stu-id="65a05-266">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="65a05-267">Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="65a05-267">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="65a05-268">Nehmen Sie eine globale Änderung im gesamten Projekt aus `_context.Student` in `_context.Students`vor, damit der Razor Pages-Code mit dem neuen DBSet-Namen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="65a05-268">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="65a05-269">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="65a05-269">There are 8 occurrences.</span></span>

<span data-ttu-id="65a05-270">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-270">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="65a05-271">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="65a05-271">Startup.cs</span></span>

<span data-ttu-id="65a05-272">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-272">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65a05-273">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="65a05-273">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="65a05-274">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-274">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="65a05-275">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="65a05-275">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="65a05-276">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="65a05-276">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-277">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-278">In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="65a05-278">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65a05-280">Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="65a05-280">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="65a05-281">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-281">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="65a05-282">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="65a05-282">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="65a05-283">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="65a05-283">Create the database</span></span>

<span data-ttu-id="65a05-284">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="65a05-284">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="65a05-285">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-285">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="65a05-286">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-286">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="65a05-287">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="65a05-287">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="65a05-288">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="65a05-288">Delete the database.</span></span> <span data-ttu-id="65a05-289">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="65a05-289">Any existing data is lost.</span></span>
* <span data-ttu-id="65a05-290">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="65a05-290">Change the data model.</span></span> <span data-ttu-id="65a05-291">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="65a05-291">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="65a05-292">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-292">Run the app.</span></span>
* <span data-ttu-id="65a05-293">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="65a05-293">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="65a05-294">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="65a05-294">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="65a05-295">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="65a05-295">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="65a05-296">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="65a05-296">When that is the case, use migrations.</span></span>

<span data-ttu-id="65a05-297">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="65a05-297">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="65a05-298">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-298">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="65a05-299">Testen der App</span><span class="sxs-lookup"><span data-stu-id="65a05-299">Test the app</span></span>

* <span data-ttu-id="65a05-300">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-300">Run the app.</span></span>
* <span data-ttu-id="65a05-301">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="65a05-301">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="65a05-302">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="65a05-302">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="65a05-303">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="65a05-303">Seed the database</span></span>

<span data-ttu-id="65a05-304">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="65a05-304">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="65a05-305">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="65a05-305">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="65a05-306">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-306">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="65a05-307">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-307">The code checks if there are any students in the database.</span></span> <span data-ttu-id="65a05-308">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="65a05-308">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="65a05-309">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-309">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="65a05-310">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="65a05-310">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65a05-312">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-312">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65a05-314">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="65a05-314">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="65a05-315">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="65a05-315">Restart the app.</span></span>

* <span data-ttu-id="65a05-316">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="65a05-316">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="65a05-317">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="65a05-317">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-318">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-318">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-319">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="65a05-319">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="65a05-320">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-320">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="65a05-321">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="65a05-321">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="65a05-322">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="65a05-322">Expand the **Tables** node.</span></span>
* <span data-ttu-id="65a05-323">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-323">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="65a05-324">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="65a05-324">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="65a05-326">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="65a05-326">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="65a05-327">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="65a05-327">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="65a05-328">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="65a05-328">Asynchronous code</span></span>

<span data-ttu-id="65a05-329">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="65a05-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="65a05-330">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="65a05-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="65a05-331">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="65a05-332">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="65a05-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="65a05-333">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="65a05-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="65a05-334">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="65a05-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="65a05-335">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="65a05-335">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="65a05-336">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="65a05-336">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="65a05-337">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-337">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="65a05-338">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="65a05-338">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="65a05-339">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-339">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="65a05-340">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-340">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="65a05-341">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="65a05-341">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="65a05-342">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="65a05-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="65a05-343">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="65a05-344">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="65a05-345">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="65a05-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="65a05-346">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="65a05-346">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="65a05-347">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="65a05-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="65a05-348">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="65a05-348">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="65a05-349">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="65a05-349">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="65a05-350">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65a05-350">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="65a05-351">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="65a05-351">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="65a05-352">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="65a05-352">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="65a05-353">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="65a05-353">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="65a05-354">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="65a05-354">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65a05-355">Die Beispiel-Web-App der Contoso University veranschaulicht, wie mit Entity Framework Core (EF Core) eine ASP.NET Core-App mit Razor Pages erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="65a05-355">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="65a05-356">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="65a05-356">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="65a05-357">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="65a05-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="65a05-358">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-358">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="65a05-359">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="65a05-359">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="65a05-360">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="65a05-360">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65a05-361">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="65a05-361">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-362">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-362">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-363">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-363">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="65a05-364">Kenntnisse über [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="65a05-364">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="65a05-365">Anfänger sollten den Artikel [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="65a05-365">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="65a05-366">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="65a05-366">Troubleshooting</span></span>

<span data-ttu-id="65a05-367">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="65a05-367">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="65a05-368">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-368">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="65a05-369">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="65a05-369">The Contoso University web app</span></span>

<span data-ttu-id="65a05-370">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="65a05-370">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="65a05-371">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-371">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="65a05-372">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-372">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="65a05-375">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="65a05-375">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="65a05-376">In diesem Tutorial wird Entity Framework Core mit Razor Pages thematisiert. Die Benutzeroberfläche wird nicht erläutert.</span><span class="sxs-lookup"><span data-stu-id="65a05-376">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="65a05-377">Erstellen der Razor Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="65a05-377">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-378">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-379">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-379">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="65a05-380">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="65a05-380">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="65a05-381">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="65a05-381">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="65a05-382">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-382">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="65a05-383">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-383">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="65a05-384">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="65a05-384">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="65a05-385">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-385">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="65a05-387">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="65a05-387">Set up the site style</span></span>

<span data-ttu-id="65a05-388">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="65a05-388">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="65a05-389">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="65a05-389">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="65a05-390">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="65a05-390">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="65a05-391">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="65a05-391">There are three occurrences.</span></span>

* <span data-ttu-id="65a05-392">Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="65a05-392">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="65a05-393">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="65a05-393">The changes are highlighted.</span></span> <span data-ttu-id="65a05-394">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="65a05-394">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="65a05-395">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="65a05-395">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="65a05-396">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="65a05-396">Create the data model</span></span>

<span data-ttu-id="65a05-397">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="65a05-397">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="65a05-398">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="65a05-398">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="65a05-400">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="65a05-400">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="65a05-401">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="65a05-401">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="65a05-402">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="65a05-402">A student can enroll in any number of courses.</span></span> <span data-ttu-id="65a05-403">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="65a05-403">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="65a05-404">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-404">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="65a05-405">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="65a05-405">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="65a05-407">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="65a05-407">Create a *Models* folder.</span></span> <span data-ttu-id="65a05-408">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:</span><span class="sxs-lookup"><span data-stu-id="65a05-408">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="65a05-409">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="65a05-409">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="65a05-410">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="65a05-410">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="65a05-411">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="65a05-411">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="65a05-412">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="65a05-412">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="65a05-413">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="65a05-413">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="65a05-414">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-414">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="65a05-415">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="65a05-415">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="65a05-416">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="65a05-416">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="65a05-417">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="65a05-417">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="65a05-418">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="65a05-418">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="65a05-419">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="65a05-419">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="65a05-420">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="65a05-420">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="65a05-421">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="65a05-421">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="65a05-422">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-422">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="65a05-423">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="65a05-423">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="65a05-424">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="65a05-424">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="65a05-425">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="65a05-425">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="65a05-427">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="65a05-427">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="65a05-428">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="65a05-428">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="65a05-429">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="65a05-429">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="65a05-430">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="65a05-430">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="65a05-431">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-431">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="65a05-432">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="65a05-432">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="65a05-433">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="65a05-433">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="65a05-434">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="65a05-434">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="65a05-435">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-435">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="65a05-436">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="65a05-436">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="65a05-437">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="65a05-437">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="65a05-438">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="65a05-439">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="65a05-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="65a05-440">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="65a05-440">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="65a05-441">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-441">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="65a05-442">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="65a05-442">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="65a05-443">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-443">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="65a05-444">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="65a05-444">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="65a05-446">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="65a05-446">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="65a05-447">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="65a05-447">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="65a05-448">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="65a05-448">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="65a05-449">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="65a05-449">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="65a05-450">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="65a05-450">Scaffold the student model</span></span>

<span data-ttu-id="65a05-451">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-451">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="65a05-452">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-452">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="65a05-453">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="65a05-453">Build the project.</span></span>
* <span data-ttu-id="65a05-454">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="65a05-454">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-455">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-455">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="65a05-456">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-456">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="65a05-457">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **HINZUFÜGEN** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-457">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="65a05-458">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="65a05-458">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="65a05-459">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-459">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="65a05-460">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="65a05-460">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="65a05-461">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-461">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="65a05-462">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-462">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="65a05-464">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="65a05-464">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-465">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-465">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="65a05-466">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-466">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="65a05-467">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="65a05-467">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="65a05-468">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="65a05-468">Files created</span></span>

* <span data-ttu-id="65a05-469">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="65a05-469">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="65a05-470">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="65a05-470">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="65a05-471">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="65a05-471">File updates</span></span>

* <span data-ttu-id="65a05-472">*Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="65a05-472">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="65a05-473">*appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="65a05-473">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="65a05-474">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="65a05-474">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="65a05-475">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-475">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65a05-476">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="65a05-476">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="65a05-477">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-477">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="65a05-478">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="65a05-478">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="65a05-479">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="65a05-479">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="65a05-480">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="65a05-480">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="65a05-481">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="65a05-481">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="65a05-482">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-482">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="65a05-483">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="65a05-483">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="65a05-484">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="65a05-484">Update main</span></span>

<span data-ttu-id="65a05-485">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="65a05-485">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="65a05-486">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="65a05-486">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="65a05-487">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="65a05-487">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="65a05-488">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-488">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="65a05-489">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="65a05-489">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="65a05-490">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-490">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="65a05-491">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="65a05-491">If it exists, no action is taken.</span></span> <span data-ttu-id="65a05-492">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-492">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="65a05-493">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-493">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="65a05-494">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-494">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="65a05-495">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="65a05-495">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="65a05-496">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="65a05-496">Delete the DB.</span></span>
* <span data-ttu-id="65a05-497">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="65a05-497">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="65a05-498">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="65a05-498">Run the app.</span></span>
* <span data-ttu-id="65a05-499">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a05-499">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="65a05-500">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-500">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="65a05-501">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="65a05-501">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="65a05-502">Testen der App</span><span class="sxs-lookup"><span data-stu-id="65a05-502">Test the app</span></span>

<span data-ttu-id="65a05-503">Führen Sie die App aus, und akzeptieren Sie die Cookierichtlinie.</span><span class="sxs-lookup"><span data-stu-id="65a05-503">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="65a05-504">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="65a05-504">This app doesn't keep personal information.</span></span> <span data-ttu-id="65a05-505">Weitere Informationen zur Cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="65a05-505">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="65a05-506">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="65a05-506">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="65a05-507">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="65a05-507">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="65a05-508">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="65a05-508">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="65a05-509">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="65a05-509">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="65a05-510">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="65a05-510">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="65a05-511">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-511">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="65a05-512">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="65a05-512">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="65a05-513">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="65a05-513">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="65a05-514">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="65a05-514">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="65a05-515">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="65a05-515">In EF Core terminology:</span></span>

* <span data-ttu-id="65a05-516">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="65a05-516">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="65a05-517">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="65a05-517">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="65a05-518">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-518">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="65a05-519">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="65a05-519">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="65a05-520">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="65a05-520">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="65a05-521">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="65a05-521">SQL Server Express LocalDB</span></span>

<span data-ttu-id="65a05-522">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="65a05-522">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="65a05-523">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="65a05-523">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="65a05-524">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="65a05-524">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="65a05-525">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="65a05-525">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="65a05-526">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="65a05-526">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="65a05-527">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="65a05-527">EF Core creates an empty DB.</span></span> <span data-ttu-id="65a05-528">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="65a05-528">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="65a05-529">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="65a05-529">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="65a05-530">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="65a05-530">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="65a05-531">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="65a05-531">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="65a05-532">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="65a05-532">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="65a05-533">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="65a05-533">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="65a05-534">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="65a05-534">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="65a05-535">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-535">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="65a05-536">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="65a05-536">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="65a05-537">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="65a05-537">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="65a05-538">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="65a05-538">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a05-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a05-539">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65a05-540">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="65a05-540">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="65a05-541">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65a05-541">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="65a05-542">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="65a05-542">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="65a05-543">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="65a05-543">View the DB</span></span>

<span data-ttu-id="65a05-544">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="65a05-544">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="65a05-545">Daher lautet der Name der Datenbank „SchoolContext-{GUID}“.</span><span class="sxs-lookup"><span data-stu-id="65a05-545">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="65a05-546">Die GUID ist für jeden Benutzer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="65a05-546">The GUID will be different for each user.</span></span>
<span data-ttu-id="65a05-547">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="65a05-547">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="65a05-548">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="65a05-548">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="65a05-549">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="65a05-549">Expand the **Tables** node.</span></span>

<span data-ttu-id="65a05-550">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="65a05-550">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="65a05-551">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="65a05-551">Asynchronous code</span></span>

<span data-ttu-id="65a05-552">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="65a05-552">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="65a05-553">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="65a05-553">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="65a05-554">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="65a05-554">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="65a05-555">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="65a05-555">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="65a05-556">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="65a05-556">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="65a05-557">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="65a05-557">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="65a05-558">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="65a05-558">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="65a05-559">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="65a05-559">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="65a05-560">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-560">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="65a05-561">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="65a05-561">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="65a05-562">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="65a05-562">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="65a05-563">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-563">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="65a05-564">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="65a05-564">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="65a05-565">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="65a05-565">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="65a05-566">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="65a05-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="65a05-567">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="65a05-568">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="65a05-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="65a05-569">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="65a05-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="65a05-570">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="65a05-570">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="65a05-571">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="65a05-571">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="65a05-572">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="65a05-572">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="65a05-573">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="65a05-573">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="65a05-574">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65a05-574">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="65a05-575">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="65a05-575">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="65a05-576">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="65a05-576">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="65a05-577">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="65a05-577">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="65a05-578">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="65a05-578">Additional resources</span></span>

* [<span data-ttu-id="65a05-579">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="65a05-579">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="65a05-580">Nächste</span><span class="sxs-lookup"><span data-stu-id="65a05-580">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
