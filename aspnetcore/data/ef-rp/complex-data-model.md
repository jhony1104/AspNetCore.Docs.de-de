---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)'
author: rick-anderson
description: In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie Regeln zur Formatierung, Validierung und Zuordnung angeben.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 96e4acf0d6c9c079ebee32fc2f9951fdd668931b
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914961"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="91367-103">Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)</span><span class="sxs-lookup"><span data-stu-id="91367-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="91367-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91367-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91367-105">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="91367-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="91367-106">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="91367-106">In this tutorial:</span></span>

* <span data-ttu-id="91367-107">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="91367-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="91367-108">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="91367-109">Das vollständige Datenmodell wird in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="91367-109">The completed data model is shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="91367-111">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="91367-111">The Student entity</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="91367-113">Ersetzen Sie den Code in *Models/Student.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="91367-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="91367-114">Der Code oben fügt eine `FullName`-Eigenschaft hinzu und fügt den vorhandenen Eigenschaften die folgenden Attribute hinzu:</span><span class="sxs-lookup"><span data-stu-id="91367-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="91367-115">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="91367-115">The FullName calculated property</span></span>

<span data-ttu-id="91367-116">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="91367-117">`FullName` kann nicht festgelegt werden und verfügt daher lediglich über einen get-Accessor.</span><span class="sxs-lookup"><span data-stu-id="91367-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="91367-118">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="91367-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="91367-119">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="91367-120">Für die Anmeldedaten von Studenten zeigen alle Webseiten derzeit die Zeit und das Datum an, obwohl nur das Datum relevant ist.</span><span class="sxs-lookup"><span data-stu-id="91367-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="91367-121">Indem Sie Attribute für die Datenanmerkung verwenden, können Sie eine Codeänderungen vornehmen, durch die das Anzeigeformat auf jeder Seite korrigiert wird, in der die Daten angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="91367-122">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="91367-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="91367-123">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="91367-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="91367-124">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="91367-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="91367-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="91367-125">For example:</span></span>

