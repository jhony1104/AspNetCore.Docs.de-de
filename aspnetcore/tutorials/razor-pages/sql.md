---
title: Arbeiten mit einer Datenbank und ASP.NET Core
author: rick-anderson
description: Dieser Artikel erläutert das Arbeiten mit einer Datenbank und ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 197697f28e9faa45c1ac2b7f993bde15994957e5
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440390"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="9aa44-103">Arbeiten mit einer Datenbank und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9aa44-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="9aa44-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="9aa44-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="9aa44-105">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9aa44-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="9aa44-106">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="9aa44-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9aa44-108">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="9aa44-109">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="9aa44-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="9aa44-110">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9aa44-112">Der Name-Wert für die Datenbank (`Database={Database name}`) ist für den generierten Code anders.</span><span class="sxs-lookup"><span data-stu-id="9aa44-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="9aa44-113">Beim Name-Wert handelt es sich um einen beliebigen Wert.</span><span class="sxs-lookup"><span data-stu-id="9aa44-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9aa44-114">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="9aa44-115">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver festzulegen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="9aa44-116">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9aa44-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="9aa44-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9aa44-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9aa44-119">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="9aa44-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="9aa44-120">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="9aa44-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9aa44-121">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9aa44-121">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="9aa44-122">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9aa44-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="9aa44-124">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/design.png)

  ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

<span data-ttu-id="9aa44-127">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="9aa44-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="9aa44-128">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="9aa44-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="9aa44-129">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9aa44-131">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="9aa44-132">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="9aa44-132">Seed the database</span></span>

<span data-ttu-id="9aa44-133">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="9aa44-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="9aa44-134">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9aa44-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="9aa44-135">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="9aa44-135">Add the seed initializer</span></span>

<span data-ttu-id="9aa44-136">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9aa44-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="9aa44-137">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="9aa44-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="9aa44-138">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="9aa44-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="9aa44-139">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="9aa44-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="9aa44-140">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="9aa44-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="9aa44-141">Eine Produktions-App würde `Database.Migrate` nicht aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-141">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="9aa44-142">Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="9aa44-142">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="9aa44-143">SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="9aa44-143">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="9aa44-144">Die Anmeldung ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-144">The login failed.</span></span>
<span data-ttu-id="9aa44-145">Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-145">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="9aa44-146">Testen der App</span><span class="sxs-lookup"><span data-stu-id="9aa44-146">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9aa44-148">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9aa44-148">Delete all the records in the DB.</span></span> <span data-ttu-id="9aa44-149">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="9aa44-149">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="9aa44-150">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9aa44-150">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="9aa44-151">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="9aa44-151">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="9aa44-152">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="9aa44-152">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="9aa44-153">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-153">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="9aa44-156">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-156">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="9aa44-157">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="9aa44-157">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9aa44-158">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9aa44-159">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="9aa44-159">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="9aa44-160">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-160">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="9aa44-161">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="9aa44-161">The app shows the seeded data.</span></span>

---

<span data-ttu-id="9aa44-162">Im nächsten Tutorial wird die Präsentation der Daten verbessert.</span><span class="sxs-lookup"><span data-stu-id="9aa44-162">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa44-163">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9aa44-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9aa44-164">[Zurück: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)
> [Weiter: Updating the pages (Aktualisieren der Seiten)](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="9aa44-164">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="9aa44-165">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9aa44-165">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="9aa44-166">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="9aa44-166">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-167">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9aa44-168">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-168">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="9aa44-169">Weitere Informationen über die in `ConfigureServices` verwendeten Methoden finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="9aa44-169">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="9aa44-170">[Unterstützung für die Datenschutz-Grundverordnung (DSGVO) in ASP.NET Core](xref:security/gdpr) für `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="9aa44-170">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="9aa44-171">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="9aa44-171">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="9aa44-172">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="9aa44-172">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="9aa44-173">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-173">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9aa44-175">Der Name-Wert für die Datenbank (`Database={Database name}`) ist für den generierten Code anders.</span><span class="sxs-lookup"><span data-stu-id="9aa44-175">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="9aa44-176">Beim Name-Wert handelt es sich um einen beliebigen Wert.</span><span class="sxs-lookup"><span data-stu-id="9aa44-176">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9aa44-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9aa44-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9aa44-178">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="9aa44-179">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver festzulegen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-179">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="9aa44-180">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9aa44-180">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-181">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="9aa44-182">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9aa44-182">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9aa44-183">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="9aa44-183">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="9aa44-184">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="9aa44-184">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9aa44-185">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9aa44-185">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="9aa44-186">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9aa44-186">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="9aa44-188">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-188">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

<span data-ttu-id="9aa44-191">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="9aa44-191">Note the key icon next to `ID`.</span></span> <span data-ttu-id="9aa44-192">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="9aa44-192">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="9aa44-193">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-193">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9aa44-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9aa44-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9aa44-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="9aa44-197">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="9aa44-197">Seed the database</span></span>

<span data-ttu-id="9aa44-198">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="9aa44-198">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="9aa44-199">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9aa44-199">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="9aa44-200">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="9aa44-200">Add the seed initializer</span></span>

<span data-ttu-id="9aa44-201">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9aa44-201">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="9aa44-202">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="9aa44-202">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="9aa44-203">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="9aa44-203">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="9aa44-204">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="9aa44-204">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="9aa44-205">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="9aa44-205">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="9aa44-206">Eine Produktions-App würde `Database.Migrate` nicht aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-206">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="9aa44-207">Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="9aa44-207">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="9aa44-208">SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="9aa44-208">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="9aa44-209">Die Anmeldung ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-209">The login failed.</span></span>
<span data-ttu-id="9aa44-210">Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-210">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="9aa44-211">Testen der App</span><span class="sxs-lookup"><span data-stu-id="9aa44-211">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9aa44-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9aa44-212">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9aa44-213">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9aa44-213">Delete all the records in the DB.</span></span> <span data-ttu-id="9aa44-214">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="9aa44-214">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="9aa44-215">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9aa44-215">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="9aa44-216">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="9aa44-216">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="9aa44-217">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="9aa44-217">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="9aa44-218">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="9aa44-218">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="9aa44-221">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-221">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="9aa44-222">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="9aa44-222">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9aa44-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9aa44-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9aa44-224">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="9aa44-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="9aa44-225">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="9aa44-226">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="9aa44-226">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9aa44-227">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9aa44-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9aa44-228">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="9aa44-228">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="9aa44-229">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9aa44-229">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="9aa44-230">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="9aa44-230">The app shows the seeded data.</span></span>

---

<span data-ttu-id="9aa44-231">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="9aa44-231">The app shows the seeded data:</span></span>

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55.png)

<span data-ttu-id="9aa44-233">Im nächsten Tutorial wird die Präsentation der Daten bereinigt.</span><span class="sxs-lookup"><span data-stu-id="9aa44-233">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa44-234">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9aa44-234">Additional resources</span></span>

* [<span data-ttu-id="9aa44-235">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="9aa44-235">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="9aa44-236">[Zurück: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)
> [Weiter: Updating the pages (Aktualisieren der Seiten)](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="9aa44-236">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end