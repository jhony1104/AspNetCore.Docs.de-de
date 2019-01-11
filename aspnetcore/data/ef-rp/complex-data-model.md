---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)'
author: rick-anderson
description: In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie Regeln zur Formatierung, Validierung und Zuordnung angeben.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 930a6f2b860c71b6f499cff53e0d909a130f7948
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637897"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="84711-103">Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)</span><span class="sxs-lookup"><span data-stu-id="84711-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="84711-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84711-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="84711-105">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="84711-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="84711-106">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="84711-106">In this tutorial:</span></span>

* <span data-ttu-id="84711-107">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="84711-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="84711-108">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="84711-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="84711-109">Die Entitätsklassen des vollständigen Datenmodells werden in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="84711-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="84711-111">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="84711-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="84711-112">Anpassen des Datenmodells mithilfe von Attributen</span><span class="sxs-lookup"><span data-stu-id="84711-112">Customize the data model with attributes</span></span>

<span data-ttu-id="84711-113">In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.</span><span class="sxs-lookup"><span data-stu-id="84711-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="84711-114">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-114">The DataType attribute</span></span>

<span data-ttu-id="84711-115">Die Seite für Studenten zeigt derzeit die Uhrzeit des Anmeldedatums an.</span><span class="sxs-lookup"><span data-stu-id="84711-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="84711-116">Üblicherweise zeigen Datumsfelder nur das Datum an, nicht die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="84711-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="84711-117">Aktualisieren Sie *Models/Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="84711-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="84711-118">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="84711-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="84711-119">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="84711-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="84711-120">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="84711-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="84711-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="84711-121">For example:</span></span>

* <span data-ttu-id="84711-122">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="84711-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="84711-123">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="84711-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="84711-124">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus, die von HTML5-Browsern genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="84711-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="84711-125">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="84711-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="84711-126">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="84711-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="84711-127">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="84711-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="84711-128">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="84711-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="84711-129">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="84711-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="84711-130">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="84711-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="84711-131">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="84711-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="84711-132">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="84711-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="84711-133">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="84711-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="84711-134">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="84711-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="84711-135">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="84711-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="84711-136">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="84711-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="84711-137">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.</span><span class="sxs-lookup"><span data-stu-id="84711-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="84711-138">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="84711-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="84711-139">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="84711-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="84711-140">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="84711-140">Run the app.</span></span> <span data-ttu-id="84711-141">Navigieren Sie zur Indexseite „Studenten“.</span><span class="sxs-lookup"><span data-stu-id="84711-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="84711-142">Die Uhrzeiten werden nicht mehr angezeigt.</span><span class="sxs-lookup"><span data-stu-id="84711-142">Times are no longer displayed.</span></span> <span data-ttu-id="84711-143">Jede Ansicht, die das `Student`-Modell verwendet, zeigt das Datum ohne Uhrzeit an.</span><span class="sxs-lookup"><span data-stu-id="84711-143">Every view that uses the `Student` model displays the date without time.</span></span>

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="84711-145">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-145">The StringLength attribute</span></span>

<span data-ttu-id="84711-146">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="84711-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="84711-147">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="84711-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="84711-148">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="84711-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="84711-149">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="84711-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="84711-150">Aktualisieren Sie das `Student`-Modell mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="84711-151">Der vorangehende Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="84711-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="84711-152">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="84711-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="84711-153">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) wird verwendet, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="84711-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="84711-154">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="84711-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="84711-155">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="84711-155">Run the app:</span></span>

* <span data-ttu-id="84711-156">Navigieren Sie zur Seite für Studenten.</span><span class="sxs-lookup"><span data-stu-id="84711-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="84711-157">Klicken Sie auf **Neu erstellen**, und geben Sie einen Namen ein, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="84711-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="84711-158">Wenn Sie auf **Erstellen** klicken, zeigt die clientseitige Validierung eine Fehlermeldung an.</span><span class="sxs-lookup"><span data-stu-id="84711-158">Select **Create**, client-side validation shows an error message.</span></span>

