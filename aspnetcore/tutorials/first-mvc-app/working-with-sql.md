---
title: Arbeiten mit SQL in einer ASP.NET Core MVC-App
author: rick-anderson
description: Erfahren Sie mehr über die Verwendung von SQL Server LocalDB oder SQLite in einer ASP.NET Core MVC-App.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: de392f4220cf0182d02a20f387164d2f4b184b58
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289083"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="fb493-103">Arbeiten mit SQL in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb493-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb493-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fb493-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb493-105">Das `MvcMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fb493-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="fb493-106">Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="fb493-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="fb493-108">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="fb493-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="fb493-109">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb493-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-110">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="fb493-111">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="fb493-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="fb493-112">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb493-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="fb493-113">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einer SQL Server-Produktionsinstanz festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fb493-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="fb493-114">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fb493-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="fb493-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="fb493-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="fb493-117">LocalDB ist eine Lightweightversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="fb493-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="fb493-118">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="fb493-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="fb493-119">Die LocalDB-Datenbank erstellt standardmäßig *MDF*-Dateien im Verzeichnis *C:/Benutzer/{Benutzer}* .</span><span class="sxs-lookup"><span data-stu-id="fb493-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="fb493-120">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="fb493-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](working-with-sql/_static/ssox.png)

* <span data-ttu-id="fb493-122">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Ansicht-Designer**.</span><span class="sxs-lookup"><span data-stu-id="fb493-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](working-with-sql/_static/dv.png)

<span data-ttu-id="fb493-125">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="fb493-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="fb493-126">EF macht die Eigenschaft `ID` standardmäßig zum Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="fb493-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="fb493-127">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="fb493-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/ssox2.png)

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-130">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="fb493-131">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="fb493-131">Seed the database</span></span>

<span data-ttu-id="fb493-132">Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`.</span><span class="sxs-lookup"><span data-stu-id="fb493-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="fb493-133">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="fb493-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="fb493-134">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb493-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="fb493-135">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="fb493-135">Add the seed initializer</span></span>

<span data-ttu-id="fb493-136">Ersetzen Sie den Inhalt von *Program.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="fb493-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="fb493-137">Testen der App</span><span class="sxs-lookup"><span data-stu-id="fb493-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fb493-139">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fb493-139">Delete all the records in the DB.</span></span> <span data-ttu-id="fb493-140">Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.</span><span class="sxs-lookup"><span data-stu-id="fb493-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="fb493-141">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fb493-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="fb493-142">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="fb493-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="fb493-143">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="fb493-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="fb493-144">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus.</span><span class="sxs-lookup"><span data-stu-id="fb493-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express-Symbol auf der Taskleiste](working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="fb493-147">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fb493-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="fb493-148">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="fb493-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-149">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fb493-150">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="fb493-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="fb493-151">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fb493-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="fb493-152">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="fb493-152">The app shows the seeded data.</span></span>

![In Microsoft Edge geöffnete MVC Movie-Anwendung mit Filmdaten](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="fb493-154">[Zurück](adding-model.md)
> [Weiter](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="fb493-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb493-155">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fb493-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb493-156">Das `MvcMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fb493-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="fb493-157">Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="fb493-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="fb493-159">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="fb493-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="fb493-160">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb493-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-161">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="fb493-162">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="fb493-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="fb493-163">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb493-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="fb493-164">Wenn Sie die App auf einem Test- oder Produktionsserver bereitstellen, können Sie eine Umgebungsvariable oder eine andere Vorgehensweise zum Festlegen der Verbindungszeichenfolge für einen tatsächlichen SQL Server-Computer verwenden.</span><span class="sxs-lookup"><span data-stu-id="fb493-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="fb493-165">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fb493-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="fb493-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="fb493-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="fb493-168">LocalDB ist eine Lightweightversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="fb493-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="fb493-169">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="fb493-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="fb493-170">Die LocalDB-Datenbank erstellt standardmäßig *MDF*-Dateien im Verzeichnis *C:/Benutzer/{Benutzer}* .</span><span class="sxs-lookup"><span data-stu-id="fb493-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="fb493-171">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="fb493-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](working-with-sql/_static/ssox.png)

* <span data-ttu-id="fb493-173">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Ansicht-Designer**.</span><span class="sxs-lookup"><span data-stu-id="fb493-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](working-with-sql/_static/dv.png)

<span data-ttu-id="fb493-176">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="fb493-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="fb493-177">EF macht die Eigenschaft `ID` standardmäßig zum Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="fb493-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="fb493-178">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="fb493-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/ssox2.png)

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-181">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="fb493-182">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="fb493-182">Seed the database</span></span>

<span data-ttu-id="fb493-183">Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`.</span><span class="sxs-lookup"><span data-stu-id="fb493-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="fb493-184">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="fb493-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="fb493-185">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb493-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="fb493-186">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="fb493-186">Add the seed initializer</span></span>

<span data-ttu-id="fb493-187">Ersetzen Sie den Inhalt von *Program.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="fb493-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="fb493-188">Testen der App</span><span class="sxs-lookup"><span data-stu-id="fb493-188">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb493-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb493-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fb493-190">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fb493-190">Delete all the records in the DB.</span></span> <span data-ttu-id="fb493-191">Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.</span><span class="sxs-lookup"><span data-stu-id="fb493-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="fb493-192">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fb493-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="fb493-193">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="fb493-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="fb493-194">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="fb493-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="fb493-195">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus.</span><span class="sxs-lookup"><span data-stu-id="fb493-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express-Symbol auf der Taskleiste](working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="fb493-198">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fb493-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="fb493-199">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="fb493-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb493-200">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="fb493-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fb493-201">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="fb493-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="fb493-202">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fb493-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="fb493-203">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="fb493-203">The app shows the seeded data.</span></span>

![In Microsoft Edge geöffnete MVC Movie-Anwendung mit Filmdaten](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="fb493-205">[Zurück](adding-model.md)
> [Weiter](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="fb493-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
