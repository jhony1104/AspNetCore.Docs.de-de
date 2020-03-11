---
title: Hinzufügen der Validierung zu einer Razor-Seite in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie einer Razor-Seite in ASP.NET Core Validierung hinzugefügt wird.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650341"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="eb506-103">Hinzufügen der Validierung zu einer Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb506-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="eb506-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb506-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eb506-105">In diesem Abschnitt wird dem Modell `Movie` Validierungslogik hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="eb506-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="eb506-106">Die Validierungsregeln werden erzwungen, wenn ein Benutzer einen Film erstellt oder bearbeitet.</span><span class="sxs-lookup"><span data-stu-id="eb506-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="eb506-107">Validierung</span><span class="sxs-lookup"><span data-stu-id="eb506-107">Validation</span></span>

<span data-ttu-id="eb506-108">Ein wesentlicher Grundsatz der Softwareentwicklung heißt [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (**D**on't **R**epeat **Y**ourself, dt. Wiederholen Sie sich nicht).</span><span class="sxs-lookup"><span data-stu-id="eb506-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="eb506-109">Razor Pages sind für Entwicklungsaufgaben gedacht, bei denen Funktionalität einmal angegeben und in der gesamten App übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="eb506-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="eb506-110">DRY kann Sie bei Folgendem unterstützen:</span><span class="sxs-lookup"><span data-stu-id="eb506-110">DRY can help:</span></span>

* <span data-ttu-id="eb506-111">Sie können die Codemenge in einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="eb506-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="eb506-112">Der Code wird weniger fehleranfällig und lässt sich leichter testen und verwalten.</span><span class="sxs-lookup"><span data-stu-id="eb506-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="eb506-113">Die von Razor Pages und Entity Framework gebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips.</span><span class="sxs-lookup"><span data-stu-id="eb506-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="eb506-114">Validierungsregeln werden an zentraler Stelle (in der Modellklasse) deklarativ angegeben und überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="eb506-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="eb506-115">Hinzufügen von Validierungsregeln zum Modell „Movie“</span><span class="sxs-lookup"><span data-stu-id="eb506-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="eb506-116">Der Namespace „DataAnnotations“ bietet eine Gruppe integrierter Validierungsattribute, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb506-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="eb506-117">„DataAnnotations“ enthält auch Formatierungsattribute wie `DataType`, die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eb506-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="eb506-118">Aktualisieren Sie die `Movie`-Klasse, um die integrierten Validierungsattribute `Required`, `StringLength`, `RegularExpression` und `Range` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="eb506-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="eb506-119">Die Validierungsattribute geben das Verhalten an, das Sie in den Modelleigenschaften erzwingen möchten, auf die sie angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="eb506-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="eb506-120">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="eb506-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="eb506-121">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="eb506-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="eb506-122">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="eb506-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="eb506-123">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="eb506-123">Must only use letters.</span></span>
  * <span data-ttu-id="eb506-124">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="eb506-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="eb506-125">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="eb506-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="eb506-126">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="eb506-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="eb506-127">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="eb506-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="eb506-128">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="eb506-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="eb506-129">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="eb506-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="eb506-130">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="eb506-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="eb506-131">Mit dem Attribut `StringLength` können Sie die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegen.</span><span class="sxs-lookup"><span data-stu-id="eb506-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="eb506-132">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="eb506-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="eb506-133">Indem Validierungsregeln von ASP.NET Core automatisch erzwungen werden, wird Ihre App stabiler.</span><span class="sxs-lookup"><span data-stu-id="eb506-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="eb506-134">Darüber hinaus wird sichergestellt, dass Sie die Validierung nicht vergessen und nicht versehentlich falsche Daten in die Datenbank übernehmen.</span><span class="sxs-lookup"><span data-stu-id="eb506-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="eb506-135">Benutzeroberflächenoption für Validierungsfehler in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="eb506-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="eb506-136">Führen Sie die App aus, und navigieren Sie zu „Pages/Movies“.</span><span class="sxs-lookup"><span data-stu-id="eb506-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="eb506-137">Klicken Sie auf den Link **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="eb506-137">Select the **Create New** link.</span></span> <span data-ttu-id="eb506-138">Füllen Sie das Formular mit einigen ungültigen Werten aus.</span><span class="sxs-lookup"><span data-stu-id="eb506-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="eb506-139">Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb506-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="eb506-141">Wie Sie sehen, hat das Formular in allen Feldern mit einem ungültigen Wert automatisch eine Validierungsfehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb506-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="eb506-142">Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).</span><span class="sxs-lookup"><span data-stu-id="eb506-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="eb506-143">Ein entscheidender Vorteil ist, dass **keine** Codeänderungen auf den Seiten „Erstellen“ oder „Bearbeiten“ erforderlich waren.</span><span class="sxs-lookup"><span data-stu-id="eb506-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="eb506-144">Nach Anwenden von „DataAnnotations“ auf das Modell wurde die Benutzeroberflächenvalidierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="eb506-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="eb506-145">Die in diesem Tutorial erstellten Razor Pages haben die Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`).</span><span class="sxs-lookup"><span data-stu-id="eb506-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="eb506-146">Testen Sie die Validierung mithilfe der Seite „Bearbeiten“. Es erfolgt dieselbe Validierung.</span><span class="sxs-lookup"><span data-stu-id="eb506-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="eb506-147">Die Formulardaten werden erst an den Server zurückgesendet, wenn auf Clientseite keine Validierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="eb506-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="eb506-148">Überprüfen Sie mithilfe von mindestens einem der folgenden Ansätze, ob keine Formulardaten bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="eb506-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="eb506-149">Setzen Sie einen Haltepunkt in der `OnPostAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="eb506-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="eb506-150">Senden Sie das Formular (wählen Sie **Erstellen** oder **Speichern**).</span><span class="sxs-lookup"><span data-stu-id="eb506-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="eb506-151">Der Haltepunkt wird niemals erreicht.</span><span class="sxs-lookup"><span data-stu-id="eb506-151">The break point is never hit.</span></span>
* <span data-ttu-id="eb506-152">Verwenden Sie das [Tool Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="eb506-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="eb506-153">Verwenden Sie die Browserentwicklungstools zum Überwachen des Netzwerkdatenverkehrs.</span><span class="sxs-lookup"><span data-stu-id="eb506-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="eb506-154">Serverseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="eb506-154">Server-side validation</span></span>

<span data-ttu-id="eb506-155">Wenn JavaScript im Browser deaktiviert ist, erfolgt beim Senden des Formulars mit Fehlern eine Bereitstellung auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="eb506-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="eb506-156">Testen Sie optional die serverseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="eb506-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="eb506-157">Deaktivieren Sie JavaScript im Browser.</span><span class="sxs-lookup"><span data-stu-id="eb506-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="eb506-158">Sie können JavaScript mit den Entwicklertools des Browsers deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="eb506-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="eb506-159">Wenn Sie JavaScript im Browser nicht deaktivieren können, probieren Sie einen anderen Browser.</span><span class="sxs-lookup"><span data-stu-id="eb506-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="eb506-160">Setzen Sie auf der Seite „Erstellen“ oder „Bearbeiten“ in der `OnPostAsync`-Methode einen Haltepunkt.</span><span class="sxs-lookup"><span data-stu-id="eb506-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="eb506-161">Senden Sie ein Formular mit ungültigen Daten.</span><span class="sxs-lookup"><span data-stu-id="eb506-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="eb506-162">Überprüfen Sie, ob der Modellstatus ungültig ist:</span><span class="sxs-lookup"><span data-stu-id="eb506-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="eb506-163">Alternativ können Sie [die clientseitige Validierung auf dem Server deaktivieren](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="eb506-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="eb506-164">Der folgende Code zeigt einen Teil der Seite *Create.cshtml*, deren Gerüst Sie zuvor im Tutorial erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="eb506-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="eb506-165">Sie wird von den Seiten „Erstellen“ und „Bearbeiten“ zum Anzeigen des anfänglichen Formulars und zum erneuten Anzeigen des Formulars bei einem Fehler verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb506-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="eb506-166">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="eb506-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="eb506-167">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="eb506-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="eb506-168">Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eb506-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="eb506-169">Die Seiten „Erstellen“ und „Bearbeiten“ weisen keine Validierungsregeln auf.</span><span class="sxs-lookup"><span data-stu-id="eb506-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="eb506-170">Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="eb506-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="eb506-171">Diese Validierungsregeln gelten automatisch für Razor Pages, die das Modell `Movie` bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="eb506-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="eb506-172">Wenn Validierungslogik geändert werden muss, erfolgt dies nur im Modell.</span><span class="sxs-lookup"><span data-stu-id="eb506-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="eb506-173">Die Validierung erfolgt in der gesamten Anwendung einheitlich (Validierungslogik ist zentral definiert).</span><span class="sxs-lookup"><span data-stu-id="eb506-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="eb506-174">Die zentrale Validierung unterstützt sauberen Code und erleichtert dessen Verwaltung und Aktualisierung.</span><span class="sxs-lookup"><span data-stu-id="eb506-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="eb506-175">Verwenden von „DataType“-Attributen</span><span class="sxs-lookup"><span data-stu-id="eb506-175">Using DataType Attributes</span></span>

<span data-ttu-id="eb506-176">Untersuchen Sie die Klasse `Movie`.</span><span class="sxs-lookup"><span data-stu-id="eb506-176">Examine the `Movie` class.</span></span> <span data-ttu-id="eb506-177">Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit.</span><span class="sxs-lookup"><span data-stu-id="eb506-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="eb506-178">Das `DataType`-Attribut wird nur auf die Eigenschaften `ReleaseDate` und `Price` angewendet.</span><span class="sxs-lookup"><span data-stu-id="eb506-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="eb506-179">Die `DataType`-Attribute geben der Anzeige-Engine nur Hinweise zum Formatieren der Daten (und liefern Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail).</span><span class="sxs-lookup"><span data-stu-id="eb506-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="eb506-180">Verwenden Sie das `RegularExpression`-Attribut, um das Format der Daten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="eb506-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="eb506-181">Das `DataType`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="eb506-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="eb506-182">`DataType`-Attribute sind keine Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="eb506-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="eb506-183">In der Beispielanwendung wird nur das Datum ohne Uhrzeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb506-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="eb506-184">Die `DataType`-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“ usw.</span><span class="sxs-lookup"><span data-stu-id="eb506-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="eb506-185">Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="eb506-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="eb506-186">Ein Link des Typs `mailto:` kann beispielsweise für `DataType.EmailAddress` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="eb506-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="eb506-187">In Browsern mit HTML5-Unterstützung kann für `DataType.Date` eine Datumsauswahl bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="eb506-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="eb506-188">Die `DataType`-Attribute geben `data-`-Attribute (ausgesprochen „Datadash“) von HTML5 aus, die HTML5-Browser nutzen.</span><span class="sxs-lookup"><span data-stu-id="eb506-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="eb506-189">Die `DataType`-Attribute bieten **keine** Validierung.</span><span class="sxs-lookup"><span data-stu-id="eb506-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="eb506-190">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="eb506-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="eb506-191">Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb506-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="eb506-192">Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="eb506-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="eb506-193">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="eb506-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="eb506-194">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="eb506-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="eb506-195">Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung angewendet werden soll, wenn der Wert zur Bearbeitung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="eb506-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="eb506-196">Dieses Verhalten ist für einige Felder ggf. nicht wünschenswert.</span><span class="sxs-lookup"><span data-stu-id="eb506-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="eb506-197">Beispielsweise sollte bei Währungswerten das Währungssymbol auf der Benutzeroberfläche für die Bearbeitung nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="eb506-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="eb506-198">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden, doch meist empfiehlt es sich, das `DataType`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb506-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="eb506-199">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Es bietet die folgenden Vorteile, die „DisplayFormat“ nicht bietet:</span><span class="sxs-lookup"><span data-stu-id="eb506-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="eb506-200">Der Browser kann HTML5-Features aktivieren (z.B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.).</span><span class="sxs-lookup"><span data-stu-id="eb506-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="eb506-201">Standardmäßig rendert der Browser Daten mit dem ordnungsgemäßen auf Ihrem Gebietsschema basierenden Format.</span><span class="sxs-lookup"><span data-stu-id="eb506-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="eb506-202">Das `DataType`-Attribut kann dem ASP.NET Core-Framework ermöglichen, die richtige Feldvorlage zum Rendern der Daten auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="eb506-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="eb506-203">`DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage.</span><span class="sxs-lookup"><span data-stu-id="eb506-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="eb506-204">Hinweis: Die jQuery-Validierung funktioniert nicht mit den Attributen `Range` und `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="eb506-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="eb506-205">Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:</span><span class="sxs-lookup"><span data-stu-id="eb506-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="eb506-206">Es wird allgemein nicht empfohlen, feste Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `Range`-Attributs und von `DateTime` abgeraten wird.</span><span class="sxs-lookup"><span data-stu-id="eb506-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="eb506-207">Der folgende Code zeigt die Kombination von Attributen in einer Zeile:</span><span class="sxs-lookup"><span data-stu-id="eb506-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="eb506-208">[Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) zeigt erweiterte EF Core-Vorgänge mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eb506-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="eb506-209">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="eb506-209">Apply migrations</span></span>

<span data-ttu-id="eb506-210">Durch die Anwendung von DataAnnotations auf die Klasse wird das Schema geändert.</span><span class="sxs-lookup"><span data-stu-id="eb506-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="eb506-211">Beispielsweise ergeben sich durch die Anwendung von DataAnnotations auf das `Title`-Feld folgende Änderungen:</span><span class="sxs-lookup"><span data-stu-id="eb506-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="eb506-212">Die Zeichen werden auf 60 begrenzt.</span><span class="sxs-lookup"><span data-stu-id="eb506-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="eb506-213">Ein `null`-Wert ist unzulässig.</span><span class="sxs-lookup"><span data-stu-id="eb506-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb506-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb506-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb506-215">Die `Movie`-Tabelle verfügt zurzeit über das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="eb506-215">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="eb506-216">Die vorstehenden Schemaänderungen bewirken nicht, dass EF eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="eb506-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="eb506-217">Erstellen Sie jedoch eine Migration, damit das Schema mit dem Modell konsistent ist.</span><span class="sxs-lookup"><span data-stu-id="eb506-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="eb506-218">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="eb506-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="eb506-219">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="eb506-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="eb506-220">`Update-Database` führt die `Up`-Methoden der `New_DataAnnotations`-Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="eb506-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="eb506-221">Untersuchen Sie die `Up`-Methode.</span><span class="sxs-lookup"><span data-stu-id="eb506-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="eb506-222">Die aktualisierte `Movie`-Tabelle hat das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="eb506-222">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="eb506-223">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="eb506-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="eb506-224">Für SQLite sind keine Migrationen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="eb506-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="eb506-225">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="eb506-225">Publish to Azure</span></span>

<span data-ttu-id="eb506-226">Informationen zum Bereitstellen in Azure finden Sie unter [Tutorial: Erstellen einer ASP.NET Core-App in Azure mit SQL-Datenbank](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="eb506-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="eb506-227">Vielen Dank für Ihr Interesse an dieser Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eb506-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="eb506-228">[Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.</span><span class="sxs-lookup"><span data-stu-id="eb506-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb506-229">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb506-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="eb506-230">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="eb506-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="eb506-231">Zurück: Hinzufügen eines neuen Felds</span><span class="sxs-lookup"><span data-stu-id="eb506-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