![Indexseite „Studenten“, die Fehler für die Zeichenfolgenlänge anzeigt](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="84711-160">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="84711-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="84711-162">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="84711-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="84711-163">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf, da die Migrationen nicht auf der Datenbank ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="84711-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="84711-164">Wenn die Migrationen im Verlauf dieses Tutorials ausgeführt werden, ändern sich die Namensfelder in `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="84711-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="84711-165">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-165">The Column attribute</span></span>

<span data-ttu-id="84711-166">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="84711-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="84711-167">In diesem Abschnitt wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="84711-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="84711-168">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="84711-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="84711-169">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="84711-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="84711-170">Aktualisieren Sie die Datei *Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="84711-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="84711-171">Durch die zuvor vorgenommene Änderung wird `Student.FirstMidName` in der App der `FirstName`-Spalte der `Student`-Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="84711-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="84711-172">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="84711-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="84711-173">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="84711-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="84711-174">Wenn die App ausgeführt wird, bevor Migrationen angewendet werden, wird folgende Ausnahme generiert:</span><span class="sxs-lookup"><span data-stu-id="84711-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="84711-175">So aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="84711-175">To update the DB:</span></span>

* <span data-ttu-id="84711-176">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="84711-176">Build the project.</span></span>
* <span data-ttu-id="84711-177">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="84711-177">Open a command window in the project folder.</span></span> <span data-ttu-id="84711-178">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="84711-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84711-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84711-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84711-180">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="84711-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

------

<span data-ttu-id="84711-181">Der Befehl `migrations add ColumnFirstName` generiert folgende Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="84711-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="84711-182">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="84711-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="84711-183">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="84711-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="84711-184">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="84711-184">Test the app.</span></span>

<span data-ttu-id="84711-185">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="84711-185">Open the Student table in SSOX:</span></span>

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="84711-187">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="84711-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="84711-188">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="84711-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="84711-189">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="84711-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="84711-190">Im folgenden Abschnitt werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="84711-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="84711-191">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="84711-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="84711-192">Aktualisieren der Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="84711-192">Student entity update</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="84711-194">Aktualisieren Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="84711-195">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="84711-195">The Required attribute</span></span>

<span data-ttu-id="84711-196">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="84711-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="84711-197">Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (`DateTime`, `int`, `double` usw.) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="84711-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="84711-198">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="84711-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="84711-199">Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="84711-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="84711-200">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-200">The Display attribute</span></span>

<span data-ttu-id="84711-201">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="84711-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="84711-202">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="84711-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="84711-203">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="84711-203">The FullName calculated property</span></span>

<span data-ttu-id="84711-204">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="84711-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="84711-205">`FullName` verfügt lediglich über einen get-Accessor und kann daher nicht festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="84711-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="84711-206">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="84711-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="84711-207">Erstellen der Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="84711-207">Create the Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="84711-209">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="84711-210">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="84711-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="84711-211">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="84711-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="84711-212">Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="84711-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="84711-213">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="84711-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="84711-214">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="84711-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="84711-215">Folgendes gilt, wenn eine Navigationseigenschaft mehrere Entitäten enthält:</span><span class="sxs-lookup"><span data-stu-id="84711-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="84711-216">Bei der Eigenschaft muss es sich um einen Listentyp handeln, bei dem Einträge hinzugefügt, gelöscht und aktualisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="84711-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="84711-217">Folgendes gilt für die Typen von Navigationseigenschaften:</span><span class="sxs-lookup"><span data-stu-id="84711-217">Navigation property types include:</span></span>

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

<span data-ttu-id="84711-218">Wenn `ICollection<T>` angegeben wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="84711-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="84711-219">Die `CourseAssignment`-Entität wird im Abschnitt über m:n-Beziehungen erläutert.</span><span class="sxs-lookup"><span data-stu-id="84711-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="84711-220">Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann.</span><span class="sxs-lookup"><span data-stu-id="84711-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="84711-221">Die `OfficeAssignment`-Eigenschaft enthält also eine einzige `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="84711-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="84711-222">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="84711-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="84711-223">Erstellen der Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="84711-223">Create the OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="84711-225">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="84711-226">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-226">The Key attribute</span></span>

<span data-ttu-id="84711-227">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="84711-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="84711-228">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="84711-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="84711-229">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="84711-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="84711-230">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="84711-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="84711-231">Entity Framework Core erkennt `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment`, weil</span><span class="sxs-lookup"><span data-stu-id="84711-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="84711-232">`InstructorID` der Namenskonvention „ID“ oder „classnameID“ nicht folgt.</span><span class="sxs-lookup"><span data-stu-id="84711-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="84711-233">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="84711-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="84711-234">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="84711-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="84711-235">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="84711-235">The Instructor navigation property</span></span>

<span data-ttu-id="84711-236">Die `OfficeAssignment`-Navigationseigenschaft für die `Instructor`-Entität ist auf NULL festlegbar, weil:</span><span class="sxs-lookup"><span data-stu-id="84711-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="84711-237">Referenztypen (z.B. Klassen) auf NULL festlegbar sind.</span><span class="sxs-lookup"><span data-stu-id="84711-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="84711-238">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="84711-238">An instructor might not have an office assignment.</span></span>


<span data-ttu-id="84711-239">Die `OfficeAssignment`-Entität besitzt eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft, weil:</span><span class="sxs-lookup"><span data-stu-id="84711-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="84711-240">`InstructorID` nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="84711-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="84711-241">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="84711-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="84711-242">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="84711-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="84711-243">Das `[Required]`-Attribut kann auf die `Instructor`-Navigationseigenschaft angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="84711-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="84711-244">Der vorangehende Code legt fest, dass ein zugehöriger Dozent vorhanden sein muss.</span><span class="sxs-lookup"><span data-stu-id="84711-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="84711-245">Der vorangehende Code ist nicht erforderlich, da der Fremdschlüssel `InstructorID` (der ebenfalls der Primärschlüssel ist) nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="84711-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="84711-246">Ändern der Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="84711-246">Modify the Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="84711-248">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="84711-249">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="84711-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="84711-250">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="84711-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="84711-251">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="84711-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="84711-252">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="84711-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="84711-253">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="84711-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="84711-254">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="84711-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="84711-255">Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="84711-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="84711-256">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="84711-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="84711-257">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="84711-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="84711-258">Die `Department`-Entität ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="84711-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="84711-259">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="84711-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="84711-260">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="84711-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="84711-261">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="84711-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="84711-262">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="84711-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="84711-263">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="84711-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="84711-264">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="84711-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="84711-265">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="84711-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="84711-266">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="84711-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="84711-267">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="84711-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="84711-268">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="84711-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="84711-269">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="84711-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="84711-270">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="84711-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="84711-271">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="84711-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="84711-272">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="84711-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="84711-273">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="84711-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="84711-274">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="84711-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="84711-275">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="84711-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="84711-276">Erstellen der Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="84711-276">Create the Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="84711-278">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="84711-279">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="84711-279">The Column attribute</span></span>

<span data-ttu-id="84711-280">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="84711-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="84711-281">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="84711-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="84711-282">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="84711-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="84711-283">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="84711-283">Column mapping is generally not required.</span></span> <span data-ttu-id="84711-284">Entity Framework Core wählt üblicherweise den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="84711-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="84711-285">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="84711-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="84711-286">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="84711-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="84711-287">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="84711-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="84711-288">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="84711-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="84711-289">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="84711-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="84711-290">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="84711-290">An administrator is always an instructor.</span></span> <span data-ttu-id="84711-291">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="84711-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="84711-292">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="84711-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="84711-293">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="84711-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="84711-294">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="84711-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="84711-295">Hinweis: Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="84711-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="84711-296">Das kaskadierende Delete kann zu Zirkelregeln für kaskadierende Deletes führen.</span><span class="sxs-lookup"><span data-stu-id="84711-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="84711-297">Durch Zirkelregeln für kaskadierende Deletes wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="84711-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="84711-298">Beispielsweise dann, wenn die `Department.InstructorID`-Eigenschaft nicht als auf NULL festlegbar definiert wurde:</span><span class="sxs-lookup"><span data-stu-id="84711-298">For example, if the `Department.InstructorID` property wasn't defined as nullable:</span></span>

* <span data-ttu-id="84711-299">Entity Framework Core konfiguriert eine Regel für kaskadierende Deletes, um den Dozenten zu löschen, wenn ein Fachbereich gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="84711-299">EF Core configures a cascade delete rule to delete the instructor when the department is deleted.</span></span>
* <span data-ttu-id="84711-300">Das Löschen eines Dozenten in Folge des Löschens eines Fachbereichs stellt nicht das beabsichtigte Verhalten dar.</span><span class="sxs-lookup"><span data-stu-id="84711-300">Deleting the instructor when the department is deleted isn't the intended behavior.</span></span>

<span data-ttu-id="84711-301">Wenn die Geschäftsregeln erfordern, dass die `InstructorID`-Eigenschaft nicht auf NULL festgelegt werden darf, verwenden Sie folgende Fluent-API-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="84711-301">If business rules required the `InstructorID` property be non-nullable, use the following fluent API statement:</span></span>

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

<span data-ttu-id="84711-302">Durch den vorangehenden Code werden kaskadierende Deletes für die Beziehung zwischen „Department“ und „Instructor“ deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="84711-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="84711-303">Aktualisieren der Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="84711-303">Update the Enrollment entity</span></span>

<span data-ttu-id="84711-304">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="84711-304">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="84711-306">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="84711-307">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="84711-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="84711-308">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="84711-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="84711-309">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="84711-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="84711-310">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="84711-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="84711-311">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="84711-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="84711-312">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="84711-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="84711-313">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="84711-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="84711-314">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="84711-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="84711-315">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="84711-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="84711-316">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="84711-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="84711-317">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="84711-317">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="84711-319">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="84711-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="84711-320">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="84711-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="84711-321">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="84711-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="84711-322">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="84711-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="84711-323">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="84711-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="84711-324">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="84711-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="84711-325">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="84711-325">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="84711-327">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="84711-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="84711-328">Beziehung zwischen „Instructor“ und „Course“</span><span class="sxs-lookup"><span data-stu-id="84711-328">Instructor-to-Courses</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="84711-330">Folgendes gilt für die m:n-Beziehung zwischen „Instructor“ und „Course“:</span><span class="sxs-lookup"><span data-stu-id="84711-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="84711-331">Eine Jointabelle ist erforderlich, die durch eine Entitätenmenge dargestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="84711-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="84711-332">Diese muss eine reine Jointabelle (eine Tabelle ohne Nutzlast) sein.</span><span class="sxs-lookup"><span data-stu-id="84711-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="84711-333">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="84711-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="84711-334">Der Name der Jointabelle für „Instructor“ und „Course“ lautet mithilfe dieses Musters beispielsweise `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="84711-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="84711-335">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="84711-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="84711-336">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="84711-336">Data models start out simple and grow.</span></span> <span data-ttu-id="84711-337">Währenddessen erhalten Joins ohne Nutzlast häufig Nutzlasten.</span><span class="sxs-lookup"><span data-stu-id="84711-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="84711-338">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="84711-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="84711-339">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="84711-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="84711-340">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="84711-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="84711-341">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="84711-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="84711-342">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="84711-342">Composite key</span></span>

<span data-ttu-id="84711-343">Fremdschlüssel sind nicht auf NULL festlegbar.</span><span class="sxs-lookup"><span data-stu-id="84711-343">FKs are not nullable.</span></span> <span data-ttu-id="84711-344">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="84711-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="84711-345">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="84711-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="84711-346">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="84711-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="84711-347">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die Fluent-API angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="84711-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="84711-348">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="84711-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="84711-349">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="84711-349">The composite key ensures:</span></span>

* <span data-ttu-id="84711-350">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="84711-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="84711-351">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="84711-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="84711-352">Mehrere Zeilen für denselben Dozenten und Kurs nicht zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="84711-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="84711-353">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="84711-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="84711-354">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="84711-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="84711-355">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="84711-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="84711-356">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="84711-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="84711-357">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="84711-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="84711-358">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="84711-358">Update the DB context</span></span>

<span data-ttu-id="84711-359">Fügen Sie folgenden hervorgehobenen Code zu *Data/SchoolContext.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="84711-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="84711-360">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="84711-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="84711-361">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="84711-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="84711-362">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="84711-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="84711-363">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="84711-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="84711-364">Der [folgende Code](/ef/core/modeling/#methods-of-configuration) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="84711-364">The [following code](/ef/core/modeling/#methods-of-configuration) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="84711-365">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="84711-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="84711-366">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="84711-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="84711-367">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="84711-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="84711-368">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="84711-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="84711-369">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="84711-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="84711-370">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="84711-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="84711-371">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="84711-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="84711-372">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="84711-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="84711-373">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="84711-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="84711-374">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="84711-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="84711-375">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="84711-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="84711-376">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="84711-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="84711-377">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="84711-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="84711-378">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="84711-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="84711-379">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="84711-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="84711-380">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="84711-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="84711-381">Entitätsdiagramm mit angezeigten Beziehungen</span><span class="sxs-lookup"><span data-stu-id="84711-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="84711-382">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="84711-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="84711-384">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="84711-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="84711-385">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="84711-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="84711-386">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="84711-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="84711-387">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="84711-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="84711-388">Ausführen eines Seedings mit Testdaten für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="84711-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="84711-389">Aktualisieren Sie den Code in *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="84711-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="84711-390">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="84711-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="84711-391">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="84711-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="84711-392">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="84711-392">The sample data is used for testing.</span></span> <span data-ttu-id="84711-393">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="84711-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="84711-394">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="84711-394">Add a migration</span></span>

<span data-ttu-id="84711-395">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="84711-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84711-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84711-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84711-397">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="84711-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

------

<span data-ttu-id="84711-398">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="84711-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="84711-399">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="84711-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="84711-400">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="84711-400">Apply the migration</span></span>

<span data-ttu-id="84711-401">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie zukünftig Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="84711-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="84711-402">In diesem Tutorial werden zwei Vorgehensweisen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="84711-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="84711-403">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="84711-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="84711-404">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="84711-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="84711-405">Obwohl diese Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="84711-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="84711-406">**Hinweis:** Dies ist ein optionaler Abschnitt des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="84711-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="84711-407">Sie können diesen Abschnitt überspringen und die Schritte zum Löschen und Neuerstellen durchführen.</span><span class="sxs-lookup"><span data-stu-id="84711-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="84711-408">Wenn Sie stattdessen die Schritte in diesem Abschnitt ausführen möchten, führen Sie nicht die Schritte zum Löschen und Neuerstellen aus.</span><span class="sxs-lookup"><span data-stu-id="84711-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="84711-409">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="84711-409">Drop and re-create the database</span></span>

<span data-ttu-id="84711-410">Durch den Code in der aktualisierten `DbInitializer`-Klasse werden Startwertdaten für die neuen Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="84711-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="84711-411">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="84711-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="84711-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84711-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84711-413">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="84711-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="84711-414">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="84711-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="84711-415">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="84711-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84711-416">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="84711-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="84711-417">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="84711-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="84711-418">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="84711-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

------

<span data-ttu-id="84711-419">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="84711-419">Run the app.</span></span> <span data-ttu-id="84711-420">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="84711-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="84711-421">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="84711-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="84711-422">Öffnen Sie die Datenbank im SSOX:</span><span class="sxs-lookup"><span data-stu-id="84711-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="84711-423">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="84711-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="84711-424">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="84711-424">Expand the **Tables** node.</span></span> <span data-ttu-id="84711-425">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="84711-425">The created tables are displayed.</span></span>

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="84711-427">Überprüfen Sie die Tabelle **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="84711-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="84711-428">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="84711-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="84711-429">Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="84711-429">Verify the **CourseAssignment** table contains data.</span></span>

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="84711-431">Anwenden der Migration auf die vorhandene Datenbank</span><span class="sxs-lookup"><span data-stu-id="84711-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="84711-432">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="84711-432">This section is optional.</span></span> <span data-ttu-id="84711-433">Diese Schritte funktionieren nur, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="84711-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="84711-434">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="84711-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="84711-435">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="84711-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="84711-436">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="84711-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="84711-437">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="84711-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="84711-438">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="84711-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="84711-439">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="84711-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="84711-440">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="84711-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="84711-441">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="84711-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="84711-442">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="84711-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="84711-443">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="84711-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="84711-444">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="84711-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="84711-445">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="84711-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="84711-446">Aktualisieren Sie die `Up`-Methode der `ComplexDataModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="84711-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="84711-447">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="84711-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="84711-448">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="84711-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="84711-449">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="84711-449">Add the following highlighted code.</span></span> <span data-ttu-id="84711-450">Der neue Code folgt nach dem Block `.CreateTable( name: "Department"`: [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span><span class="sxs-lookup"><span data-stu-id="84711-450">The new code goes after the `.CreateTable( name: "Department"` block: [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span></span>

<span data-ttu-id="84711-451">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel` `Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="84711-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="84711-452">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="84711-452">A production app would:</span></span>

* <span data-ttu-id="84711-453">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="84711-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="84711-454">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="84711-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="84711-455">Im folgenden Tutorial werden verknüpfte Daten behandelt.</span><span class="sxs-lookup"><span data-stu-id="84711-455">The next tutorial covers related data.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="84711-456">[Zurück](xref:data/ef-rp/migrations)
> [Weiter](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="84711-456">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