* <span data-ttu-id="91367-126">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="91367-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="91367-127">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="91367-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="91367-128">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus.</span><span class="sxs-lookup"><span data-stu-id="91367-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="91367-129">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="91367-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="91367-130">Das DisplayFormat-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="91367-131">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="91367-132">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="91367-133">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats.</span><span class="sxs-lookup"><span data-stu-id="91367-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="91367-134">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="91367-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="91367-135">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="91367-136">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="91367-137">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="91367-138">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="91367-139">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="91367-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="91367-140">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="91367-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="91367-141">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="91367-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="91367-142">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links und einigen clientseitigen Eingabevalidierungen.</span><span class="sxs-lookup"><span data-stu-id="91367-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="91367-143">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="91367-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="91367-144">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="91367-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="91367-145">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="91367-146">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="91367-147">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="91367-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="91367-148">Der gezeigte Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="91367-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="91367-149">Ein Beispiel, das die minimale Zeichenfolgenlänge festlegt, wird [später](#the-required-attribute) gezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="91367-150">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="91367-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="91367-151">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="91367-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="91367-152">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="91367-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="91367-153">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) kann verwendet werden, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="91367-154">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="91367-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91367-156">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="91367-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="91367-158">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="91367-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="91367-159">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf.</span><span class="sxs-lookup"><span data-stu-id="91367-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="91367-160">Wenn eine Migration später in diesem Tutorial erstellt und angewendet wird, werden die Namensfelder `nvarchar(50)` als Ergebnis der Attribute für die Zeichenfolgenlänge.</span><span class="sxs-lookup"><span data-stu-id="91367-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91367-162">Überprüfen Sie in Ihrem SQLite-Tool die Spaltendefinitionen für die `Student`-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="91367-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="91367-163">Die Namensfelder weisen den Typ `Text` auf.</span><span class="sxs-lookup"><span data-stu-id="91367-163">The name fields have type `Text`.</span></span> <span data-ttu-id="91367-164">Beachten Sie, dass das Feld für den Vornamen den Namen `FirstMidName` trägt.</span><span class="sxs-lookup"><span data-stu-id="91367-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="91367-165">Im nächsten Abschnitt ändern Sie den Namen dieser Spalte in `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="91367-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="91367-166">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="91367-167">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="91367-168">Im `Student`-Modell wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="91367-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="91367-169">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="91367-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="91367-170">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="91367-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="91367-171">Mit dem `[Column]`-Attribut wird `Student.FirstMidName` im Datenmodell der Spalte `FirstName` in der Tabelle `Student` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="91367-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="91367-172">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="91367-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="91367-173">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="91367-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="91367-174">Diese Diskrepanz wird durch Hinzufügen einer Migration zu einem späteren Zeitpunkt in diesem Tutorial aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="91367-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="91367-175">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="91367-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="91367-176">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="91367-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="91367-177">Das `Required`-Attribut ist für Typen, die keine NULL-Werte annehmen können (etwas Werttypen, z.B. `DateTime`, `int` und `double`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="91367-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="91367-178">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="91367-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="91367-179">Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="91367-179">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="91367-180">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-180">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="91367-181">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="91367-181">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="91367-182">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="91367-182">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="91367-183">Erstellen einer Migration</span><span class="sxs-lookup"><span data-stu-id="91367-183">Create a migration</span></span>

<span data-ttu-id="91367-184">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="91367-184">Run the app and go to the Students page.</span></span> <span data-ttu-id="91367-185">Es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="91367-185">An exception is thrown.</span></span> <span data-ttu-id="91367-186">Das `[Column]`-Attribut bewirkt, dass EF eine Spalte mit dem Namen `FirstName` erwartet, aber der Spaltenname in der Datenbank ist weiterhin `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="91367-186">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-187">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91367-188">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="91367-188">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="91367-189">Geben Sie in der PMC folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="91367-189">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="91367-190">Mit dem ersten dieser Befehle wird die folgende Warnmeldung generiert:</span><span class="sxs-lookup"><span data-stu-id="91367-190">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="91367-191">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="91367-191">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="91367-192">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="91367-192">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="91367-193">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="91367-193">Open the Student table in SSOX:</span></span>

  ![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="91367-195">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="91367-195">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="91367-196">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="91367-196">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="91367-197">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="91367-197">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91367-199">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="91367-199">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="91367-200">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="91367-200">Open a command window in the project folder.</span></span> <span data-ttu-id="91367-201">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="91367-201">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="91367-202">Der Befehl für die Datenbankaktualisierung zeigt einen Fehler wie im folgenden Beispiel an:</span><span class="sxs-lookup"><span data-stu-id="91367-202">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="91367-203">In diesem Tutorial wird die anfängliche Migration gelöscht und erneut erstellt, um diese Fehlermeldung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="91367-203">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="91367-204">Weitere Informationen finden Sie im SQLite-Warnhinweis am Anfang des [Migrationstutorials](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="91367-204">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="91367-205">Löschen Sie den Ordner *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="91367-205">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="91367-206">Führen Sie die folgenden Befehle aus, um die Datenbank zu löschen, eine neue anfängliche Migration zu erstellen und die Migration anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="91367-206">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="91367-207">Überprüfen Sie die Tabelle „Student“ in Ihrem SQLite-Tool.</span><span class="sxs-lookup"><span data-stu-id="91367-207">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="91367-208">Die Spalte, die zunächst FirstMidName hieß, ist jetzt FirstName.</span><span class="sxs-lookup"><span data-stu-id="91367-208">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="91367-209">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="91367-209">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="91367-210">Beachten Sie, dass die Uhrzeiten nicht mit Datumsangaben eingegeben oder angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-210">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="91367-211">Klicken Sie auf **Neu erstellen**, und versuchen Sie, einen Namen einzugeben, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-211">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="91367-212">In den folgenden Abschnitten werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="91367-212">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="91367-213">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="91367-213">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="91367-214">Die Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="91367-214">The Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="91367-216">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="91367-217">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="91367-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="91367-218">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="91367-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="91367-219">Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-219">Navigation properties</span></span>

<span data-ttu-id="91367-220">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="91367-220">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="91367-221">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="91367-221">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="91367-222">Ein Dozent kann höchstens ein Büro haben, sodass die `OfficeAssignment`-Eigenschaft eine einzelne `OfficeAssignment`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="91367-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="91367-223">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="91367-224">Die Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="91367-224">The OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="91367-226">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="91367-227">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-227">The Key attribute</span></span>

<span data-ttu-id="91367-228">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="91367-228">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="91367-229">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="91367-230">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="91367-231">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="91367-232">EF Core kann `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment` erkennen, da `InstructorID` nicht den ID- oder classnameID-Benennungskonventionen folgt.</span><span class="sxs-lookup"><span data-stu-id="91367-232">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="91367-233">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="91367-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="91367-234">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="91367-235">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="91367-235">The Instructor navigation property</span></span>

<span data-ttu-id="91367-236">Die `Instructor.OfficeAssignment`-Navigationseigenschaft kann NULL sein, da möglicherweise keine `OfficeAssignment`-Zeile für einen bestimmten Dozenten vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="91367-236">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="91367-237">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-237">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="91367-238">Die `OfficeAssignment.Instructor`-Navigationseigenschaft hat immer eine Instructor-Entität, da der Typ `InstructorID` des Fremdschlüssels `int` ist, ein Typ, der keine NULL-Werte annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="91367-238">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="91367-239">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="91367-239">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="91367-240">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="91367-240">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="91367-241">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="91367-241">The Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="91367-243">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-243">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="91367-244">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="91367-244">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="91367-245">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-245">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="91367-246">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="91367-246">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="91367-247">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="91367-247">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="91367-248">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="91367-248">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="91367-249">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-249">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="91367-250">Durch explizites Einschließen des Fremdschlüssels in das Datenmodell können Updates jedoch einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-250">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="91367-251">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="91367-251">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="91367-252">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="91367-252">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="91367-253">Die `Department`-Eigenschaft ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="91367-253">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="91367-254">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="91367-254">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="91367-255">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="91367-255">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="91367-256">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="91367-256">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="91367-257">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="91367-257">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="91367-258">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="91367-258">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="91367-259">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="91367-259">Database-generated is generally the best approach.</span></span> <span data-ttu-id="91367-260">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="91367-260">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="91367-261">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="91367-261">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="91367-262">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-262">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="91367-263">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="91367-263">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="91367-264">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="91367-264">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-265">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-266">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-266">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="91367-267">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="91367-267">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="91367-268">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="91367-268">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="91367-269">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="91367-269">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="91367-270">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="91367-270">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="91367-271">Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="91367-271">The Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="91367-273">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-273">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="91367-274">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-274">The Column attribute</span></span>

<span data-ttu-id="91367-275">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="91367-275">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="91367-276">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="91367-276">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="91367-277">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="91367-277">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="91367-278">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="91367-278">Column mapping is generally not required.</span></span> <span data-ttu-id="91367-279">Entity Framework Core wählt den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="91367-279">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="91367-280">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="91367-280">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="91367-281">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="91367-281">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-282">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-282">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-283">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-283">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="91367-284">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="91367-284">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="91367-285">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="91367-285">An administrator is always an instructor.</span></span> <span data-ttu-id="91367-286">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="91367-286">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="91367-287">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="91367-287">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="91367-288">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="91367-288">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="91367-289">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="91367-289">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="91367-290">Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="91367-290">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="91367-291">Das Standardverhalten kann zu Zirkelregeln für kaskadierende Löschvorgänge führen.</span><span class="sxs-lookup"><span data-stu-id="91367-291">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="91367-292">Durch Zirkelregeln für kaskadierende Löschvorgänge wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-292">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="91367-293">Wenn die `Department.InstructorID`-Eigenschaft beispielsweise als nicht auf NULL festlegbare Eigenschaft definiert wurde, konfiguriert EF Core eine Löschregel für kaskadierende Löschvorgänge.</span><span class="sxs-lookup"><span data-stu-id="91367-293">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="91367-294">In diesem Fall würde der Fachbereich gelöscht, wenn der als Administrator zugewiesene Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="91367-294">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="91367-295">In diesem Szenario wäre eine Einschränkungsregel sinnvoller.</span><span class="sxs-lookup"><span data-stu-id="91367-295">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="91367-296">Die folgende Fluent-API würde eine Einschränkungsregel festlegen und kaskadierende Löschvorgänge deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="91367-296">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="91367-297">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="91367-297">The Enrollment entity</span></span>

<span data-ttu-id="91367-298">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="91367-298">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="91367-300">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-300">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-301">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-301">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-302">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-302">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="91367-303">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="91367-303">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="91367-304">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="91367-304">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="91367-305">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="91367-305">Many-to-Many Relationships</span></span>

<span data-ttu-id="91367-306">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-306">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="91367-307">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="91367-307">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="91367-308">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="91367-308">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="91367-309">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="91367-309">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="91367-310">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="91367-310">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="91367-311">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="91367-311">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="91367-313">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-313">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="91367-314">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="91367-314">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="91367-315">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="91367-315">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="91367-316">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="91367-316">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="91367-317">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="91367-317">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="91367-318">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="91367-318">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="91367-319">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="91367-319">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="91367-321">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-321">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="91367-322">Die m:n-Beziehung zwischen „Instructor“ und „Courses“ erfordert eine Jointabelle, und die Entität für diese Jointabelle ist „CourseAssignment“.</span><span class="sxs-lookup"><span data-stu-id="91367-322">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="91367-324">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="91367-324">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="91367-325">Der Name der Jointabelle für „Instructor“ und „Courses“ würde mithilfe dieses Musters beispielsweise `CourseInstructor` lauten.</span><span class="sxs-lookup"><span data-stu-id="91367-325">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="91367-326">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="91367-326">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="91367-327">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="91367-327">Data models start out simple and grow.</span></span> <span data-ttu-id="91367-328">Jointabellen ohne Nutzlast (PJTs) entwickeln sich häufig so, dass sie eine Nutzlast beinhalten.</span><span class="sxs-lookup"><span data-stu-id="91367-328">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="91367-329">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="91367-329">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="91367-330">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="91367-330">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="91367-331">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="91367-331">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="91367-332">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="91367-332">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="91367-333">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="91367-333">Composite key</span></span>

<span data-ttu-id="91367-334">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="91367-334">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="91367-335">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-335">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="91367-336">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-336">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="91367-337">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-337">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="91367-338">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="91367-338">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="91367-339">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="91367-339">The composite key ensures that:</span></span>

* <span data-ttu-id="91367-340">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-340">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="91367-341">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-341">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="91367-342">Mehrere Zeilen für denselben Dozenten und Kurs unzulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-342">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="91367-343">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="91367-343">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="91367-344">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="91367-344">To prevent such duplicates:</span></span>

* <span data-ttu-id="91367-345">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="91367-345">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="91367-346">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="91367-346">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="91367-347">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="91367-347">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="91367-348">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="91367-348">Update the database context</span></span>

<span data-ttu-id="91367-349">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-349">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="91367-350">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="91367-350">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="91367-351">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="91367-351">Fluent API alternative to attributes</span></span>

<span data-ttu-id="91367-352">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="91367-352">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="91367-353">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="91367-353">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="91367-354">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="91367-354">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="91367-355">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="91367-355">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="91367-356">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="91367-356">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="91367-357">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-357">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="91367-358">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="91367-358">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="91367-359">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="91367-359">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="91367-360">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-360">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="91367-361">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="91367-361">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="91367-362">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="91367-362">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="91367-363">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="91367-363">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="91367-364">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="91367-364">Choose one of these two approaches.</span></span>
* <span data-ttu-id="91367-365">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="91367-365">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="91367-366">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="91367-366">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="91367-367">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="91367-367">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="91367-368">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="91367-368">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="91367-369">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="91367-369">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="91367-370">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="91367-370">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="91367-371">Entitätsdiagramm</span><span class="sxs-lookup"><span data-stu-id="91367-371">Entity diagram</span></span>

<span data-ttu-id="91367-372">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-372">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="91367-374">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="91367-374">The preceding diagram shows:</span></span>

* <span data-ttu-id="91367-375">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="91367-375">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="91367-376">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="91367-376">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="91367-377">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="91367-377">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="91367-378">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-378">Seed the database</span></span>

<span data-ttu-id="91367-379">Aktualisieren Sie den Code in *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="91367-379">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="91367-380">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="91367-380">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="91367-381">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="91367-381">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="91367-382">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="91367-382">The sample data is used for testing.</span></span> <span data-ttu-id="91367-383">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="91367-383">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="91367-384">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="91367-384">Add a migration</span></span>

<span data-ttu-id="91367-385">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="91367-385">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-386">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91367-387">Führen Sie in der PMC den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="91367-387">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="91367-388">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="91367-388">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="91367-389">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="91367-389">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="91367-390">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler zu behandeln ist.</span><span class="sxs-lookup"><span data-stu-id="91367-390">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91367-392">Wenn Sie eine Migration hinzufügen und den `database update`-Befehl ausführen, wird der folgende Fehler generiert:</span><span class="sxs-lookup"><span data-stu-id="91367-392">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="91367-393">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler vermieden werden kann.</span><span class="sxs-lookup"><span data-stu-id="91367-393">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="91367-394">Anwenden der Migration oder Löschen und erneutes Erstellen</span><span class="sxs-lookup"><span data-stu-id="91367-394">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="91367-395">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="91367-395">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="91367-396">In diesem Tutorial werden zwei Alternativen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="91367-396">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="91367-397">[Löschen und Neuerstellen der Datenbank](#drop).</span><span class="sxs-lookup"><span data-stu-id="91367-397">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="91367-398">Wählen Sie diesen Abschnitt aus, wenn Sie SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-398">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="91367-399">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="91367-399">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="91367-400">Die Anleitungen in diesem Abschnitt funktionieren nur für SQL Server, **nicht für SQLite**.</span><span class="sxs-lookup"><span data-stu-id="91367-400">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="91367-401">Beide Optionen funktionieren für SQL Server.</span><span class="sxs-lookup"><span data-stu-id="91367-401">Either choice works for SQL Server.</span></span> <span data-ttu-id="91367-402">Obwohl die apply-migration-Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="91367-402">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="91367-403">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-403">Drop and re-create the database</span></span>

<span data-ttu-id="91367-404">[Überspringen Sie diesen Abschnitt](#apply-the-migration), wenn Sie SQL Server verwenden und den apply-migration-Ansatz im folgenden Abschnitt ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="91367-404">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="91367-405">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="91367-405">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91367-407">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="91367-407">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="91367-408">Löschen Sie den Ordner *Migrations*, und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="91367-408">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91367-410">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="91367-410">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="91367-411">Der Projektordner enthält die Datei *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="91367-411">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="91367-412">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="91367-412">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="91367-413">Löschen Sie den Ordner *Migrations*, und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="91367-413">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="91367-414">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="91367-414">Run the app.</span></span> <span data-ttu-id="91367-415">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="91367-415">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="91367-416">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="91367-416">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-417">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-417">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91367-418">Öffnen Sie die Datenbank in SSOX:</span><span class="sxs-lookup"><span data-stu-id="91367-418">Open the database in SSOX:</span></span>

* <span data-ttu-id="91367-419">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="91367-419">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="91367-420">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="91367-420">Expand the **Tables** node.</span></span> <span data-ttu-id="91367-421">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-421">The created tables are displayed.</span></span>

  ![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="91367-423">Überprüfen Sie die Tabelle **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="91367-423">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="91367-424">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="91367-424">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="91367-425">Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="91367-425">Verify the **CourseAssignment** table contains data.</span></span>

  ![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91367-428">Verwenden Sie das SQLite-Tool, um die Datenbank zu untersuchen:</span><span class="sxs-lookup"><span data-stu-id="91367-428">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="91367-429">Neue Tabellen und Spalten.</span><span class="sxs-lookup"><span data-stu-id="91367-429">New tables and columns.</span></span>
* <span data-ttu-id="91367-430">Mit Daten aufgefüllte Tabellen, z.B. die **CourseAssignment**-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="91367-430">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="91367-431">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="91367-431">Apply the migration</span></span>

<span data-ttu-id="91367-432">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="91367-432">This section is optional.</span></span> <span data-ttu-id="91367-433">Diese Schritte funktionieren nur für SQL Server LocalDB und nur dann, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="91367-433">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="91367-434">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="91367-435">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="91367-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="91367-436">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="91367-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="91367-437">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="91367-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="91367-438">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) abgeschlossen haben.</span><span class="sxs-lookup"><span data-stu-id="91367-438">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="91367-439">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="91367-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="91367-440">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="91367-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="91367-441">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="91367-441">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="91367-442">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="91367-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="91367-443">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="91367-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="91367-444">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="91367-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="91367-445">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="91367-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="91367-446">Aktualisieren Sie in der `ComplexDataModel`-Migrationsklasse die `Up`-Methode:</span><span class="sxs-lookup"><span data-stu-id="91367-446">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="91367-447">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="91367-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="91367-448">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="91367-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="91367-449">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="91367-449">Add the following highlighted code.</span></span> <span data-ttu-id="91367-450">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="91367-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="91367-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="91367-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="91367-452">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel.Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="91367-452">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="91367-453">Die Vorgehensweise zum Behandeln der hier gezeigten Situation wurde für dieses Tutorial vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="91367-453">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="91367-454">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="91367-454">A production app would:</span></span>

* <span data-ttu-id="91367-455">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="91367-455">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="91367-456">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="91367-456">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91367-458">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="91367-458">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="91367-459">Da die `DbInitializer.Initialize`-Methode nur für die Verwendung mit einer leeren Datenbank konzipiert ist, löschen Sie mithilfe von SSOX alle Zeilen in den Tabellen „Student“ und „Course“.</span><span class="sxs-lookup"><span data-stu-id="91367-459">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="91367-460">(Der kaskadierende Löschvorgang übernimmt die Tabelle „Enrollment“.)</span><span class="sxs-lookup"><span data-stu-id="91367-460">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91367-462">Wenn Sie SQL Server LocalDB mit Visual Studio Code verwenden, führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="91367-462">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="91367-463">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="91367-463">Run the app.</span></span> <span data-ttu-id="91367-464">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="91367-464">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="91367-465">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="91367-465">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="91367-466">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="91367-466">Next steps</span></span>

<span data-ttu-id="91367-467">In den nächsten beiden Tutorials wird gezeigt, wie verwandte Daten gelesen und aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="91367-467">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="91367-468">[Vorheriges Tutorial](xref:data/ef-rp/migrations)
> [Nächstes Tutorial](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="91367-468">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91367-469">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="91367-469">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="91367-470">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="91367-470">In this tutorial:</span></span>

* <span data-ttu-id="91367-471">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="91367-471">More entities and relationships are added.</span></span>
* <span data-ttu-id="91367-472">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-472">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="91367-473">Die Entitätsklassen des vollständigen Datenmodells werden in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="91367-473">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="91367-475">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="91367-475">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="91367-476">Anpassen des Datenmodells mithilfe von Attributen</span><span class="sxs-lookup"><span data-stu-id="91367-476">Customize the data model with attributes</span></span>

<span data-ttu-id="91367-477">In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.</span><span class="sxs-lookup"><span data-stu-id="91367-477">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="91367-478">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-478">The DataType attribute</span></span>

<span data-ttu-id="91367-479">Die Seite für Studenten zeigt derzeit die Uhrzeit des Anmeldedatums an.</span><span class="sxs-lookup"><span data-stu-id="91367-479">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="91367-480">Üblicherweise zeigen Datumsfelder nur das Datum an, nicht die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="91367-480">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="91367-481">Aktualisieren Sie *Models/Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="91367-481">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="91367-482">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="91367-482">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="91367-483">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="91367-483">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="91367-484">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="91367-484">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="91367-485">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="91367-485">For example:</span></span>

* <span data-ttu-id="91367-486">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="91367-486">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="91367-487">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="91367-487">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="91367-488">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus, die von HTML5-Browsern genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-488">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="91367-489">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="91367-489">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="91367-490">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-490">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="91367-491">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-491">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="91367-492">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="91367-492">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="91367-493">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="91367-493">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="91367-494">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-494">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="91367-495">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-495">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="91367-496">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-496">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="91367-497">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-497">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="91367-498">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="91367-498">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="91367-499">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="91367-499">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="91367-500">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="91367-500">The browser can enable HTML5 features.</span></span> <span data-ttu-id="91367-501">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.</span><span class="sxs-lookup"><span data-stu-id="91367-501">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="91367-502">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="91367-502">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="91367-503">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="91367-503">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="91367-504">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="91367-504">Run the app.</span></span> <span data-ttu-id="91367-505">Navigieren Sie zur Indexseite „Studenten“.</span><span class="sxs-lookup"><span data-stu-id="91367-505">Navigate to the Students Index page.</span></span> <span data-ttu-id="91367-506">Die Uhrzeiten werden nicht mehr angezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-506">Times are no longer displayed.</span></span> <span data-ttu-id="91367-507">Jede Ansicht, die das `Student`-Modell verwendet, zeigt das Datum ohne Uhrzeit an.</span><span class="sxs-lookup"><span data-stu-id="91367-507">Every view that uses the `Student` model displays the date without time.</span></span>

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="91367-509">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-509">The StringLength attribute</span></span>

<span data-ttu-id="91367-510">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-510">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="91367-511">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="91367-511">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="91367-512">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="91367-512">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="91367-513">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="91367-513">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="91367-514">Aktualisieren Sie das `Student`-Modell mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-514">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="91367-515">Der vorangehende Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="91367-515">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="91367-516">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="91367-516">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="91367-517">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) wird verwendet, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="91367-517">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="91367-518">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="91367-518">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="91367-519">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="91367-519">Run the app:</span></span>

* <span data-ttu-id="91367-520">Navigieren Sie zur Seite für Studenten.</span><span class="sxs-lookup"><span data-stu-id="91367-520">Navigate to the Students page.</span></span>
* <span data-ttu-id="91367-521">Klicken Sie auf **Neu erstellen**, und geben Sie einen Namen ein, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-521">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="91367-522">Wenn Sie auf **Erstellen** klicken, zeigt die clientseitige Validierung eine Fehlermeldung an.</span><span class="sxs-lookup"><span data-stu-id="91367-522">Select **Create**, client-side validation shows an error message.</span></span>

![Indexseite „Studenten“, die Fehler für die Zeichenfolgenlänge anzeigt](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="91367-524">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="91367-524">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="91367-526">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="91367-526">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="91367-527">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf, da die Migrationen nicht auf der Datenbank ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="91367-527">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="91367-528">Wenn die Migrationen im Verlauf dieses Tutorials ausgeführt werden, ändern sich die Namensfelder in `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="91367-528">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="91367-529">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-529">The Column attribute</span></span>

<span data-ttu-id="91367-530">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-530">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="91367-531">In diesem Abschnitt wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="91367-531">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="91367-532">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="91367-532">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="91367-533">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="91367-533">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="91367-534">Aktualisieren Sie die Datei *Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="91367-534">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="91367-535">Durch die zuvor vorgenommene Änderung wird `Student.FirstMidName` in der App der `FirstName`-Spalte der `Student`-Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="91367-535">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="91367-536">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="91367-536">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="91367-537">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="91367-537">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="91367-538">Wenn die App ausgeführt wird, bevor Migrationen angewendet werden, wird folgende Ausnahme generiert:</span><span class="sxs-lookup"><span data-stu-id="91367-538">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="91367-539">So aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="91367-539">To update the DB:</span></span>

* <span data-ttu-id="91367-540">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="91367-540">Build the project.</span></span>
* <span data-ttu-id="91367-541">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="91367-541">Open a command window in the project folder.</span></span> <span data-ttu-id="91367-542">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="91367-542">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-543">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="91367-545">Der Befehl `migrations add ColumnFirstName` generiert folgende Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="91367-545">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="91367-546">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="91367-546">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="91367-547">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="91367-547">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="91367-548">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="91367-548">Test the app.</span></span>

<span data-ttu-id="91367-549">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="91367-549">Open the Student table in SSOX:</span></span>

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="91367-551">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="91367-551">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="91367-552">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="91367-552">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="91367-553">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="91367-553">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="91367-554">Im folgenden Abschnitt werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="91367-554">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="91367-555">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="91367-555">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="91367-556">Aktualisieren der Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="91367-556">Student entity update</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="91367-558">Aktualisieren Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-558">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="91367-559">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="91367-559">The Required attribute</span></span>

<span data-ttu-id="91367-560">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="91367-560">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="91367-561">Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (`DateTime`, `int`, `double` usw.) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="91367-561">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="91367-562">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="91367-562">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="91367-563">Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="91367-563">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="91367-564">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-564">The Display attribute</span></span>

<span data-ttu-id="91367-565">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="91367-565">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="91367-566">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="91367-566">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="91367-567">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="91367-567">The FullName calculated property</span></span>

<span data-ttu-id="91367-568">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-568">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="91367-569">`FullName` verfügt lediglich über einen get-Accessor und kann daher nicht festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-569">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="91367-570">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="91367-570">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="91367-571">Erstellen der Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="91367-571">Create the Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="91367-573">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-573">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="91367-574">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="91367-574">Multiple attributes can be on one line.</span></span> <span data-ttu-id="91367-575">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="91367-575">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="91367-576">Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="91367-576">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="91367-577">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="91367-577">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="91367-578">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="91367-578">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="91367-579">Folgendes gilt, wenn eine Navigationseigenschaft mehrere Entitäten enthält:</span><span class="sxs-lookup"><span data-stu-id="91367-579">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="91367-580">Bei der Eigenschaft muss es sich um einen Listentyp handeln, bei dem Einträge hinzugefügt, gelöscht und aktualisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="91367-580">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="91367-581">Folgendes gilt für die Typen von Navigationseigenschaften:</span><span class="sxs-lookup"><span data-stu-id="91367-581">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="91367-582">Wenn `ICollection<T>` angegeben wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="91367-582">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="91367-583">Die `CourseAssignment`-Entität wird im Abschnitt über m:n-Beziehungen erläutert.</span><span class="sxs-lookup"><span data-stu-id="91367-583">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="91367-584">Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann.</span><span class="sxs-lookup"><span data-stu-id="91367-584">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="91367-585">Die `OfficeAssignment`-Eigenschaft enthält also eine einzige `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-585">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="91367-586">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-586">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="91367-587">Erstellen der Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="91367-587">Create the OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="91367-589">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-589">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="91367-590">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-590">The Key attribute</span></span>

<span data-ttu-id="91367-591">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="91367-591">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="91367-592">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-592">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="91367-593">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-593">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="91367-594">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-594">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="91367-595">Entity Framework Core erkennt `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment`, weil</span><span class="sxs-lookup"><span data-stu-id="91367-595">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="91367-596">`InstructorID` der Namenskonvention „ID“ oder „classnameID“ nicht folgt.</span><span class="sxs-lookup"><span data-stu-id="91367-596">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="91367-597">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="91367-597">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="91367-598">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-598">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="91367-599">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="91367-599">The Instructor navigation property</span></span>

<span data-ttu-id="91367-600">Die `OfficeAssignment`-Navigationseigenschaft für die `Instructor`-Entität ist auf NULL festlegbar, weil:</span><span class="sxs-lookup"><span data-stu-id="91367-600">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="91367-601">Referenztypen (z.B. Klassen) auf NULL festlegbar sind.</span><span class="sxs-lookup"><span data-stu-id="91367-601">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="91367-602">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="91367-602">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="91367-603">Die `OfficeAssignment`-Entität besitzt eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft, weil:</span><span class="sxs-lookup"><span data-stu-id="91367-603">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="91367-604">`InstructorID` nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="91367-604">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="91367-605">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="91367-605">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="91367-606">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="91367-606">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="91367-607">Das `[Required]`-Attribut kann auf die `Instructor`-Navigationseigenschaft angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="91367-607">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="91367-608">Der vorangehende Code legt fest, dass ein zugehöriger Dozent vorhanden sein muss.</span><span class="sxs-lookup"><span data-stu-id="91367-608">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="91367-609">Der vorangehende Code ist nicht erforderlich, da der Fremdschlüssel `InstructorID` (der ebenfalls der Primärschlüssel ist) nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="91367-609">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="91367-610">Ändern der Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="91367-610">Modify the Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="91367-612">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-612">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="91367-613">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="91367-613">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="91367-614">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-614">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="91367-615">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="91367-615">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="91367-616">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="91367-616">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="91367-617">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="91367-617">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="91367-618">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-618">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="91367-619">Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-619">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="91367-620">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="91367-620">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="91367-621">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="91367-621">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="91367-622">Die `Department`-Entität ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="91367-622">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="91367-623">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="91367-623">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="91367-624">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="91367-624">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="91367-625">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="91367-625">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="91367-626">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="91367-626">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="91367-627">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="91367-627">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="91367-628">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="91367-628">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="91367-629">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="91367-629">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="91367-630">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="91367-630">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="91367-631">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-631">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="91367-632">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="91367-632">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="91367-633">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="91367-633">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-634">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-634">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-635">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-635">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="91367-636">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="91367-636">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="91367-637">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="91367-637">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="91367-638">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="91367-638">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="91367-639">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="91367-639">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="91367-640">Erstellen der Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="91367-640">Create the Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="91367-642">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-642">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="91367-643">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="91367-643">The Column attribute</span></span>

<span data-ttu-id="91367-644">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="91367-644">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="91367-645">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="91367-645">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="91367-646">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="91367-646">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="91367-647">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="91367-647">Column mapping is generally not required.</span></span> <span data-ttu-id="91367-648">Entity Framework Core wählt üblicherweise den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="91367-648">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="91367-649">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="91367-649">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="91367-650">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="91367-650">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-651">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-651">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-652">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-652">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="91367-653">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="91367-653">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="91367-654">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="91367-654">An administrator is always an instructor.</span></span> <span data-ttu-id="91367-655">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="91367-655">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="91367-656">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="91367-656">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="91367-657">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="91367-657">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="91367-658">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="91367-658">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="91367-659">Hinweis: Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="91367-659">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="91367-660">Das kaskadierende Delete kann zu Zirkelregeln für kaskadierende Deletes führen.</span><span class="sxs-lookup"><span data-stu-id="91367-660">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="91367-661">Durch Zirkelregeln für kaskadierende Deletes wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-661">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="91367-662">Beispielsweise dann, wenn die `Department.InstructorID`-Eigenschaft als nicht auf NULL festlegbar definiert wurde:</span><span class="sxs-lookup"><span data-stu-id="91367-662">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="91367-663">Entity Framework Core konfiguriert eine Regel für kaskadierende Deletes, um den Fachbereich zu löschen, wenn ein Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="91367-663">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="91367-664">Das Löschen eines Fachbereichs in Folge des Löschens eines Dozenten stellt nicht das beabsichtigte Verhalten dar.</span><span class="sxs-lookup"><span data-stu-id="91367-664">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="91367-665">Die folgende Fluent-API würde eine restrict-Regel anstelle einer Kascade festlegen.</span><span class="sxs-lookup"><span data-stu-id="91367-665">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="91367-666">Durch den vorangehenden Code werden kaskadierende Deletes für die Beziehung zwischen „Department“ und „Instructor“ deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="91367-666">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="91367-667">Aktualisieren der Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="91367-667">Update the Enrollment entity</span></span>

<span data-ttu-id="91367-668">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="91367-668">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="91367-670">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-670">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="91367-671">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="91367-671">Foreign key and navigation properties</span></span>

<span data-ttu-id="91367-672">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="91367-672">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="91367-673">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="91367-673">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="91367-674">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="91367-674">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="91367-675">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="91367-675">Many-to-Many Relationships</span></span>

<span data-ttu-id="91367-676">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="91367-676">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="91367-677">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="91367-677">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="91367-678">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="91367-678">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="91367-679">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="91367-679">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="91367-680">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="91367-680">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="91367-681">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="91367-681">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="91367-683">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-683">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="91367-684">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="91367-684">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="91367-685">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="91367-685">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="91367-686">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="91367-686">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="91367-687">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="91367-687">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="91367-688">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="91367-688">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="91367-689">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="91367-689">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="91367-691">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="91367-691">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="91367-692">Beziehung zwischen „Instructor“ und „Course“</span><span class="sxs-lookup"><span data-stu-id="91367-692">Instructor-to-Courses</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="91367-694">Folgendes gilt für die m:n-Beziehung zwischen „Instructor“ und „Course“:</span><span class="sxs-lookup"><span data-stu-id="91367-694">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="91367-695">Eine Jointabelle ist erforderlich, die durch eine Entitätenmenge dargestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="91367-695">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="91367-696">Diese muss eine reine Jointabelle (eine Tabelle ohne Nutzlast) sein.</span><span class="sxs-lookup"><span data-stu-id="91367-696">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="91367-697">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="91367-697">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="91367-698">Der Name der Jointabelle für „Instructor“ und „Course“ lautet mithilfe dieses Musters beispielsweise `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="91367-698">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="91367-699">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="91367-699">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="91367-700">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="91367-700">Data models start out simple and grow.</span></span> <span data-ttu-id="91367-701">Währenddessen erhalten Joins ohne Nutzlast häufig Nutzlasten.</span><span class="sxs-lookup"><span data-stu-id="91367-701">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="91367-702">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="91367-702">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="91367-703">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="91367-703">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="91367-704">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="91367-704">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="91367-705">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="91367-705">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="91367-706">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="91367-706">Composite key</span></span>

<span data-ttu-id="91367-707">Fremdschlüssel sind nicht auf NULL festlegbar.</span><span class="sxs-lookup"><span data-stu-id="91367-707">FKs are not nullable.</span></span> <span data-ttu-id="91367-708">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="91367-708">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="91367-709">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-709">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="91367-710">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="91367-710">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="91367-711">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="91367-711">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="91367-712">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="91367-712">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="91367-713">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="91367-713">The composite key ensures:</span></span>

* <span data-ttu-id="91367-714">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-714">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="91367-715">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-715">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="91367-716">Mehrere Zeilen für denselben Dozenten und Kurs nicht zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="91367-716">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="91367-717">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="91367-717">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="91367-718">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="91367-718">To prevent such duplicates:</span></span>

* <span data-ttu-id="91367-719">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="91367-719">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="91367-720">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="91367-720">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="91367-721">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="91367-721">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="91367-722">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="91367-722">Update the DB context</span></span>

<span data-ttu-id="91367-723">Fügen Sie folgenden hervorgehobenen Code zu *Data/SchoolContext.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="91367-723">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="91367-724">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="91367-724">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="91367-725">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="91367-725">Fluent API alternative to attributes</span></span>

<span data-ttu-id="91367-726">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="91367-726">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="91367-727">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="91367-727">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="91367-728">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="91367-728">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="91367-729">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="91367-729">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="91367-730">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="91367-730">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="91367-731">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-731">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="91367-732">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="91367-732">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="91367-733">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="91367-733">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="91367-734">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91367-734">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="91367-735">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="91367-735">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="91367-736">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="91367-736">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="91367-737">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="91367-737">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="91367-738">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="91367-738">Choose one of these two approaches.</span></span>
* <span data-ttu-id="91367-739">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="91367-739">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="91367-740">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="91367-740">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="91367-741">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="91367-741">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="91367-742">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="91367-742">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="91367-743">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="91367-743">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="91367-744">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="91367-744">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="91367-745">Entitätsdiagramm mit angezeigten Beziehungen</span><span class="sxs-lookup"><span data-stu-id="91367-745">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="91367-746">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="91367-746">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="91367-748">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="91367-748">The preceding diagram shows:</span></span>

* <span data-ttu-id="91367-749">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="91367-749">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="91367-750">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="91367-750">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="91367-751">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="91367-751">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="91367-752">Ausführen eines Seedings mit Testdaten für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-752">Seed the DB with Test Data</span></span>

<span data-ttu-id="91367-753">Aktualisieren Sie den Code in *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="91367-753">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="91367-754">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="91367-754">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="91367-755">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="91367-755">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="91367-756">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="91367-756">The sample data is used for testing.</span></span> <span data-ttu-id="91367-757">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="91367-757">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="91367-758">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="91367-758">Add a migration</span></span>

<span data-ttu-id="91367-759">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="91367-759">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-760">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-760">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-761">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-761">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="91367-762">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="91367-762">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="91367-763">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="91367-763">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="91367-764">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="91367-764">Apply the migration</span></span>

<span data-ttu-id="91367-765">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie zukünftig Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="91367-765">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="91367-766">In diesem Tutorial werden zwei Vorgehensweisen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="91367-766">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="91367-767">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-767">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="91367-768">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="91367-768">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="91367-769">Obwohl diese Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="91367-769">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="91367-770">**Hinweis:** Dies ist ein optionaler Abschnitt des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="91367-770">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="91367-771">Sie können diesen Abschnitt überspringen und die Schritte zum Löschen und Neuerstellen durchführen.</span><span class="sxs-lookup"><span data-stu-id="91367-771">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="91367-772">Wenn Sie stattdessen die Schritte in diesem Abschnitt ausführen möchten, führen Sie nicht die Schritte zum Löschen und Neuerstellen aus.</span><span class="sxs-lookup"><span data-stu-id="91367-772">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="91367-773">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-773">Drop and re-create the database</span></span>

<span data-ttu-id="91367-774">Durch den Code in der aktualisierten `DbInitializer`-Klasse werden Startwertdaten für die neuen Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="91367-774">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="91367-775">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="91367-775">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91367-776">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91367-776">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91367-777">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="91367-777">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="91367-778">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="91367-778">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="91367-779">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91367-779">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91367-780">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="91367-780">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="91367-781">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="91367-781">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="91367-782">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="91367-782">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="91367-783">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="91367-783">Run the app.</span></span> <span data-ttu-id="91367-784">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="91367-784">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="91367-785">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="91367-785">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="91367-786">Öffnen Sie die Datenbank im SSOX:</span><span class="sxs-lookup"><span data-stu-id="91367-786">Open the DB in SSOX:</span></span>

* <span data-ttu-id="91367-787">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="91367-787">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="91367-788">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="91367-788">Expand the **Tables** node.</span></span> <span data-ttu-id="91367-789">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="91367-789">The created tables are displayed.</span></span>

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="91367-791">Überprüfen Sie die Tabelle **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="91367-791">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="91367-792">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="91367-792">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="91367-793">Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="91367-793">Verify the **CourseAssignment** table contains data.</span></span>

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="91367-795">Anwenden der Migration auf die vorhandene Datenbank</span><span class="sxs-lookup"><span data-stu-id="91367-795">Apply the migration to the existing database</span></span>

<span data-ttu-id="91367-796">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="91367-796">This section is optional.</span></span> <span data-ttu-id="91367-797">Diese Schritte funktionieren nur, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="91367-797">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="91367-798">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="91367-798">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="91367-799">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="91367-799">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="91367-800">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="91367-800">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="91367-801">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="91367-801">Don't make these code changes without a backup.</span></span> <span data-ttu-id="91367-802">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="91367-802">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="91367-803">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="91367-803">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="91367-804">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="91367-804">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="91367-805">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="91367-805">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="91367-806">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="91367-806">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="91367-807">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="91367-807">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="91367-808">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="91367-808">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="91367-809">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="91367-809">Fix the foreign key constraints</span></span>

<span data-ttu-id="91367-810">Aktualisieren Sie die `Up`-Methode der `ComplexDataModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="91367-810">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="91367-811">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="91367-811">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="91367-812">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="91367-812">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="91367-813">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="91367-813">Add the following highlighted code.</span></span> <span data-ttu-id="91367-814">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="91367-814">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="91367-815">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel` `Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="91367-815">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="91367-816">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="91367-816">A production app would:</span></span>

* <span data-ttu-id="91367-817">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="91367-817">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="91367-818">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="91367-818">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="91367-819">Im folgenden Tutorial werden verknüpfte Daten behandelt.</span><span class="sxs-lookup"><span data-stu-id="91367-819">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91367-820">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="91367-820">Additional resources</span></span>

* [<span data-ttu-id="91367-821">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="91367-821">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="91367-822">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="91367-822">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="91367-823">[Zurück](xref:data/ef-rp/migrations)
> [Weiter](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="91367-823">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end