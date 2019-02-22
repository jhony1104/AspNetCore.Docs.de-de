---
title: Arbeiten mit einer Datenbank und ASP.NET Core
author: rick-anderson
description: Dieser Artikel erläutert das Arbeiten mit einer Datenbank und ASP.NET Core.
ms.author: riande
ms.date: 12/07/2017
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e05f5dbc73c35f1f938346b2eaab8c0fa7d8ab9
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410296"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="8bbd6-103">Arbeiten mit einer Datenbank und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bbd6-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="8bbd6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="8bbd6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="8bbd6-105">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8bbd6-106">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8bbd6-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bbd6-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8bbd6-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bbd6-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8bbd6-109">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8bbd6-109">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="8bbd6-110">Weitere Informationen über die in `ConfigureServices` verwendeten Methoden finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-110">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="8bbd6-111">[Unterstützung für die Datenschutz-Grundverordnung (DSGVO) in ASP.NET Core](xref:security/gdpr) für `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-111">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="8bbd6-112">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="8bbd6-112">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="8bbd6-113">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-113">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="8bbd6-114">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-114">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8bbd6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bbd6-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8bbd6-116">Der Name-Wert für die Datenbank (`Database={Database name}`) ist für den generierten Code anders.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-116">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="8bbd6-117">Beim Name-Wert handelt es sich um einen beliebigen Wert.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-117">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8bbd6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bbd6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8bbd6-119">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8bbd6-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="8bbd6-120">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-120">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="8bbd6-121">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8bbd6-121">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8bbd6-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bbd6-122">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8bbd6-123">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8bbd6-123">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8bbd6-124">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-124">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8bbd6-125">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-125">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8bbd6-126">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-126">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="8bbd6-127">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8bbd6-127">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="8bbd6-129">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-129">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

<span data-ttu-id="8bbd6-132">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-132">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8bbd6-133">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-133">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="8bbd6-134">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-134">Right click on the `Movie` table and select **View Data**:</span></span>

  <span data-ttu-id="8bbd6-135">![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)
<!-- Code --------------------------></span><span class="sxs-lookup"><span data-stu-id="8bbd6-135">![Movie table open showing table data](sql/_static/vd22.png)
<!-- Code --------------------------></span></span>
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8bbd6-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bbd6-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8bbd6-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8bbd6-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="8bbd6-138">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="8bbd6-138">Seed the database</span></span>

<span data-ttu-id="8bbd6-139">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-139">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8bbd6-140">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-140">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="8bbd6-141">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="8bbd6-141">Add the seed initializer</span></span>

<span data-ttu-id="8bbd6-142">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-142">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="8bbd6-143">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-143">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8bbd6-144">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-144">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="8bbd6-145">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-145">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="8bbd6-146">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-146">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="8bbd6-147">Eine Produktions-App würde `Database.Migrate` nicht aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-147">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="8bbd6-148">Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-148">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="8bbd6-149">SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-149">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="8bbd6-150">Die Anmeldung ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-150">The login failed.</span></span>
<span data-ttu-id="8bbd6-151">Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-151">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="8bbd6-152">Testen der App</span><span class="sxs-lookup"><span data-stu-id="8bbd6-152">Test the app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8bbd6-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bbd6-153">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8bbd6-154">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-154">Delete all the records in the DB.</span></span> <span data-ttu-id="8bbd6-155">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-155">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="8bbd6-156">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-156">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="8bbd6-157">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-157">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8bbd6-158">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-158">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="8bbd6-159">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-159">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="8bbd6-162">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-162">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="8bbd6-163">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-163">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8bbd6-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bbd6-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8bbd6-165">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="8bbd6-165">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="8bbd6-166">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-166">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="8bbd6-167">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-167">The app shows the seeded data.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8bbd6-168">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8bbd6-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8bbd6-169">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="8bbd6-169">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="8bbd6-170">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-170">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="8bbd6-171">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-171">The app shows the seeded data.</span></span>

---  
<!-- End of VS tabs -->


   
<span data-ttu-id="8bbd6-172">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="8bbd6-172">The app shows the seeded data:</span></span>

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55.png)

<span data-ttu-id="8bbd6-174">Im nächsten Tutorial wird die Präsentation der Daten bereinigt.</span><span class="sxs-lookup"><span data-stu-id="8bbd6-174">The next tutorial will clean up the presentation of the data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8bbd6-175">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8bbd6-175">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>
