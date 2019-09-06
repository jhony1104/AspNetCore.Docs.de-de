---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)'
author: tdykstra
description: In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie Regeln zur Formatierung, Validierung und Zuordnung angeben.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: ab29cf687c80551d275cae69f28b7576016bfff6
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238122"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="781fa-103">Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)</span><span class="sxs-lookup"><span data-stu-id="781fa-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="781fa-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="781fa-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="781fa-105">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="781fa-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="781fa-106">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="781fa-106">In this tutorial:</span></span>

* <span data-ttu-id="781fa-107">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="781fa-108">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="781fa-109">Das vollständige Datenmodell wird in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="781fa-109">The completed data model is shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="781fa-111">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="781fa-111">The Student entity</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="781fa-113">Ersetzen Sie den Code in *Models/Student.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="781fa-114">Der Code oben fügt eine `FullName`-Eigenschaft hinzu und fügt den vorhandenen Eigenschaften die folgenden Attribute hinzu:</span><span class="sxs-lookup"><span data-stu-id="781fa-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="781fa-115">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="781fa-115">The FullName calculated property</span></span>

<span data-ttu-id="781fa-116">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="781fa-117">`FullName` kann nicht festgelegt werden und verfügt daher lediglich über einen get-Accessor.</span><span class="sxs-lookup"><span data-stu-id="781fa-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="781fa-118">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="781fa-119">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="781fa-120">Für die Anmeldedaten von Studenten zeigen alle Webseiten derzeit die Zeit und das Datum an, obwohl nur das Datum relevant ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="781fa-121">Indem Sie Attribute für die Datenanmerkung verwenden, können Sie eine Codeänderungen vornehmen, durch die das Anzeigeformat auf jeder Seite korrigiert wird, in der die Daten angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="781fa-122">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="781fa-123">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="781fa-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="781fa-124">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="781fa-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="781fa-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="781fa-125">For example:</span></span>

* <span data-ttu-id="781fa-126">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="781fa-127">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="781fa-128">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="781fa-129">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="781fa-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="781fa-130">Das DisplayFormat-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="781fa-131">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="781fa-132">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="781fa-133">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats.</span><span class="sxs-lookup"><span data-stu-id="781fa-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="781fa-134">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="781fa-135">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="781fa-136">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="781fa-137">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="781fa-138">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="781fa-139">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="781fa-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="781fa-140">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="781fa-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="781fa-141">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="781fa-142">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links und einigen clientseitigen Eingabevalidierungen.</span><span class="sxs-lookup"><span data-stu-id="781fa-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="781fa-143">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="781fa-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="781fa-144">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="781fa-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="781fa-145">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="781fa-146">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="781fa-147">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="781fa-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="781fa-148">Der gezeigte Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="781fa-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="781fa-149">Ein Beispiel, das die minimale Zeichenfolgenlänge festlegt, wird [später](#the-required-attribute) gezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="781fa-150">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="781fa-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="781fa-151">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="781fa-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="781fa-152">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="781fa-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="781fa-153">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) kann verwendet werden, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="781fa-154">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="781fa-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="781fa-156">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="781fa-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="781fa-158">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="781fa-159">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="781fa-160">Wenn eine Migration später in diesem Tutorial erstellt und angewendet wird, werden die Namensfelder `nvarchar(50)` als Ergebnis der Attribute für die Zeichenfolgenlänge.</span><span class="sxs-lookup"><span data-stu-id="781fa-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="781fa-162">Überprüfen Sie in Ihrem SQLite-Tool die Spaltendefinitionen für die `Student`-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="781fa-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="781fa-163">Die Namensfelder weisen den Typ `Text` auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-163">The name fields have type `Text`.</span></span> <span data-ttu-id="781fa-164">Beachten Sie, dass das Feld für den Vornamen den Namen `FirstMidName` trägt.</span><span class="sxs-lookup"><span data-stu-id="781fa-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="781fa-165">Im nächsten Abschnitt ändern Sie den Namen dieser Spalte in `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="781fa-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="781fa-166">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="781fa-167">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="781fa-168">Im `Student`-Modell wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="781fa-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="781fa-169">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="781fa-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="781fa-170">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="781fa-171">Mit dem `[Column]`-Attribut wird `Student.FirstMidName` im Datenmodell der Spalte `FirstName` in der Tabelle `Student` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="781fa-172">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="781fa-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="781fa-173">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="781fa-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="781fa-174">Diese Diskrepanz wird durch Hinzufügen einer Migration zu einem späteren Zeitpunkt in diesem Tutorial aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="781fa-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="781fa-175">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="781fa-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="781fa-176">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="781fa-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="781fa-177">Das `Required`-Attribut ist für Typen, die keine NULL-Werte annehmen können (etwas Werttypen, z.B. `DateTime`, `int` und `double`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="781fa-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="781fa-178">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="781fa-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="781fa-179">Das `Required`-Attribut muss mit `MinimumLength` verwendet werden, damit die `MinimumLength` erzwungen wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="781fa-180">`MinimumLength` und `Required` lassen Leerzeichen zu, um die Überprüfung zu bestehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="781fa-181">Verwenden Sie das `RegularExpression`-Attribut, um vollständige Kontrolle über die Zeichenfolge zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="781fa-182">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="781fa-183">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="781fa-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="781fa-184">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="781fa-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="781fa-185">Erstellen einer Migration</span><span class="sxs-lookup"><span data-stu-id="781fa-185">Create a migration</span></span>

<span data-ttu-id="781fa-186">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="781fa-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="781fa-187">Es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="781fa-187">An exception is thrown.</span></span> <span data-ttu-id="781fa-188">Das `[Column]`-Attribut bewirkt, dass EF eine Spalte mit dem Namen `FirstName` erwartet, aber der Spaltenname in der Datenbank ist weiterhin `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="781fa-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="781fa-190">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="781fa-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="781fa-191">Geben Sie in der PMC folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="781fa-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="781fa-192">Mit dem ersten dieser Befehle wird die folgende Warnmeldung generiert:</span><span class="sxs-lookup"><span data-stu-id="781fa-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="781fa-193">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="781fa-194">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="781fa-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="781fa-195">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="781fa-195">Open the Student table in SSOX:</span></span>

  ![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="781fa-197">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="781fa-198">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="781fa-199">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="781fa-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="781fa-201">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="781fa-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="781fa-202">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="781fa-202">Open a command window in the project folder.</span></span> <span data-ttu-id="781fa-203">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="781fa-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="781fa-204">Der Befehl für die Datenbankaktualisierung zeigt einen Fehler wie im folgenden Beispiel an:</span><span class="sxs-lookup"><span data-stu-id="781fa-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="781fa-205">In diesem Tutorial wird die anfängliche Migration gelöscht und erneut erstellt, um diese Fehlermeldung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="781fa-206">Weitere Informationen finden Sie im SQLite-Warnhinweis am Anfang des [Migrationstutorials](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="781fa-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="781fa-207">Löschen Sie den Ordner *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="781fa-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="781fa-208">Führen Sie die folgenden Befehle aus, um die Datenbank zu löschen, eine neue anfängliche Migration zu erstellen und die Migration anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="781fa-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="781fa-209">Überprüfen Sie die Tabelle „Student“ in Ihrem SQLite-Tool.</span><span class="sxs-lookup"><span data-stu-id="781fa-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="781fa-210">Die Spalte, die zunächst FirstMidName hieß, ist jetzt FirstName.</span><span class="sxs-lookup"><span data-stu-id="781fa-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="781fa-211">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="781fa-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="781fa-212">Beachten Sie, dass die Uhrzeiten nicht mit Datumsangaben eingegeben oder angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="781fa-213">Klicken Sie auf **Neu erstellen**, und versuchen Sie, einen Namen einzugeben, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="781fa-214">In den folgenden Abschnitten werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="781fa-215">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="781fa-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="781fa-216">Die Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="781fa-216">The Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="781fa-218">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="781fa-219">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="781fa-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="781fa-220">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="781fa-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="781fa-221">Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-221">Navigation properties</span></span>

<span data-ttu-id="781fa-222">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="781fa-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="781fa-223">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="781fa-224">Ein Dozent kann höchstens ein Büro haben, sodass die `OfficeAssignment`-Eigenschaft eine einzelne `OfficeAssignment`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="781fa-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="781fa-225">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="781fa-226">Die Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="781fa-226">The OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="781fa-228">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="781fa-229">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-229">The Key attribute</span></span>

<span data-ttu-id="781fa-230">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="781fa-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="781fa-231">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="781fa-232">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="781fa-233">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="781fa-234">EF Core kann `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment` erkennen, da `InstructorID` nicht den ID- oder classnameID-Benennungskonventionen folgt.</span><span class="sxs-lookup"><span data-stu-id="781fa-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="781fa-235">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="781fa-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="781fa-236">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="781fa-237">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="781fa-237">The Instructor navigation property</span></span>

<span data-ttu-id="781fa-238">Die `Instructor.OfficeAssignment`-Navigationseigenschaft kann NULL sein, da möglicherweise keine `OfficeAssignment`-Zeile für einen bestimmten Dozenten vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="781fa-239">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="781fa-240">Die `OfficeAssignment.Instructor`-Navigationseigenschaft hat immer eine Instructor-Entität, da der Typ `InstructorID` des Fremdschlüssels `int` ist, ein Typ, der keine NULL-Werte annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="781fa-241">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="781fa-242">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="781fa-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="781fa-243">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="781fa-243">The Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="781fa-245">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="781fa-246">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="781fa-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="781fa-247">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="781fa-248">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="781fa-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="781fa-249">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="781fa-250">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="781fa-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="781fa-251">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="781fa-252">Durch explizites Einschließen des Fremdschlüssels in das Datenmodell können Updates jedoch einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="781fa-253">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="781fa-254">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="781fa-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="781fa-255">Die `Department`-Eigenschaft ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="781fa-256">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="781fa-257">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="781fa-258">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="781fa-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="781fa-259">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="781fa-260">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="781fa-261">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="781fa-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="781fa-262">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="781fa-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="781fa-263">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="781fa-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="781fa-264">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="781fa-265">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="781fa-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="781fa-266">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="781fa-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-267">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-268">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="781fa-269">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="781fa-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="781fa-270">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="781fa-271">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="781fa-272">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="781fa-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="781fa-273">Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="781fa-273">The Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="781fa-275">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="781fa-276">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-276">The Column attribute</span></span>

<span data-ttu-id="781fa-277">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="781fa-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="781fa-278">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="781fa-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="781fa-279">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="781fa-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="781fa-280">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="781fa-280">Column mapping is generally not required.</span></span> <span data-ttu-id="781fa-281">Entity Framework Core wählt den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="781fa-282">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="781fa-283">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="781fa-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-284">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-285">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="781fa-286">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="781fa-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="781fa-287">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="781fa-287">An administrator is always an instructor.</span></span> <span data-ttu-id="781fa-288">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="781fa-289">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="781fa-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="781fa-290">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="781fa-291">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="781fa-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="781fa-292">Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="781fa-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="781fa-293">Das Standardverhalten kann zu Zirkelregeln für kaskadierende Löschvorgänge führen.</span><span class="sxs-lookup"><span data-stu-id="781fa-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="781fa-294">Durch Zirkelregeln für kaskadierende Löschvorgänge wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="781fa-295">Wenn die `Department.InstructorID`-Eigenschaft beispielsweise als nicht auf NULL festlegbare Eigenschaft definiert wurde, konfiguriert EF Core eine Löschregel für kaskadierende Löschvorgänge.</span><span class="sxs-lookup"><span data-stu-id="781fa-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="781fa-296">In diesem Fall würde der Fachbereich gelöscht, wenn der als Administrator zugewiesene Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="781fa-297">In diesem Szenario wäre eine Einschränkungsregel sinnvoller.</span><span class="sxs-lookup"><span data-stu-id="781fa-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="781fa-298">Die folgende [Fluent-API](#fluent-api-alternative-to-attributes) würde eine Einschränkungsregel festlegen und das kaskadierende Delete deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="781fa-299">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="781fa-299">The Enrollment entity</span></span>

<span data-ttu-id="781fa-300">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-300">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="781fa-302">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-303">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-304">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="781fa-305">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="781fa-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="781fa-306">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="781fa-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="781fa-307">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="781fa-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="781fa-308">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="781fa-309">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="781fa-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="781fa-310">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="781fa-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="781fa-311">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="781fa-312">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="781fa-313">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="781fa-313">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="781fa-315">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="781fa-316">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="781fa-317">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="781fa-318">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="781fa-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="781fa-319">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="781fa-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="781fa-320">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="781fa-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="781fa-321">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="781fa-321">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="781fa-323">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="781fa-324">Die m:n-Beziehung zwischen „Instructor“ und „Courses“ erfordert eine Jointabelle, und die Entität für diese Jointabelle ist „CourseAssignment“.</span><span class="sxs-lookup"><span data-stu-id="781fa-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="781fa-326">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="781fa-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="781fa-327">Der Name der Jointabelle für „Instructor“ und „Courses“ würde mithilfe dieses Musters beispielsweise `CourseInstructor` lauten.</span><span class="sxs-lookup"><span data-stu-id="781fa-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="781fa-328">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="781fa-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="781fa-329">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="781fa-329">Data models start out simple and grow.</span></span> <span data-ttu-id="781fa-330">Jointabellen ohne Nutzlast (PJTs) entwickeln sich häufig so, dass sie eine Nutzlast beinhalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="781fa-331">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="781fa-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="781fa-332">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="781fa-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="781fa-333">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="781fa-334">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="781fa-335">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="781fa-335">Composite key</span></span>

<span data-ttu-id="781fa-336">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="781fa-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="781fa-337">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="781fa-338">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="781fa-339">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="781fa-340">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="781fa-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="781fa-341">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="781fa-341">The composite key ensures that:</span></span>

* <span data-ttu-id="781fa-342">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="781fa-343">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="781fa-344">Mehrere Zeilen für denselben Dozenten und Kurs unzulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="781fa-345">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="781fa-346">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="781fa-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="781fa-347">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="781fa-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="781fa-348">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="781fa-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="781fa-349">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="781fa-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="781fa-350">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="781fa-350">Update the database context</span></span>

<span data-ttu-id="781fa-351">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="781fa-352">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="781fa-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="781fa-353">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="781fa-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="781fa-354">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="781fa-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="781fa-355">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="781fa-356">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="781fa-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="781fa-357">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="781fa-358">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="781fa-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="781fa-359">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="781fa-360">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="781fa-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="781fa-361">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="781fa-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="781fa-362">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="781fa-363">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="781fa-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="781fa-364">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="781fa-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="781fa-365">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="781fa-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="781fa-366">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="781fa-367">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="781fa-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="781fa-368">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="781fa-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="781fa-369">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="781fa-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="781fa-370">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="781fa-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="781fa-371">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="781fa-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="781fa-372">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="781fa-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="781fa-373">Entitätsdiagramm</span><span class="sxs-lookup"><span data-stu-id="781fa-373">Entity diagram</span></span>

<span data-ttu-id="781fa-374">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="781fa-376">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="781fa-377">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="781fa-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="781fa-378">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="781fa-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="781fa-379">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="781fa-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="781fa-380">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-380">Seed the database</span></span>

<span data-ttu-id="781fa-381">Aktualisieren Sie den Code in *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="781fa-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="781fa-382">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="781fa-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="781fa-383">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="781fa-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="781fa-384">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="781fa-384">The sample data is used for testing.</span></span> <span data-ttu-id="781fa-385">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="781fa-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="781fa-386">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="781fa-386">Add a migration</span></span>

<span data-ttu-id="781fa-387">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="781fa-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="781fa-389">Führen Sie in der PMC den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="781fa-390">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="781fa-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="781fa-391">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="781fa-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="781fa-392">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler zu behandeln ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="781fa-394">Wenn Sie eine Migration hinzufügen und den `database update`-Befehl ausführen, wird der folgende Fehler generiert:</span><span class="sxs-lookup"><span data-stu-id="781fa-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="781fa-395">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler vermieden werden kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="781fa-396">Anwenden der Migration oder Löschen und erneutes Erstellen</span><span class="sxs-lookup"><span data-stu-id="781fa-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="781fa-397">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="781fa-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="781fa-398">In diesem Tutorial werden zwei Alternativen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="781fa-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="781fa-399">[Löschen und Neuerstellen der Datenbank](#drop).</span><span class="sxs-lookup"><span data-stu-id="781fa-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="781fa-400">Wählen Sie diesen Abschnitt aus, wenn Sie SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="781fa-401">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="781fa-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="781fa-402">Die Anleitungen in diesem Abschnitt funktionieren nur für SQL Server, **nicht für SQLite**.</span><span class="sxs-lookup"><span data-stu-id="781fa-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="781fa-403">Beide Optionen funktionieren für SQL Server.</span><span class="sxs-lookup"><span data-stu-id="781fa-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="781fa-404">Obwohl die apply-migration-Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="781fa-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="781fa-405">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-405">Drop and re-create the database</span></span>

<span data-ttu-id="781fa-406">[Überspringen Sie diesen Abschnitt](#apply-the-migration), wenn Sie SQL Server verwenden und den apply-migration-Ansatz im folgenden Abschnitt ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="781fa-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="781fa-407">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="781fa-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="781fa-409">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="781fa-410">Löschen Sie den Ordner *Migrations*, und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="781fa-412">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="781fa-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="781fa-413">Der Projektordner enthält die Datei *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="781fa-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="781fa-414">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-414">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="781fa-415">Löschen Sie den Ordner *Migrations*, und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="781fa-416">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-416">Run the app.</span></span> <span data-ttu-id="781fa-417">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="781fa-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="781fa-418">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="781fa-420">Öffnen Sie die Datenbank in SSOX:</span><span class="sxs-lookup"><span data-stu-id="781fa-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="781fa-421">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="781fa-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="781fa-422">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="781fa-422">Expand the **Tables** node.</span></span> <span data-ttu-id="781fa-423">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-423">The created tables are displayed.</span></span>

  ![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="781fa-425">Überprüfen Sie die Tabelle **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="781fa-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="781fa-426">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="781fa-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="781fa-427">Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="781fa-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="781fa-430">Verwenden Sie das SQLite-Tool, um die Datenbank zu untersuchen:</span><span class="sxs-lookup"><span data-stu-id="781fa-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="781fa-431">Neue Tabellen und Spalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-431">New tables and columns.</span></span>
* <span data-ttu-id="781fa-432">Mit Daten aufgefüllte Tabellen, z.B. die **CourseAssignment**-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="781fa-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="781fa-433">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="781fa-433">Apply the migration</span></span>

<span data-ttu-id="781fa-434">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="781fa-434">This section is optional.</span></span> <span data-ttu-id="781fa-435">Diese Schritte funktionieren nur für SQL Server LocalDB und nur dann, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="781fa-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="781fa-436">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="781fa-437">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="781fa-438">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="781fa-439">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="781fa-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="781fa-440">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) abgeschlossen haben.</span><span class="sxs-lookup"><span data-stu-id="781fa-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="781fa-441">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="781fa-442">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="781fa-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="781fa-443">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="781fa-444">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="781fa-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="781fa-445">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="781fa-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="781fa-446">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="781fa-447">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="781fa-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="781fa-448">Aktualisieren Sie in der `ComplexDataModel`-Migrationsklasse die `Up`-Methode:</span><span class="sxs-lookup"><span data-stu-id="781fa-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="781fa-449">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="781fa-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="781fa-450">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="781fa-451">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="781fa-451">Add the following highlighted code.</span></span> <span data-ttu-id="781fa-452">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="781fa-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="781fa-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="781fa-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="781fa-454">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel.Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="781fa-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="781fa-455">Die Vorgehensweise zum Behandeln der hier gezeigten Situation wurde für dieses Tutorial vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="781fa-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="781fa-456">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="781fa-456">A production app would:</span></span>

* <span data-ttu-id="781fa-457">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="781fa-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="781fa-458">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="781fa-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="781fa-460">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="781fa-461">Da die `DbInitializer.Initialize`-Methode nur für die Verwendung mit einer leeren Datenbank konzipiert ist, löschen Sie mithilfe von SSOX alle Zeilen in den Tabellen „Student“ und „Course“.</span><span class="sxs-lookup"><span data-stu-id="781fa-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="781fa-462">(Der kaskadierende Löschvorgang übernimmt die Tabelle „Enrollment“.)</span><span class="sxs-lookup"><span data-stu-id="781fa-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="781fa-464">Wenn Sie SQL Server LocalDB mit Visual Studio Code verwenden, führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="781fa-465">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-465">Run the app.</span></span> <span data-ttu-id="781fa-466">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="781fa-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="781fa-467">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="781fa-468">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="781fa-468">Next steps</span></span>

<span data-ttu-id="781fa-469">In den nächsten beiden Tutorials wird gezeigt, wie verwandte Daten gelesen und aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="781fa-470">[Vorheriges Tutorial](xref:data/ef-rp/migrations)
> [Nächstes Tutorial](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="781fa-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="781fa-471">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="781fa-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="781fa-472">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="781fa-472">In this tutorial:</span></span>

* <span data-ttu-id="781fa-473">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="781fa-474">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="781fa-475">Die Entitätsklassen des vollständigen Datenmodells werden in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="781fa-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="781fa-477">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="781fa-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="781fa-478">Anpassen des Datenmodells mithilfe von Attributen</span><span class="sxs-lookup"><span data-stu-id="781fa-478">Customize the data model with attributes</span></span>

<span data-ttu-id="781fa-479">In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.</span><span class="sxs-lookup"><span data-stu-id="781fa-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="781fa-480">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-480">The DataType attribute</span></span>

<span data-ttu-id="781fa-481">Die Seite für Studenten zeigt derzeit die Uhrzeit des Anmeldedatums an.</span><span class="sxs-lookup"><span data-stu-id="781fa-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="781fa-482">Üblicherweise zeigen Datumsfelder nur das Datum an, nicht die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="781fa-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="781fa-483">Aktualisieren Sie *Models/Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="781fa-484">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="781fa-485">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="781fa-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="781fa-486">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="781fa-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="781fa-487">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="781fa-487">For example:</span></span>

* <span data-ttu-id="781fa-488">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="781fa-489">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="781fa-490">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus, die von HTML5-Browsern genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="781fa-491">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="781fa-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="781fa-492">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="781fa-493">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="781fa-494">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="781fa-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="781fa-495">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="781fa-496">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="781fa-497">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="781fa-498">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="781fa-499">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="781fa-500">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="781fa-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="781fa-501">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="781fa-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="781fa-502">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="781fa-503">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.</span><span class="sxs-lookup"><span data-stu-id="781fa-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="781fa-504">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="781fa-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="781fa-505">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="781fa-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="781fa-506">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-506">Run the app.</span></span> <span data-ttu-id="781fa-507">Navigieren Sie zur Indexseite „Studenten“.</span><span class="sxs-lookup"><span data-stu-id="781fa-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="781fa-508">Die Uhrzeiten werden nicht mehr angezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-508">Times are no longer displayed.</span></span> <span data-ttu-id="781fa-509">Jede Ansicht, die das `Student`-Modell verwendet, zeigt das Datum ohne Uhrzeit an.</span><span class="sxs-lookup"><span data-stu-id="781fa-509">Every view that uses the `Student` model displays the date without time.</span></span>

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="781fa-511">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-511">The StringLength attribute</span></span>

<span data-ttu-id="781fa-512">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="781fa-513">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="781fa-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="781fa-514">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="781fa-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="781fa-515">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="781fa-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="781fa-516">Aktualisieren Sie das `Student`-Modell mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="781fa-517">Der vorangehende Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="781fa-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="781fa-518">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="781fa-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="781fa-519">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) wird verwendet, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="781fa-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="781fa-520">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="781fa-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="781fa-521">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-521">Run the app:</span></span>

* <span data-ttu-id="781fa-522">Navigieren Sie zur Seite für Studenten.</span><span class="sxs-lookup"><span data-stu-id="781fa-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="781fa-523">Klicken Sie auf **Neu erstellen**, und geben Sie einen Namen ein, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="781fa-524">Wenn Sie auf **Erstellen** klicken, zeigt die clientseitige Validierung eine Fehlermeldung an.</span><span class="sxs-lookup"><span data-stu-id="781fa-524">Select **Create**, client-side validation shows an error message.</span></span>

![Indexseite „Studenten“, die Fehler für die Zeichenfolgenlänge anzeigt](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="781fa-526">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="781fa-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="781fa-528">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="781fa-529">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf, da die Migrationen nicht auf der Datenbank ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="781fa-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="781fa-530">Wenn die Migrationen im Verlauf dieses Tutorials ausgeführt werden, ändern sich die Namensfelder in `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="781fa-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="781fa-531">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-531">The Column attribute</span></span>

<span data-ttu-id="781fa-532">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="781fa-533">In diesem Abschnitt wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="781fa-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="781fa-534">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="781fa-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="781fa-535">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="781fa-536">Aktualisieren Sie die Datei *Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="781fa-537">Durch die zuvor vorgenommene Änderung wird `Student.FirstMidName` in der App der `FirstName`-Spalte der `Student`-Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="781fa-538">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="781fa-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="781fa-539">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="781fa-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="781fa-540">Wenn die App ausgeführt wird, bevor Migrationen angewendet werden, wird folgende Ausnahme generiert:</span><span class="sxs-lookup"><span data-stu-id="781fa-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="781fa-541">So aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="781fa-541">To update the DB:</span></span>

* <span data-ttu-id="781fa-542">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="781fa-542">Build the project.</span></span>
* <span data-ttu-id="781fa-543">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="781fa-543">Open a command window in the project folder.</span></span> <span data-ttu-id="781fa-544">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="781fa-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="781fa-547">Der Befehl `migrations add ColumnFirstName` generiert folgende Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="781fa-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="781fa-548">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="781fa-549">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="781fa-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="781fa-550">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="781fa-550">Test the app.</span></span>

<span data-ttu-id="781fa-551">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="781fa-551">Open the Student table in SSOX:</span></span>

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="781fa-553">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="781fa-554">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="781fa-555">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="781fa-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="781fa-556">Im folgenden Abschnitt werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="781fa-557">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="781fa-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="781fa-558">Aktualisieren der Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="781fa-558">Student entity update</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="781fa-560">Aktualisieren Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="781fa-561">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="781fa-561">The Required attribute</span></span>

<span data-ttu-id="781fa-562">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="781fa-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="781fa-563">Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (`DateTime`, `int`, `double` usw.) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="781fa-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="781fa-564">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="781fa-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="781fa-565">Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="781fa-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="781fa-566">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-566">The Display attribute</span></span>

<span data-ttu-id="781fa-567">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="781fa-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="781fa-568">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="781fa-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="781fa-569">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="781fa-569">The FullName calculated property</span></span>

<span data-ttu-id="781fa-570">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="781fa-571">`FullName` verfügt lediglich über einen get-Accessor und kann daher nicht festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="781fa-572">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="781fa-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="781fa-573">Erstellen der Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="781fa-573">Create the Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="781fa-575">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="781fa-576">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="781fa-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="781fa-577">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="781fa-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="781fa-578">Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="781fa-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="781fa-579">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="781fa-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="781fa-580">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="781fa-581">Folgendes gilt, wenn eine Navigationseigenschaft mehrere Entitäten enthält:</span><span class="sxs-lookup"><span data-stu-id="781fa-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="781fa-582">Bei der Eigenschaft muss es sich um einen Listentyp handeln, bei dem Einträge hinzugefügt, gelöscht und aktualisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="781fa-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="781fa-583">Folgendes gilt für die Typen von Navigationseigenschaften:</span><span class="sxs-lookup"><span data-stu-id="781fa-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="781fa-584">Wenn `ICollection<T>` angegeben wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="781fa-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="781fa-585">Die `CourseAssignment`-Entität wird im Abschnitt über m:n-Beziehungen erläutert.</span><span class="sxs-lookup"><span data-stu-id="781fa-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="781fa-586">Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="781fa-587">Die `OfficeAssignment`-Eigenschaft enthält also eine einzige `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="781fa-588">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="781fa-589">Erstellen der Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="781fa-589">Create the OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="781fa-591">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="781fa-592">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-592">The Key attribute</span></span>

<span data-ttu-id="781fa-593">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="781fa-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="781fa-594">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="781fa-595">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="781fa-596">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="781fa-597">Entity Framework Core erkennt `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment`, weil</span><span class="sxs-lookup"><span data-stu-id="781fa-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="781fa-598">`InstructorID` der Namenskonvention „ID“ oder „classnameID“ nicht folgt.</span><span class="sxs-lookup"><span data-stu-id="781fa-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="781fa-599">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="781fa-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="781fa-600">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="781fa-601">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="781fa-601">The Instructor navigation property</span></span>

<span data-ttu-id="781fa-602">Die `OfficeAssignment`-Navigationseigenschaft für die `Instructor`-Entität ist auf NULL festlegbar, weil:</span><span class="sxs-lookup"><span data-stu-id="781fa-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="781fa-603">Referenztypen (z.B. Klassen) auf NULL festlegbar sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="781fa-604">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="781fa-605">Die `OfficeAssignment`-Entität besitzt eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft, weil:</span><span class="sxs-lookup"><span data-stu-id="781fa-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="781fa-606">`InstructorID` nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="781fa-607">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="781fa-608">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="781fa-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="781fa-609">Das `[Required]`-Attribut kann auf die `Instructor`-Navigationseigenschaft angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="781fa-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="781fa-610">Der vorangehende Code legt fest, dass ein zugehöriger Dozent vorhanden sein muss.</span><span class="sxs-lookup"><span data-stu-id="781fa-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="781fa-611">Der vorangehende Code ist nicht erforderlich, da der Fremdschlüssel `InstructorID` (der ebenfalls der Primärschlüssel ist) nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="781fa-612">Ändern der Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="781fa-612">Modify the Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="781fa-614">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="781fa-615">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="781fa-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="781fa-616">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="781fa-617">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="781fa-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="781fa-618">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="781fa-619">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="781fa-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="781fa-620">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="781fa-621">Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="781fa-622">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="781fa-623">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="781fa-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="781fa-624">Die `Department`-Entität ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="781fa-625">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="781fa-626">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="781fa-627">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="781fa-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="781fa-628">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="781fa-629">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="781fa-630">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="781fa-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="781fa-631">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="781fa-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="781fa-632">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="781fa-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="781fa-633">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="781fa-634">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="781fa-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="781fa-635">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="781fa-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-636">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-637">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="781fa-638">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="781fa-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="781fa-639">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="781fa-640">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="781fa-641">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="781fa-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="781fa-642">Erstellen der Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="781fa-642">Create the Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="781fa-644">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="781fa-645">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="781fa-645">The Column attribute</span></span>

<span data-ttu-id="781fa-646">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="781fa-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="781fa-647">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="781fa-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="781fa-648">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="781fa-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="781fa-649">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="781fa-649">Column mapping is generally not required.</span></span> <span data-ttu-id="781fa-650">Entity Framework Core wählt üblicherweise den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="781fa-651">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="781fa-652">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="781fa-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-653">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-654">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="781fa-655">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="781fa-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="781fa-656">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="781fa-656">An administrator is always an instructor.</span></span> <span data-ttu-id="781fa-657">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="781fa-658">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="781fa-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="781fa-659">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="781fa-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="781fa-660">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="781fa-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="781fa-661">Hinweis: Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="781fa-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="781fa-662">Das kaskadierende Delete kann zu Zirkelregeln für kaskadierende Deletes führen.</span><span class="sxs-lookup"><span data-stu-id="781fa-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="781fa-663">Durch Zirkelregeln für kaskadierende Deletes wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="781fa-664">Beispielsweise dann, wenn die `Department.InstructorID`-Eigenschaft als nicht auf NULL festlegbar definiert wurde:</span><span class="sxs-lookup"><span data-stu-id="781fa-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="781fa-665">Entity Framework Core konfiguriert eine Regel für kaskadierende Deletes, um den Fachbereich zu löschen, wenn ein Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="781fa-666">Das Löschen eines Fachbereichs in Folge des Löschens eines Dozenten stellt nicht das beabsichtigte Verhalten dar.</span><span class="sxs-lookup"><span data-stu-id="781fa-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="781fa-667">Die folgende [Fluent-API](#fluent-api-alternative-to-attributes) würde eine Einschränkungsregel anstelle eines kaskadierenden Deletes festlegen.</span><span class="sxs-lookup"><span data-stu-id="781fa-667">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="781fa-668">Durch den vorangehenden Code werden kaskadierende Deletes für die Beziehung zwischen „Department“ und „Instructor“ deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="781fa-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="781fa-669">Aktualisieren der Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="781fa-669">Update the Enrollment entity</span></span>

<span data-ttu-id="781fa-670">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-670">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="781fa-672">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="781fa-673">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="781fa-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="781fa-674">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="781fa-675">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="781fa-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="781fa-676">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="781fa-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="781fa-677">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="781fa-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="781fa-678">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="781fa-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="781fa-679">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="781fa-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="781fa-680">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="781fa-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="781fa-681">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="781fa-682">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="781fa-683">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="781fa-683">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="781fa-685">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="781fa-686">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="781fa-687">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="781fa-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="781fa-688">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="781fa-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="781fa-689">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="781fa-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="781fa-690">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="781fa-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="781fa-691">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="781fa-691">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="781fa-693">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="781fa-694">Beziehung zwischen „Instructor“ und „Course“</span><span class="sxs-lookup"><span data-stu-id="781fa-694">Instructor-to-Courses</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="781fa-696">Folgendes gilt für die m:n-Beziehung zwischen „Instructor“ und „Course“:</span><span class="sxs-lookup"><span data-stu-id="781fa-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="781fa-697">Eine Jointabelle ist erforderlich, die durch eine Entitätenmenge dargestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="781fa-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="781fa-698">Diese muss eine reine Jointabelle (eine Tabelle ohne Nutzlast) sein.</span><span class="sxs-lookup"><span data-stu-id="781fa-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="781fa-699">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="781fa-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="781fa-700">Der Name der Jointabelle für „Instructor“ und „Course“ lautet mithilfe dieses Musters beispielsweise `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="781fa-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="781fa-701">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="781fa-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="781fa-702">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="781fa-702">Data models start out simple and grow.</span></span> <span data-ttu-id="781fa-703">Währenddessen erhalten Joins ohne Nutzlast häufig Nutzlasten.</span><span class="sxs-lookup"><span data-stu-id="781fa-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="781fa-704">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="781fa-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="781fa-705">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="781fa-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="781fa-706">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="781fa-707">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="781fa-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="781fa-708">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="781fa-708">Composite key</span></span>

<span data-ttu-id="781fa-709">Fremdschlüssel sind nicht auf NULL festlegbar.</span><span class="sxs-lookup"><span data-stu-id="781fa-709">FKs are not nullable.</span></span> <span data-ttu-id="781fa-710">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="781fa-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="781fa-711">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="781fa-712">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="781fa-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="781fa-713">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="781fa-714">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="781fa-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="781fa-715">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="781fa-715">The composite key ensures:</span></span>

* <span data-ttu-id="781fa-716">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="781fa-717">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="781fa-718">Mehrere Zeilen für denselben Dozenten und Kurs nicht zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="781fa-719">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="781fa-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="781fa-720">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="781fa-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="781fa-721">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="781fa-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="781fa-722">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="781fa-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="781fa-723">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="781fa-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="781fa-724">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="781fa-724">Update the DB context</span></span>

<span data-ttu-id="781fa-725">Fügen Sie folgenden hervorgehobenen Code zu *Data/SchoolContext.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="781fa-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="781fa-726">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="781fa-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="781fa-727">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="781fa-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="781fa-728">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="781fa-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="781fa-729">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="781fa-730">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="781fa-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="781fa-731">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="781fa-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="781fa-732">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="781fa-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="781fa-733">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="781fa-734">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="781fa-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="781fa-735">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="781fa-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="781fa-736">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="781fa-737">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="781fa-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="781fa-738">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="781fa-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="781fa-739">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="781fa-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="781fa-740">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="781fa-741">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="781fa-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="781fa-742">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="781fa-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="781fa-743">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="781fa-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="781fa-744">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="781fa-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="781fa-745">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="781fa-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="781fa-746">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="781fa-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="781fa-747">Entitätsdiagramm mit angezeigten Beziehungen</span><span class="sxs-lookup"><span data-stu-id="781fa-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="781fa-748">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="781fa-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="781fa-750">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="781fa-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="781fa-751">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="781fa-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="781fa-752">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="781fa-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="781fa-753">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="781fa-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="781fa-754">Ausführen eines Seedings mit Testdaten für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="781fa-755">Aktualisieren Sie den Code in *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="781fa-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="781fa-756">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="781fa-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="781fa-757">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="781fa-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="781fa-758">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="781fa-758">The sample data is used for testing.</span></span> <span data-ttu-id="781fa-759">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="781fa-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="781fa-760">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="781fa-760">Add a migration</span></span>

<span data-ttu-id="781fa-761">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="781fa-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-762">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="781fa-764">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="781fa-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="781fa-765">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="781fa-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="781fa-766">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="781fa-766">Apply the migration</span></span>

<span data-ttu-id="781fa-767">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie zukünftig Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="781fa-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="781fa-768">In diesem Tutorial werden zwei Vorgehensweisen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="781fa-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="781fa-769">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="781fa-770">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="781fa-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="781fa-771">Obwohl diese Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="781fa-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="781fa-772">**Hinweis:** Dies ist ein optionaler Abschnitt des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="781fa-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="781fa-773">Sie können diesen Abschnitt überspringen und die Schritte zum Löschen und Neuerstellen durchführen.</span><span class="sxs-lookup"><span data-stu-id="781fa-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="781fa-774">Wenn Sie stattdessen die Schritte in diesem Abschnitt ausführen möchten, führen Sie nicht die Schritte zum Löschen und Neuerstellen aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="781fa-775">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-775">Drop and re-create the database</span></span>

<span data-ttu-id="781fa-776">Durch den Code in der aktualisierten `DbInitializer`-Klasse werden Startwertdaten für die neuen Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="781fa-777">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="781fa-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="781fa-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="781fa-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="781fa-779">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="781fa-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="781fa-780">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="781fa-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="781fa-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="781fa-782">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="781fa-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="781fa-783">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="781fa-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="781fa-784">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="781fa-784">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="781fa-785">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="781fa-785">Run the app.</span></span> <span data-ttu-id="781fa-786">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="781fa-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="781fa-787">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="781fa-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="781fa-788">Öffnen Sie die Datenbank im SSOX:</span><span class="sxs-lookup"><span data-stu-id="781fa-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="781fa-789">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="781fa-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="781fa-790">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="781fa-790">Expand the **Tables** node.</span></span> <span data-ttu-id="781fa-791">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="781fa-791">The created tables are displayed.</span></span>

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="781fa-793">Überprüfen Sie die Tabelle **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="781fa-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="781fa-794">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="781fa-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="781fa-795">Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="781fa-795">Verify the **CourseAssignment** table contains data.</span></span>

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="781fa-797">Anwenden der Migration auf die vorhandene Datenbank</span><span class="sxs-lookup"><span data-stu-id="781fa-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="781fa-798">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="781fa-798">This section is optional.</span></span> <span data-ttu-id="781fa-799">Diese Schritte funktionieren nur, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="781fa-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="781fa-800">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="781fa-801">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="781fa-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="781fa-802">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="781fa-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="781fa-803">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="781fa-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="781fa-804">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="781fa-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="781fa-805">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="781fa-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="781fa-806">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="781fa-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="781fa-807">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="781fa-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="781fa-808">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="781fa-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="781fa-809">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="781fa-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="781fa-810">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="781fa-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="781fa-811">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="781fa-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="781fa-812">Aktualisieren Sie die `Up`-Methode der `ComplexDataModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="781fa-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="781fa-813">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="781fa-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="781fa-814">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="781fa-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="781fa-815">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="781fa-815">Add the following highlighted code.</span></span> <span data-ttu-id="781fa-816">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="781fa-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="781fa-817">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel` `Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="781fa-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="781fa-818">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="781fa-818">A production app would:</span></span>

* <span data-ttu-id="781fa-819">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="781fa-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="781fa-820">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="781fa-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="781fa-821">Im folgenden Tutorial werden verknüpfte Daten behandelt.</span><span class="sxs-lookup"><span data-stu-id="781fa-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="781fa-822">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="781fa-822">Additional resources</span></span>

* [<span data-ttu-id="781fa-823">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="781fa-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="781fa-824">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="781fa-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="781fa-825">[Zurück](xref:data/ef-rp/migrations)
> [Weiter](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="781fa-825">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
