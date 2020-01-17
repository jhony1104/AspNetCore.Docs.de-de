---
title: Modellvalidierung im ASP.NET Core MVC
author: rick-anderson
description: Informationen zur Modellvalidierung in ASP.NET Core MVC und Razor Pages
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 042a9933e561de4957f6332bdff3c4f09d2e119b
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355266"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="a61de-103">Modellvalidierung in ASP.NET Core MVC und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a61de-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a61de-104">Von [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a61de-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="a61de-105">In diesem Artikel wird erläutert, wie Benutzereingaben in einer ASP.NET Core MVC- oder Razor Pages-App validiert werden.</span><span class="sxs-lookup"><span data-stu-id="a61de-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="a61de-106">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a61de-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="a61de-107">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="a61de-107">Model state</span></span>

<span data-ttu-id="a61de-108">Der Modellstatus stellt Fehler dar, die aus zwei Subsystemen kommen: Modellbindung und Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="a61de-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="a61de-109">Fehler, die von der [Modellbindung](model-binding.md) herrühren, sind im allgemeinen Datenkonvertierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="a61de-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="a61de-110">Beispielsweise wird ein "x" in ein Integerfeld eingegeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="a61de-111">Die Modellvalidierung erfolgt nach der Modellbindung und meldet Fehler, bei denen die Daten nicht den Geschäftsregeln entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a61de-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="a61de-112">Beispielsweise wird eine 0 in einem Feld eingegeben, das eine Bewertung zwischen 1 und 5 erwartet.</span><span class="sxs-lookup"><span data-stu-id="a61de-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="a61de-113">Sowohl Modellbindung als auch Modellvalidierung finden noch vor der Ausführung einer Controlleraktion oder einer Razor Pages-Handlermethode statt.</span><span class="sxs-lookup"><span data-stu-id="a61de-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="a61de-114">Bei Web-Apps liegen die Überprüfung von `ModelState.IsValid` und entsprechende Maßnahmen im Verantwortungsbereich der App.</span><span class="sxs-lookup"><span data-stu-id="a61de-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="a61de-115">Web-Apps zeigen die Seite normalerweise mit einer Fehlermeldung erneut an:</span><span class="sxs-lookup"><span data-stu-id="a61de-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="a61de-116">Web-API-Controller müssen `ModelState.IsValid` nicht überprüfen, wenn sie das `[ApiController]`-Attribut haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="a61de-117">In diesem Fall wird eine automatische HTTP 400-Antwort zurückgegeben, die Fehlerdetails beinhaltet, wenn der Modellstatus ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="a61de-118">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="a61de-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="a61de-119">Validierung erneut ausführen</span><span class="sxs-lookup"><span data-stu-id="a61de-119">Rerun validation</span></span>

<span data-ttu-id="a61de-120">Die Validierung erfolgt automatisch, aber ggf. möchten Sie sie manuell wiederholen.</span><span class="sxs-lookup"><span data-stu-id="a61de-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="a61de-121">Eine Szenario wäre, dass Sie einen Wert für eine Eigenschaft berechnen und möchten, dass die Validierung erneut ausgeführt wird, nachdem der berechnete Wert für die Eigenschaft festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="a61de-122">Wenn Sie die Validierung erneut ausführen möchten, rufen Sie wie im Folgenden dargestellt die `TryValidateModel`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="a61de-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="a61de-123">Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="a61de-123">Validation attributes</span></span>

<span data-ttu-id="a61de-124">Mit Validierungsattributen können Sie Validierungsregeln für Modelleigenschaften angeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="a61de-125">Im folgenden Beispiel aus der Beispiel-App sehen Sie eine Modellklasse, die mit Validierungsattributen versehen wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="a61de-126">Das `[ClassicMovie]`-Attribut ist ein benutzerdefiniertes Validierungsattribut; die anderen sind integriert.</span><span class="sxs-lookup"><span data-stu-id="a61de-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="a61de-127">Nicht dargestellt ist `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="a61de-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="a61de-128">`[ClassicMovieWithClientValidator]` zeigt einen alternativen Weg zum Implementieren eines benutzerdefinierten Attributs auf.</span><span class="sxs-lookup"><span data-stu-id="a61de-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="a61de-129">Integrierte Attribute</span><span class="sxs-lookup"><span data-stu-id="a61de-129">Built-in attributes</span></span>

<span data-ttu-id="a61de-130">Im Folgenden sind einige der integrierten Validierungsattribute aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="a61de-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="a61de-131">`[CreditCard]`: Überprüft, ob die Eigenschaft über ein Kreditkartenformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="a61de-132">`[Compare]`: Überprüft, ob zwei Eigenschaften in einem Modell miteinander übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a61de-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="a61de-133">`[EmailAddress]`: Überprüft, ob die Eigenschaft über ein E-Mail-Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="a61de-134">`[Phone]`: Überprüft, ob die Eigenschaft über ein Telefonnummernformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="a61de-135">`[Range]`: Überprüft, ob der Eigenschaftenwert im vorgegebenen Bereich liegt.</span><span class="sxs-lookup"><span data-stu-id="a61de-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="a61de-136">`[RegularExpression]`: Überprüft, ob der Eigenschaftswert mit dem angegebenen regulären Ausdruck übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="a61de-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="a61de-137">`[Required]`: Überprüft, ob das Feld leer ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="a61de-138">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [`[Required]`-Attribut](#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="a61de-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="a61de-139">`[StringLength]`: Überprüft, ob ein Zeichenfolgeneigenschaftswert kürzer ist als die angegebene Längenbeschränkung.</span><span class="sxs-lookup"><span data-stu-id="a61de-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="a61de-140">`[Url]`: Überprüft, ob die Eigenschaft über ein URL-Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="a61de-141">`[Remote]`: Überprüft die Eingabe auf dem Client, indem eine Aktionsmethode auf dem Server abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="a61de-142">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [`[Remote]`-Attribut](#remote-attribute).</span><span class="sxs-lookup"><span data-stu-id="a61de-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="a61de-143">Im [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)-Namespace finden Sie eine vollständige Liste der Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="a61de-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="a61de-144">Fehlermeldungen</span><span class="sxs-lookup"><span data-stu-id="a61de-144">Error messages</span></span>

<span data-ttu-id="a61de-145">Mit Validierungsattributen können Sie die Fehlermeldung angeben, die im Fall einer ungültigen Eingabe angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="a61de-146">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a61de-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="a61de-147">Intern rufen die Attribute die Methode `String.Format` mit einem Platzhalter für den Feldnamen und manchmal zusätzliche Platzhalter auf.</span><span class="sxs-lookup"><span data-stu-id="a61de-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="a61de-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a61de-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="a61de-149">Bei Anwendung auf eine `Name`-Eigenschaft wäre die Fehlermeldung, die vom vorangehenden Code erstellt wurde, die Folgende: „Name length must be between 6 and 8“ (Die Länge des Namens muss zwischen 6 und 8 Zeichen betragen).</span><span class="sxs-lookup"><span data-stu-id="a61de-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="a61de-150">Wenn Sie herausfinden möchten, welche Parameter an die Methode `String.Format` für die Fehlermeldung eines bestimmten Attributs übergeben werden, sehen Sie sich den [DataAnnotations-Quellcode](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations) an.</span><span class="sxs-lookup"><span data-stu-id="a61de-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="a61de-151">[Required]-Attribut</span><span class="sxs-lookup"><span data-stu-id="a61de-151">[Required] attribute</span></span>

<span data-ttu-id="a61de-152">Standardmäßig behandelt das Validierungssystem Parameter oder Eigenschaften, die keine NULL-Werte zulassen, wie wenn diese ein `[Required]`-Attribut hätten.</span><span class="sxs-lookup"><span data-stu-id="a61de-152">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="a61de-153">[Werttypen](/dotnet/csharp/language-reference/keywords/value-types) wie `decimal` und `int` lassen keine NULL-Werte zu.</span><span class="sxs-lookup"><span data-stu-id="a61de-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="a61de-154">[Required]-Validierung auf dem Server</span><span class="sxs-lookup"><span data-stu-id="a61de-154">[Required] validation on the server</span></span>

<span data-ttu-id="a61de-155">Auf dem Server wird ein erforderlicher Wert als fehlend betrachtet, wenn für eine Eigenschaft NULL festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-155">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="a61de-156">Ein Feld, das keine NULL-Werte zulässt, ist immer gültig, und die Fehlermeldung des `[Required]`-Attributs wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-156">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="a61de-157">Die Modellbindung für eine Eigenschaft, die keine NULL-Werte zulässt, schlägt jedoch möglicherweise fehl, und führt zu einer Fehlermeldung wie `The value '' is invalid` (Der Wert „“ ist ungültig).</span><span class="sxs-lookup"><span data-stu-id="a61de-157">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="a61de-158">Wenn Sie eine benutzerdefinierte Fehlermeldung für die serverseitige Validierung von Typen, die nicht NULL zulassen, angeben möchten, gibt es die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="a61de-158">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="a61de-159">Lassen Sie für das Feld NULL-Werte zu (z. B. `decimal?` statt `decimal`).</span><span class="sxs-lookup"><span data-stu-id="a61de-159">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="a61de-160">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/)-Werttypen werden behandelt wie Standard-Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="a61de-160">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="a61de-161">Geben Sie die Standardfehlermeldung an, die von der Modellbindung verwendet werden soll, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-161">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="a61de-162">Weitere Informationen zu Fehlern bei der Modellbindung, für die Sie Standardmeldungen festlegen können, finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="a61de-162">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="a61de-163">[Required]-Validierung auf dem Client</span><span class="sxs-lookup"><span data-stu-id="a61de-163">[Required] validation on the client</span></span>

<span data-ttu-id="a61de-164">Typen und Zeichenfolgen, für die keine NULL-Werte festgelegt werden können, werden auf dem Client anders behandelt wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a61de-164">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="a61de-165">Auf dem Client:</span><span class="sxs-lookup"><span data-stu-id="a61de-165">On the client:</span></span>

* <span data-ttu-id="a61de-166">Ein Wert gilt nur als vorhanden, wenn eine Eingabe für ihn erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-166">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="a61de-167">Deshalb werden bei der clientseitigen Validierung Typen, für die keine NULL-Werte festgelegt werden können, gleich behandelt wie Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="a61de-167">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="a61de-168">Leerzeichen in einem Zeichenfolgenfeld werden von der jQuery Validation-[Required](https://jqueryvalidation.org/required-method/)-Methode als gültige Eingabe betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a61de-168">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="a61de-169">Die serverseitige Validierung betrachtet ein erforderliches Zeichenfolgenfeld als ungültig, wenn nur Leerzeichen eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a61de-169">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="a61de-170">Wie bereits gesagt wurde, werden Typen, die keine NULL-Werte zulassen, so behandelt, als hätten sie ein `[Required]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-170">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="a61de-171">Das heißt, die clientseitige Validierung erfolgt, auch wenn Sie das `[Required]`-Attribut nicht anwenden.</span><span class="sxs-lookup"><span data-stu-id="a61de-171">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="a61de-172">Wenn Sie das Attribut jedoch nicht anwenden, erhalten Sie eine Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-172">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="a61de-173">Wenn Sie eine benutzerdefinierte Fehlermeldung angeben möchten, verwenden Sie das Attribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-173">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="a61de-174">[Remote]-Attribut</span><span class="sxs-lookup"><span data-stu-id="a61de-174">[Remote] attribute</span></span>

<span data-ttu-id="a61de-175">Das `[Remote]`-Attribut implementiert die clientseitige Validierung, für die eine Methode auf dem Server aufgerufen werden muss, um zu bestimmen, ob die Feldeingabe gültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-175">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="a61de-176">So muss die App z. B. überprüfen, ob eine Benutzername bereits verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-176">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="a61de-177">So wird die Remotevalidierung implementiert:</span><span class="sxs-lookup"><span data-stu-id="a61de-177">To implement remote validation:</span></span>

1. <span data-ttu-id="a61de-178">Erstellen Sie eine Aktionsmethode für JavaScript, die aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-178">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="a61de-179">Die jQuery Validate-[Remote](https://jqueryvalidation.org/remote-method/)-Methode erwartet eine JSON-Antwort:</span><span class="sxs-lookup"><span data-stu-id="a61de-179">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="a61de-180">`true` bedeutet, die Eingabedaten sind gültig.</span><span class="sxs-lookup"><span data-stu-id="a61de-180">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="a61de-181">`false`, `undefined` oder `null` bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-181">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="a61de-182">Verwenden Sie die Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-182">Display the default error message.</span></span>
   * <span data-ttu-id="a61de-183">Jede andere Zeichenfolge bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-183">Any other string means the input is invalid.</span></span> <span data-ttu-id="a61de-184">Verwenden Sie die Zeichenfolge als benutzerdefinierte Fehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-184">Display the string as a custom error message.</span></span>

   <span data-ttu-id="a61de-185">Hier finden Sie ein Beispiel einer Aktionsmethode, die eine benutzerdefinierte Fehlermeldung zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="a61de-185">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="a61de-186">Versehen Sie in der Modellklasse die Eigenschaft mit einem `[Remote]`-Attribut, das auf die Validierungsaktionsmethode zeigt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-186">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="a61de-187">Das `[Remote]`-Attribut ist im `Microsoft.AspNetCore.Mvc`-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="a61de-187">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="a61de-188">Zusätzliche Felder</span><span class="sxs-lookup"><span data-stu-id="a61de-188">Additional fields</span></span>

<span data-ttu-id="a61de-189">Die `AdditionalFields`-Eigenschaft des `[Remote]`-Attributs ermöglicht Ihnen die Validierung von Kombinationen von Feldern für Daten auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a61de-189">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="a61de-190">Wenn z. B. das `User`-Modell die Eigenschaften `FirstName` und `LastName` hätte, sollten Sie überprüfen, dass kein bereits vorhandener Benutzer diese Namenskombination verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-190">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="a61de-191">Das folgende Beispiel veranschaulicht die Verwendung von `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="a61de-191">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="a61de-192">`AdditionalFields` könnte explizit auf die Zeichenfolgen „FirstName“ und „LastName“ festgelegt werden, aber die Verwendung des [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operators vereinfacht das spätere Refactoring.</span><span class="sxs-lookup"><span data-stu-id="a61de-192">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="a61de-193">Die Aktionsmethode für diese Validierung muss sowohl `firstName`- als auch `lastName`-Argumente akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="a61de-193">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="a61de-194">Wenn der Benutzer einen Vor- oder einen Nachnamen eingibt, führt JavaScript einen Remoteaufruf durch, um zu prüfen, ob dieses Paar Namen bereits verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-194">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="a61de-195">Wenn Sie mindestens zwei weitere Felder überprüfen möchten, geben Sie sie als eine mit Kommas getrennte Liste an.</span><span class="sxs-lookup"><span data-stu-id="a61de-195">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="a61de-196">Wenn Sie z. B. dem Modell eine `MiddleName`-Eigenschaft hinzufügen möchten, legen Sie das `[Remote]`-Attribut wie im folgenden Code veranschaulicht fest:</span><span class="sxs-lookup"><span data-stu-id="a61de-196">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="a61de-197">`AdditionalFields` muss wie alle anderen Attributargumente ein konstanter Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="a61de-197">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="a61de-198">Aus diesem Grund verwenden Sie keine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings) oder rufen <xref:System.String.Join*> auf, um `AdditionalFields` zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-198">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="a61de-199">Alternativen zu integrierten Attributen</span><span class="sxs-lookup"><span data-stu-id="a61de-199">Alternatives to built-in attributes</span></span>

<span data-ttu-id="a61de-200">Wenn Sie eine Validierung benötigen, die nicht von integrierten Attributen bereitgestellt wird, haben Sie die folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a61de-200">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="a61de-201">[Erstellen Sie benutzerdefinierte Attribute.](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="a61de-201">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="a61de-202">[Implementieren Sie IValidatableObject.](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="a61de-202">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="a61de-203">Benutzerdefinierte Attribute</span><span class="sxs-lookup"><span data-stu-id="a61de-203">Custom attributes</span></span>

<span data-ttu-id="a61de-204">Für Szenarios, die die integrierten Validierungsattribute nicht verarbeiten können, können Sie benutzerdefinierte Validierungsattribute erstellen.</span><span class="sxs-lookup"><span data-stu-id="a61de-204">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="a61de-205">Erstellen Sie eine Klasse, die von <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> erbt, und überschreiben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="a61de-205">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="a61de-206">Die `IsValid`-Methode akzeptiert ein Objekt namens *value*. Dies ist die Eingabe, die überprüft werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-206">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="a61de-207">Ein Überladen akzeptiert auch ein `ValidationContext`-Objekt, das zusätzliche Informationen bereitstellt, z. B. die Modellinstanz, die von der Modellbindung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-207">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="a61de-208">Im folgenden Beispiel wird überprüft, ob das Veröffentlichungsdatum eines Films aus dem Genre *Classic* (Klassiker) zeitlich nicht hinter einer angegebenen Jahreszahl liegt.</span><span class="sxs-lookup"><span data-stu-id="a61de-208">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="a61de-209">Das `[ClassicMovie]`-Attribut:</span><span class="sxs-lookup"><span data-stu-id="a61de-209">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="a61de-210">wird nur auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a61de-210">Is only run on the server.</span></span>
* <span data-ttu-id="a61de-211">Für Filmklassiker wird das Veröffentlichungsdatum überprüft:</span><span class="sxs-lookup"><span data-stu-id="a61de-211">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="a61de-212">Die obenstehende `movie`-Variable stellt ein `Movie`-Objekt dar, das die Daten der Formularübermittlung enthält.</span><span class="sxs-lookup"><span data-stu-id="a61de-212">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="a61de-213">Schlägt die Validierung fehl, wird die Klasse `ValidationResult` mit einer Fehlermeldung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-213">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="a61de-214">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="a61de-214">IValidatableObject</span></span>

<span data-ttu-id="a61de-215">Im vorherigen Beispiel wurde nur mit `Movie`-Typen gearbeitet.</span><span class="sxs-lookup"><span data-stu-id="a61de-215">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="a61de-216">Eine andere Option zur Validierung auf Klassenebene ist die Implementierung von `IValidatableObject` in der Modellklasse, wie im folgenden Beispiel dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-216">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="a61de-217">Validierung der Knoten auf oberster Ebene</span><span class="sxs-lookup"><span data-stu-id="a61de-217">Top-level node validation</span></span>

<span data-ttu-id="a61de-218">Knoten der obersten Ebene sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="a61de-218">Top-level nodes include:</span></span>

* <span data-ttu-id="a61de-219">Aktionsparameter</span><span class="sxs-lookup"><span data-stu-id="a61de-219">Action parameters</span></span>
* <span data-ttu-id="a61de-220">Controllereigenschaften</span><span class="sxs-lookup"><span data-stu-id="a61de-220">Controller properties</span></span>
* <span data-ttu-id="a61de-221">Seitenhandlerparameter</span><span class="sxs-lookup"><span data-stu-id="a61de-221">Page handler parameters</span></span>
* <span data-ttu-id="a61de-222">Seitenmodelleigenschaften</span><span class="sxs-lookup"><span data-stu-id="a61de-222">Page model properties</span></span>

<span data-ttu-id="a61de-223">An ein Modell gebundene Knoten auf oberster Ebene und Modelleigenschaften werden überprüft.</span><span class="sxs-lookup"><span data-stu-id="a61de-223">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="a61de-224">Im folgenden Beispiel aus der Beispiel-App verwendet die `VerifyPhone`-Methode die <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>-Klasse, um die `phone`-Aktionsparameter zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="a61de-224">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="a61de-225">Knoten auf oberster Ebene können <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> mit Validierungsattributen verwenden.</span><span class="sxs-lookup"><span data-stu-id="a61de-225">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="a61de-226">Im folgenden Beispiel aus der Beispiel-App gibt die `CheckAge`-Methode an, dass der `age`-Parameter aus der Abfragezeichenfolge gebunden sein muss, wenn das Formular übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-226">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="a61de-227">Auf der Seite für die Altersprüfung (*CheckAge.cshtml*) sind zwei Formulare vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a61de-227">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="a61de-228">Das erste Formular übermittelt einen `Age`-Wert von `99` als Abfragezeichenfolgen-Parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="a61de-228">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="a61de-229">Wenn ein ordnungsgemäß formatierter `age`-Parameter aus der Abfragezeichenfolge übermittelt wird, wird das Formular überprüft.</span><span class="sxs-lookup"><span data-stu-id="a61de-229">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="a61de-230">Das zweite Formular auf der Seite für die Altersprüfung übermittelt den `Age`-Wert im Anforderungstext, worauf die Validierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="a61de-230">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="a61de-231">Die Bindung schlägt fehl, da der `age`-Parameter aus einer Abfragezeichenfolge stammen muss.</span><span class="sxs-lookup"><span data-stu-id="a61de-231">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="a61de-232">Maximale Fehleranzahl</span><span class="sxs-lookup"><span data-stu-id="a61de-232">Maximum errors</span></span>

<span data-ttu-id="a61de-233">Die Validierung stoppt, wenn die maximale Anzahl an Fehlern erreicht wird. Diese liegt standardmäßig bei 200.</span><span class="sxs-lookup"><span data-stu-id="a61de-233">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="a61de-234">Sie können diese Zahl mit dem folgenden Code in `Startup.ConfigureServices` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a61de-234">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="a61de-235">Maximale Rekursion</span><span class="sxs-lookup"><span data-stu-id="a61de-235">Maximum recursion</span></span>

<span data-ttu-id="a61de-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> durchläuft den Objektgraph des Modells, das überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="a61de-237">Bei Modellen, die umfassend oder unendlich rekursiv sind, führt die Validierung möglicherweise zu einem Stapelüberlauf.</span><span class="sxs-lookup"><span data-stu-id="a61de-237">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="a61de-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) stellt eine Möglichkeit dar, die Validierung frühzeitig zu stoppen, wenn die Besucherrekursion eine konfigurierte Tiefe überschreitet.</span><span class="sxs-lookup"><span data-stu-id="a61de-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="a61de-239">Der Standardwert von `MvcOptions.MaxValidationDepth` ist „32“.</span><span class="sxs-lookup"><span data-stu-id="a61de-239">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="a61de-240">Automatischer Kurzschluss</span><span class="sxs-lookup"><span data-stu-id="a61de-240">Automatic short-circuit</span></span>

<span data-ttu-id="a61de-241">Die Validierung wird automatisch kurzgeschlossen (übersprungen), wenn der Modellgraph keine Validierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="a61de-241">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="a61de-242">Objekte, deren Validierung durch die Runtime übersprungen wird, beinhalten Primitivsammlungen (z. B. `byte[]`, `string[]`, `Dictionary<string, string>`) und komplexe Objektgraphen, die keine Validierungssteuerelemente haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-242">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="a61de-243">Validierung deaktivieren</span><span class="sxs-lookup"><span data-stu-id="a61de-243">Disable validation</span></span>

<span data-ttu-id="a61de-244">So deaktivieren Sie die Validierung:</span><span class="sxs-lookup"><span data-stu-id="a61de-244">To disable validation:</span></span>

1. <span data-ttu-id="a61de-245">Erstellen Sie eine Implementierung von `IObjectModelValidator`, die keine Felder als ungültig kennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="a61de-245">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="a61de-246">Fügen Sie `Startup.ConfigureServices` den folgenden Code hinzu, sodass die Standardimplementierung von `IObjectModelValidator` im Abhängigkeitsinjektionscontainer ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-246">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="a61de-247">Möglicherweise werden weiterhin Modellstatusfehler angezeigt, die ihren Ursprung in der Modellbindung haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-247">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="a61de-248">Clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-248">Client-side validation</span></span>

<span data-ttu-id="a61de-249">Die Überprüfung auf Clientseite verhindert die Übermittlung solange, bis das Formular gültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-249">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="a61de-250">Die Schaltfläche „Übermitteln“ führt JavaScript aus, das entweder das Formular übermittelt oder Fehlermeldungen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-250">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="a61de-251">Die clientseitige Validierung umgeht einen unnötigen Roundtrip zum Server, wenn es in einem Formular Eingabefehler gibt.</span><span class="sxs-lookup"><span data-stu-id="a61de-251">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="a61de-252">Die folgenden Skriptverweise in *_Layout.cshtml* und *_ValidationScriptsPartial.cshtml* unterstützen die clientseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="a61de-252">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="a61de-253">Bei dem [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive)-Skript handelt es sich um eine benutzerdefinierte Front-End-Bibliothek von Microsoft, die auf dem beliebten [jQuery Validate](https://jqueryvalidation.org/)-Plugin basiert.</span><span class="sxs-lookup"><span data-stu-id="a61de-253">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="a61de-254">Ohne jQuery Unobtrusive Validation müssten Sie dieselbe Validierungslogik an zwei unterschiedlichen Stellen codieren: einmal in den Attributen der Validierung auf Serverseite für Modelleigenschaften und einmal in den Skripts auf Clientseite.</span><span class="sxs-lookup"><span data-stu-id="a61de-254">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="a61de-255">Stattdessen verwenden die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und die [HTML-Hilfsprogramme](xref:mvc/views/overview) die Validierungsattribute und Typmetadaten aus den Modelleigenschaften, um HTML 5-Attribute des Typs `data-` in den Formularelementen zu rendern, die validiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="a61de-255">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="a61de-256">jQuery Unobtrusive Validation analysiert dann diese `data-`-Attribute und übergibt die Logik an jQuery Validate, wodurch die Logik der Validierung auf Serverseite in den Client „kopiert“ wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-256">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="a61de-257">Sie können Validierungsfehler im Client anzeigen, indem Sie die relevanten Taghilfsprogramme wie folgt verwenden:</span><span class="sxs-lookup"><span data-stu-id="a61de-257">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="a61de-258">Die vorangegangenen Taghilfsprogramme rendern die folgende HTML:</span><span class="sxs-lookup"><span data-stu-id="a61de-258">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="a61de-259">Beachten Sie, dass die `data-`-Attribute in der HTML-Ausgabe mit den Validierungsattributen für die `Movie.ReleaseDate`-Eigenschaft übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a61de-259">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="a61de-260">Das `data-val-required`-Attribut enthält eine Fehlermeldung, die angezeigt wird, wenn der Benutzer das Datumsfeld für die Veröffentlichung nicht ausfüllt.</span><span class="sxs-lookup"><span data-stu-id="a61de-260">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="a61de-261">„jQuery Unobtrusive Validation“ übergibt diesen Wert an die jQuery Validate-Methode [required()](https://jqueryvalidation.org/required-method/), die diese Meldung dann im zugehörigen **\<span>** -Element anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-261">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="a61de-262">Die Datentypvalidierung basiert auf dem .NET-Typ einer Eigenschaft, es sei denn, dieser wird von einem `[DataType]`-Attribut überschrieben.</span><span class="sxs-lookup"><span data-stu-id="a61de-262">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="a61de-263">Browser haben ihre eigenen Standardfehlermeldungen, aber das jQuery Validation Unobtrusive Validation-Paket kann diese Meldungen überschreiben.</span><span class="sxs-lookup"><span data-stu-id="a61de-263">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="a61de-264">Mithilfe von `[DataType]`-Attributen und Subklassen wie `[EmailAddress]` können Sie die Fehlermeldung angeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-264">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="a61de-265">Unaufdringliche Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-265">Unobtrusive validation</span></span>

<span data-ttu-id="a61de-266">Informationen zur unaufdringlichen Validierung finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="a61de-266">For information on unobtrusive validation, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="a61de-267">Hinzufügen der Validierung zu dynamischen Formularen</span><span class="sxs-lookup"><span data-stu-id="a61de-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="a61de-268">jQuery Unobtrusive Validation übergibt die Validierungslogik und -parameter an jQuery Validate, wenn die Seite das erste Mal geladen wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="a61de-269">Deshalb funktioniert die Validierung nicht automatisch bei dynamisch generierten Formularen.</span><span class="sxs-lookup"><span data-stu-id="a61de-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="a61de-270">Wenn Sie die Validierung aktivieren möchten, müssen Sie jQuery Unobtrusive Validation auffordern, das dynamische Formular direkt nach dem Erstellen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="a61de-271">Beispielweise wird im nachfolgenden Code dargestellt, wie Sie die Validierung auf Clientseite für ein Formular einrichten können, das über AJAX hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="a61de-272">Die `$.validator.unobtrusive.parse()`-Methode akzeptiert einen jQuery-Selektor für ihr einziges Argument.</span><span class="sxs-lookup"><span data-stu-id="a61de-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="a61de-273">Diese Methode fordert „jQuery Unobtrusive Validation“ auf, die `data-`-Attribute von Formularen in diesem Selektor zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="a61de-274">Die Werte diese Attribute werden dann an das jQuery Validate-Plugin übergeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="a61de-275">Hinzufügen der Validierung zu dynamischen Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="a61de-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="a61de-276">Die `$.validator.unobtrusive.parse()`-Methode funktioniert für ein gesamtes Formular, nicht für individuelle, dynamisch erstellte Steuerelemente wie `<input>` und `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="a61de-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="a61de-277">Wenn das Formular erneut analysiert werden soll, entfernen Sie die Validierungsdaten, die bei der vorherigen Analyse des Formulars hinzugefügt wurden, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="a61de-278">Benutzerdefinierte clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-278">Custom client-side validation</span></span>

<span data-ttu-id="a61de-279">Die benutzerdefinierte clientseitige Validierung erfolgt über die Generierung von HTML-Attributen des Typs `data-`, die mit einem benutzerdefinierten jQuery Validate-Adapter funktionieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="a61de-280">Der folgende Beispieladaptercode wurde für die `[ClassicMovie]`- und `[ClassicMovieWithClientValidator]`-Attribute geschrieben, die zuvor in diesem Artikel bereits eingeführt wurden:</span><span class="sxs-lookup"><span data-stu-id="a61de-280">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="a61de-281">Informationen zum Schreiben dieser Adapter finden Sie in der [jQuery Validate-Dokumentation](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="a61de-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="a61de-282">Die Verwendung eines Adapters für ein bestimmtes Feld wird von `data-`-Attributen ausgelöst, die Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="a61de-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="a61de-283">Das Feld wird so gekennzeichnet, dass es validiert wird (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="a61de-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="a61de-284">Ein Name für die Validierungsregel und ein Text für die Fehlermeldung werden bestimmt (z. B. `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="a61de-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="a61de-285">Zusätzliche Parameter werden bereitgestellt, die vom Validierungssteuerelement benötigt werden (z. B. `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="a61de-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="a61de-286">Im folgenden Beispiel sehen Sie die `data-`-Attribute des `ClassicMovie`-Attributs der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="a61de-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="a61de-287">Wie bereits gesagt, verwenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und [HTML-Hilfsprogramme](xref:mvc/views/overview) Informationen der Validierungsattribute, um `data-`-Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="a61de-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="a61de-288">Es gibt zwei Möglichkeiten, Code zu schreiben, der zur Erstellung von benutzerdefinierten HTML-Attributen des Typs `data-` führt:</span><span class="sxs-lookup"><span data-stu-id="a61de-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="a61de-289">Erstellen einer Klasse, die von `AttributeAdapterBase<TAttribute>` ableitet, und einer Klasse, die `IValidationAttributeAdapterProvider` implementiert, und Registrieren Ihres Attributs und des dazugehörigen Adapters in der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="a61de-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="a61de-290">Diese Methode folgt dem [Single-Responsibility-Prinzip](https://wikipedia.org/wiki/Single_responsibility_principle), nach dem sich der serverbezogene und der clientbezogene Validierungscode in unterschiedlichen Klassen befinden.</span><span class="sxs-lookup"><span data-stu-id="a61de-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="a61de-291">Der Adapter bietet außerdem den Vorteil, dass andere Dienste in der Abhängigkeitsinjektion bei Bedarf zur Verfügung stehen, da der Adapter in der Abhängigkeitsinjektion registriert ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="a61de-292">Implementieren von `IClientModelValidator` in Ihrer `ValidationAttribute`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="a61de-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="a61de-293">Diese Methode ist geeignet, wenn das Attribut für keinerlei serverseitige Validierung zuständig ist und keine Dienste aus der Abhängigkeitsinjektion benötigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="a61de-294">AttributeAdapter für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="a61de-295">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="a61de-296">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="a61de-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="a61de-297">Erstellen Sie eine Attributadapterklasse für das benutzerdefinierte Validierungsattribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="a61de-298">Leiten Sie die Klasse von [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) ab.</span><span class="sxs-lookup"><span data-stu-id="a61de-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="a61de-299">Erstellen Sie eine `AddValidation`-Methode, die der gerenderten Ausgabe `data-`-Attribute hinzufügt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="a61de-300">Erstellen Sie eine Adapteranbieterklasse, die <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> implementiert.</span><span class="sxs-lookup"><span data-stu-id="a61de-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="a61de-301">Übergeben Sie in der `GetAttributeAdapter`-Methode dem Konstruktor des Adapters das benutzerdefinierte Attribut, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="a61de-302">Registrieren Sie den Adapteranbieter für die Abhängigkeitsinjektion in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a61de-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="a61de-303">IClientModelValidator für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="a61de-304">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovieWithClientValidator`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="a61de-305">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="a61de-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="a61de-306">Implementieren Sie im benutzerdefinierten Validierungsattribut die `IClientModelValidator`-Schnittstelle, und erstellen Sie eine `AddValidation`-Methode.</span><span class="sxs-lookup"><span data-stu-id="a61de-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="a61de-307">Fügen Sie in der `AddValidation`-Methode die `data-`-Attribute für die Validierung hinzu, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="a61de-308">Deaktivieren der clientseitigen Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-308">Disable client-side validation</span></span>

<span data-ttu-id="a61de-309">Im folgenden Code wird die Clientvalidierung in Razor Pages deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="a61de-309">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="a61de-310">Weitere Optionen zum Deaktivieren der clientseitigen Validierung:</span><span class="sxs-lookup"><span data-stu-id="a61de-310">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="a61de-311">Kommentieren Sie den Verweis auf `_ValidationScriptsPartial` in allen *CSHTML*-Dateien aus.</span><span class="sxs-lookup"><span data-stu-id="a61de-311">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="a61de-312">Entfernen Sie den Inhalt der *Pages\Shared\_ValidationScriptsPartial.cshtml*-Datei.</span><span class="sxs-lookup"><span data-stu-id="a61de-312">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="a61de-313">Der vorherige Ansatz verhindert nicht die clientseite Validierung der ASP.NET Core-Identitäts-Razor-Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="a61de-313">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="a61de-314">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="a61de-314">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a61de-315">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a61de-315">Additional resources</span></span>

* [<span data-ttu-id="a61de-316">System.ComponentModel.DataAnnotations-Namespace</span><span class="sxs-lookup"><span data-stu-id="a61de-316">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="a61de-317">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="a61de-317">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a61de-318">In diesem Artikel wird erläutert, wie Benutzereingaben in einer ASP.NET Core MVC- oder Razor Pages-App validiert werden.</span><span class="sxs-lookup"><span data-stu-id="a61de-318">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="a61de-319">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a61de-319">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="a61de-320">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="a61de-320">Model state</span></span>

<span data-ttu-id="a61de-321">Der Modellstatus stellt Fehler dar, die aus zwei Subsystemen kommen: Modellbindung und Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="a61de-321">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="a61de-322">Fehler, die Ihren Ursprung bei der [Modellbindung](model-binding.md) haben, sind normalerweise Fehler bei der Datenkonvertierung, z. B. wenn in ein Feld ein „x“ eingegeben wird, das einen Integer erwartet.</span><span class="sxs-lookup"><span data-stu-id="a61de-322">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="a61de-323">Die Modellvalidierung erfolgt nach der Modellbindung und meldet Fehler, wenn die Daten in Konflikt mit den Geschäftsregeln stehen, z. B. wenn in ein Feld eine 0 eingegeben wird, das eine Bewertung zwischen 1 und 5 erwartet.</span><span class="sxs-lookup"><span data-stu-id="a61de-323">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="a61de-324">Sowohl Modellbindung als auch -validierung finden noch vor der Ausführung einer Controlleraktion oder einer Razor Pages-Handlermethode statt.</span><span class="sxs-lookup"><span data-stu-id="a61de-324">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="a61de-325">Bei Web-Apps liegen die Überprüfung von `ModelState.IsValid` und entsprechende Maßnahmen im Verantwortungsbereich der App.</span><span class="sxs-lookup"><span data-stu-id="a61de-325">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="a61de-326">Web-Apps zeigen die Seite normalerweise mit einer Fehlermeldung erneut an:</span><span class="sxs-lookup"><span data-stu-id="a61de-326">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="a61de-327">Web-API-Controller müssen `ModelState.IsValid` nicht überprüfen, wenn sie das `[ApiController]`-Attribut haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-327">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="a61de-328">In diesem Fall wird eine automatische HTTP 400-Antwort zurückgegeben, die Fehlerdetails beinhaltet, wenn der Modellstatus ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-328">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="a61de-329">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="a61de-329">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="a61de-330">Validierung erneut ausführen</span><span class="sxs-lookup"><span data-stu-id="a61de-330">Rerun validation</span></span>

<span data-ttu-id="a61de-331">Die Validierung erfolgt automatisch, aber ggf. möchten Sie sie manuell wiederholen.</span><span class="sxs-lookup"><span data-stu-id="a61de-331">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="a61de-332">Eine Szenario wäre, dass Sie einen Wert für eine Eigenschaft berechnen und möchten, dass die Validierung erneut ausgeführt wird, nachdem der berechnete Wert für die Eigenschaft festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-332">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="a61de-333">Wenn Sie die Validierung erneut ausführen möchten, rufen Sie wie im Folgenden dargestellt die `TryValidateModel`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="a61de-333">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="a61de-334">Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="a61de-334">Validation attributes</span></span>

<span data-ttu-id="a61de-335">Mit Validierungsattributen können Sie Validierungsregeln für Modelleigenschaften angeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-335">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="a61de-336">Im folgenden Beispiel aus der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) sehen Sie eine Modellklasse, die mit Validierungsattributen versehen wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-336">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="a61de-337">Das `[ClassicMovie]`-Attribut ist ein benutzerdefiniertes Validierungsattribut; die anderen sind integriert.</span><span class="sxs-lookup"><span data-stu-id="a61de-337">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="a61de-338">Nicht gezeigt wird hier das Attribut `[ClassicMovie2]`, das eine Alternative darstellt, ein benutzerdefiniertes Attribut zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-338">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="a61de-339">Integrierte Attribute</span><span class="sxs-lookup"><span data-stu-id="a61de-339">Built-in attributes</span></span>

<span data-ttu-id="a61de-340">Zu den integrierten Validierungsattributen gehören:</span><span class="sxs-lookup"><span data-stu-id="a61de-340">Built-in validation attributes include:</span></span>

* <span data-ttu-id="a61de-341">`[CreditCard]`: Überprüft, ob die Eigenschaft über ein Kreditkartenformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-341">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="a61de-342">`[Compare]`: Überprüft, ob zwei Eigenschaften in einem Modell miteinander übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a61de-342">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="a61de-343">Beispielsweise verwendet die *Register.cshtml.cs*-Datei `[Compare]`, um zu prüfen, ob die beiden eingegebenen Kennwörter übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a61de-343">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="a61de-344">[Gerüst für Identität](xref:security/authentication/scaffold-identity), um den Registrierungscode anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a61de-344">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="a61de-345">`[EmailAddress]`: Überprüft, ob die Eigenschaft über ein E-Mail-Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-345">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="a61de-346">`[Phone]`: Überprüft, ob die Eigenschaft über ein Telefonnummernformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-346">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="a61de-347">`[Range]`: Überprüft, ob der Eigenschaftenwert im vorgegebenen Bereich liegt.</span><span class="sxs-lookup"><span data-stu-id="a61de-347">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="a61de-348">`[RegularExpression]`: Überprüft, ob der Eigenschaftswert mit dem angegebenen regulären Ausdruck übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="a61de-348">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="a61de-349">`[Required]`: Überprüft, ob das Feld leer ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-349">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="a61de-350">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [`[Required]`-Attribut](#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="a61de-350">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="a61de-351">`[StringLength]`: Überprüft, ob ein Zeichenfolgeneigenschaftswert kürzer ist als die angegebene Längenbeschränkung.</span><span class="sxs-lookup"><span data-stu-id="a61de-351">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="a61de-352">`[Url]`: Überprüft, ob die Eigenschaft über ein URL-Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="a61de-352">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="a61de-353">`[Remote]`: Überprüft die Eingabe auf dem Client, indem eine Aktionsmethode auf dem Server abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-353">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="a61de-354">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [`[Remote]`-Attribut](#remote-attribute).</span><span class="sxs-lookup"><span data-stu-id="a61de-354">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="a61de-355">Wenn das `[RegularExpression]`-Attribut mit der clientseitigen Validierung verwendet wird, wird der Regex in JavaScript auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a61de-355">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="a61de-356">Dies bedeutet, dass ein mit [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) übereinstimmendes Verhalten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-356">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="a61de-357">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="a61de-357">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="a61de-358">Im [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)-Namespace finden Sie eine vollständige Liste der Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="a61de-358">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="a61de-359">Fehlermeldungen</span><span class="sxs-lookup"><span data-stu-id="a61de-359">Error messages</span></span>

<span data-ttu-id="a61de-360">Mit Validierungsattributen können Sie die Fehlermeldung angeben, die im Fall einer ungültigen Eingabe angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-360">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="a61de-361">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a61de-361">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="a61de-362">Intern rufen die Attribute die Methode `String.Format` mit einem Platzhalter für den Feldnamen und manchmal zusätzliche Platzhalter auf.</span><span class="sxs-lookup"><span data-stu-id="a61de-362">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="a61de-363">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a61de-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="a61de-364">Bei Anwendung auf eine `Name`-Eigenschaft wäre die Fehlermeldung, die vom vorangehenden Code erstellt wurde, die Folgende: „Name length must be between 6 and 8“ (Die Länge des Namens muss zwischen 6 und 8 Zeichen betragen).</span><span class="sxs-lookup"><span data-stu-id="a61de-364">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="a61de-365">Wenn Sie herausfinden möchten, welche Parameter an die Methode `String.Format` für die Fehlermeldung eines bestimmten Attributs übergeben werden, sehen Sie sich den [DataAnnotations-Quellcode](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations) an.</span><span class="sxs-lookup"><span data-stu-id="a61de-365">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="a61de-366">[Required]-Attribut</span><span class="sxs-lookup"><span data-stu-id="a61de-366">[Required] attribute</span></span>

<span data-ttu-id="a61de-367">Standardmäßig behandelt das Validierungssystem Parameter oder Eigenschaften, die keine NULL-Werte zulassen, wie wenn diese ein `[Required]`-Attribut hätten.</span><span class="sxs-lookup"><span data-stu-id="a61de-367">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="a61de-368">[Werttypen](/dotnet/csharp/language-reference/keywords/value-types) wie `decimal` und `int` lassen keine NULL-Werte zu.</span><span class="sxs-lookup"><span data-stu-id="a61de-368">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="a61de-369">[Required]-Validierung auf dem Server</span><span class="sxs-lookup"><span data-stu-id="a61de-369">[Required] validation on the server</span></span>

<span data-ttu-id="a61de-370">Auf dem Server wird ein erforderlicher Wert als fehlend betrachtet, wenn für eine Eigenschaft NULL festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-370">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="a61de-371">Ein Feld, das keine NULL-Werte zulässt, ist immer gültig, und die Fehlermeldung des [Required]-Attributs wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-371">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="a61de-372">Die Modellbindung für eine Eigenschaft, die keine NULL-Werte zulässt, schlägt jedoch möglicherweise fehl, und führt zu einer Fehlermeldung wie `The value '' is invalid` (Der Wert „“ ist ungültig).</span><span class="sxs-lookup"><span data-stu-id="a61de-372">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="a61de-373">Wenn Sie eine benutzerdefinierte Fehlermeldung für die serverseitige Validierung von Typen, die nicht NULL zulassen, angeben möchten, gibt es die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="a61de-373">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="a61de-374">Lassen Sie für das Feld NULL-Werte zu (z. B. `decimal?` statt `decimal`).</span><span class="sxs-lookup"><span data-stu-id="a61de-374">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="a61de-375">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/)-Werttypen werden behandelt wie Standard-Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="a61de-375">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="a61de-376">Geben Sie die Standardfehlermeldung an, die von der Modellbindung verwendet werden soll, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-376">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="a61de-377">Weitere Informationen zu Fehlern bei der Modellbindung, für die Sie Standardmeldungen festlegen können, finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="a61de-377">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="a61de-378">[Required]-Validierung auf dem Client</span><span class="sxs-lookup"><span data-stu-id="a61de-378">[Required] validation on the client</span></span>

<span data-ttu-id="a61de-379">Typen und Zeichenfolgen, für die keine NULL-Werte festgelegt werden können, werden auf dem Client anders behandelt wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a61de-379">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="a61de-380">Auf dem Client:</span><span class="sxs-lookup"><span data-stu-id="a61de-380">On the client:</span></span>

* <span data-ttu-id="a61de-381">Ein Wert gilt nur als vorhanden, wenn eine Eingabe für ihn erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-381">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="a61de-382">Deshalb werden bei der clientseitigen Validierung Typen, für die keine NULL-Werte festgelegt werden können, gleich behandelt wie Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="a61de-382">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="a61de-383">Leerzeichen in einem Zeichenfolgenfeld werden von der jQuery Validation-[Required](https://jqueryvalidation.org/required-method/)-Methode als gültige Eingabe betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a61de-383">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="a61de-384">Die serverseitige Validierung betrachtet ein erforderliches Zeichenfolgenfeld als ungültig, wenn nur Leerzeichen eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a61de-384">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="a61de-385">Wie bereits gesagt wurde, werden Typen, die keine NULL-Werte zulassen, so behandelt, als hätten sie ein `[Required]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-385">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="a61de-386">Das heißt, die clientseitige Validierung erfolgt, auch wenn Sie das `[Required]`-Attribut nicht anwenden.</span><span class="sxs-lookup"><span data-stu-id="a61de-386">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="a61de-387">Wenn Sie das Attribut jedoch nicht anwenden, erhalten Sie eine Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-387">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="a61de-388">Wenn Sie eine benutzerdefinierte Fehlermeldung angeben möchten, verwenden Sie das Attribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-388">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="a61de-389">[Remote]-Attribut</span><span class="sxs-lookup"><span data-stu-id="a61de-389">[Remote] attribute</span></span>

<span data-ttu-id="a61de-390">Das `[Remote]`-Attribut implementiert die clientseitige Validierung, für die eine Methode auf dem Server aufgerufen werden muss, um zu bestimmen, ob die Feldeingabe gültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-390">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="a61de-391">So muss die App z. B. überprüfen, ob eine Benutzername bereits verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-391">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="a61de-392">So wird die Remotevalidierung implementiert:</span><span class="sxs-lookup"><span data-stu-id="a61de-392">To implement remote validation:</span></span>

1. <span data-ttu-id="a61de-393">Erstellen Sie eine Aktionsmethode für JavaScript, die aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-393">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="a61de-394">Die jQuery Validate-[Remote](https://jqueryvalidation.org/remote-method/)-Methode erwartet eine JSON-Antwort:</span><span class="sxs-lookup"><span data-stu-id="a61de-394">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="a61de-395">`"true"` bedeutet, die Eingabedaten sind gültig.</span><span class="sxs-lookup"><span data-stu-id="a61de-395">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="a61de-396">`"false"`, `undefined` oder `null` bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-396">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="a61de-397">Verwenden Sie die Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-397">Display the default error message.</span></span>
   * <span data-ttu-id="a61de-398">Jede andere Zeichenfolge bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-398">Any other string means the input is invalid.</span></span> <span data-ttu-id="a61de-399">Verwenden Sie die Zeichenfolge als benutzerdefinierte Fehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="a61de-399">Display the string as a custom error message.</span></span>

   <span data-ttu-id="a61de-400">Hier finden Sie ein Beispiel einer Aktionsmethode, die eine benutzerdefinierte Fehlermeldung zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="a61de-400">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="a61de-401">Versehen Sie in der Modellklasse die Eigenschaft mit einem `[Remote]`-Attribut, das auf die Validierungsaktionsmethode zeigt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-401">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="a61de-402">Das `[Remote]`-Attribut ist im `Microsoft.AspNetCore.Mvc`-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="a61de-402">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="a61de-403">Wenn Sie nicht das Metapaket `Microsoft.AspNetCore.App` oder `Microsoft.AspNetCore.All` verwenden, installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="a61de-403">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="a61de-404">Zusätzliche Felder</span><span class="sxs-lookup"><span data-stu-id="a61de-404">Additional fields</span></span>

<span data-ttu-id="a61de-405">Die `AdditionalFields`-Eigenschaft des `[Remote]`-Attributs ermöglicht Ihnen die Validierung von Kombinationen von Feldern für Daten auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a61de-405">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="a61de-406">Wenn z. B. das `User`-Modell die Eigenschaften `FirstName` und `LastName` hätte, sollten Sie überprüfen, dass kein bereits vorhandener Benutzer diese Namenskombination verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-406">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="a61de-407">Das folgende Beispiel veranschaulicht die Verwendung von `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="a61de-407">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="a61de-408">`AdditionalFields` könnte explizit auf die Zeichenfolgen `"FirstName"` und `"LastName"` festgelegt werden, aber die Verwendung des [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operators vereinfacht das spätere Refactoring.</span><span class="sxs-lookup"><span data-stu-id="a61de-408">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="a61de-409">Die Aktionsmethode für diese Validierung muss sowohl Vor- als auch Nachnamensargumente akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="a61de-409">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="a61de-410">Wenn der Benutzer einen Vor- oder einen Nachnamen eingibt, führt JavaScript einen Remoteaufruf durch, um zu prüfen, ob dieses Paar Namen bereits verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-410">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="a61de-411">Wenn Sie mindestens zwei weitere Felder überprüfen möchten, geben Sie sie als eine mit Kommas getrennte Liste an.</span><span class="sxs-lookup"><span data-stu-id="a61de-411">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="a61de-412">Wenn Sie z. B. dem Modell eine `MiddleName`-Eigenschaft hinzufügen möchten, legen Sie das `[Remote]`-Attribut wie im folgenden Code veranschaulicht fest:</span><span class="sxs-lookup"><span data-stu-id="a61de-412">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="a61de-413">`AdditionalFields` muss wie alle anderen Attributargumente ein konstanter Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="a61de-413">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="a61de-414">Aus diesem Grund verwenden Sie keine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings) oder rufen <xref:System.String.Join*> auf, um `AdditionalFields` zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-414">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="a61de-415">Alternativen zu integrierten Attributen</span><span class="sxs-lookup"><span data-stu-id="a61de-415">Alternatives to built-in attributes</span></span>

<span data-ttu-id="a61de-416">Wenn Sie eine Validierung benötigen, die nicht von integrierten Attributen bereitgestellt wird, haben Sie die folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a61de-416">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="a61de-417">[Erstellen Sie benutzerdefinierte Attribute.](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="a61de-417">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="a61de-418">[Implementieren Sie IValidatableObject.](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="a61de-418">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="a61de-419">Benutzerdefinierte Attribute</span><span class="sxs-lookup"><span data-stu-id="a61de-419">Custom attributes</span></span>

<span data-ttu-id="a61de-420">Für Szenarios, die die integrierten Validierungsattribute nicht verarbeiten können, können Sie benutzerdefinierte Validierungsattribute erstellen.</span><span class="sxs-lookup"><span data-stu-id="a61de-420">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="a61de-421">Erstellen Sie eine Klasse, die von <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> erbt, und überschreiben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="a61de-421">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="a61de-422">Die `IsValid`-Methode akzeptiert ein Objekt namens *value*. Dies ist die Eingabe, die überprüft werden soll.</span><span class="sxs-lookup"><span data-stu-id="a61de-422">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="a61de-423">Ein Überladen akzeptiert auch ein `ValidationContext`-Objekt, das zusätzliche Informationen bereitstellt, z. B. die Modellinstanz, die von der Modellbindung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-423">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="a61de-424">Im folgenden Beispiel wird überprüft, ob das Veröffentlichungsdatum eines Films aus dem Genre *Classic* (Klassiker) zeitlich nicht hinter einer angegebenen Jahreszahl liegt.</span><span class="sxs-lookup"><span data-stu-id="a61de-424">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="a61de-425">Das `[ClassicMovie2]`-Attribut prüft zuerst das Genre und fährt nur fort, wenn als Genre *Classic* bestimmt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a61de-425">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="a61de-426">Für Filme, die als Klassiker erkannt wurden, prüft es das Veröffentlichungsdatum, um dafür zu sorgen, dass der Film nicht älter ist als eine Jahresgrenze, die an den Attributkonstruktor übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-426">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="a61de-427">Die obenstehende `movie`-Variable stellt ein `Movie`-Objekt dar, das die Daten der Formularübermittlung enthält.</span><span class="sxs-lookup"><span data-stu-id="a61de-427">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="a61de-428">Die `IsValid`-Methode prüft das Datum und das Genre.</span><span class="sxs-lookup"><span data-stu-id="a61de-428">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="a61de-429">Ist die Validierung erfolgreich, gibt `IsValid` einen `ValidationResult.Success`-Code zurück.</span><span class="sxs-lookup"><span data-stu-id="a61de-429">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="a61de-430">Schlägt die Validierung fehl, wird die Klasse `ValidationResult` mit einer Fehlermeldung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-430">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="a61de-431">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="a61de-431">IValidatableObject</span></span>

<span data-ttu-id="a61de-432">Im vorherigen Beispiel wurde nur mit `Movie`-Typen gearbeitet.</span><span class="sxs-lookup"><span data-stu-id="a61de-432">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="a61de-433">Eine andere Option zur Validierung auf Klassenebene ist die Implementierung von `IValidatableObject` in der Modellklasse, wie im folgenden Beispiel dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-433">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="a61de-434">Validierung der Knoten auf oberster Ebene</span><span class="sxs-lookup"><span data-stu-id="a61de-434">Top-level node validation</span></span>

<span data-ttu-id="a61de-435">Knoten der obersten Ebene sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="a61de-435">Top-level nodes include:</span></span>

* <span data-ttu-id="a61de-436">Aktionsparameter</span><span class="sxs-lookup"><span data-stu-id="a61de-436">Action parameters</span></span>
* <span data-ttu-id="a61de-437">Controllereigenschaften</span><span class="sxs-lookup"><span data-stu-id="a61de-437">Controller properties</span></span>
* <span data-ttu-id="a61de-438">Seitenhandlerparameter</span><span class="sxs-lookup"><span data-stu-id="a61de-438">Page handler parameters</span></span>
* <span data-ttu-id="a61de-439">Seitenmodelleigenschaften</span><span class="sxs-lookup"><span data-stu-id="a61de-439">Page model properties</span></span>

<span data-ttu-id="a61de-440">An ein Modell gebundene Knoten auf oberster Ebene und Modelleigenschaften werden überprüft.</span><span class="sxs-lookup"><span data-stu-id="a61de-440">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="a61de-441">Im folgenden Beispiel aus der Beispiel-App verwendet die `VerifyPhone`-Methode die <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>-Klasse, um die `phone`-Aktionsparameter zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="a61de-441">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="a61de-442">Knoten auf oberster Ebene können <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> mit Validierungsattributen verwenden.</span><span class="sxs-lookup"><span data-stu-id="a61de-442">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="a61de-443">Im folgenden Beispiel aus der Beispiel-App gibt die `CheckAge`-Methode an, dass der `age`-Parameter aus der Abfragezeichenfolge gebunden sein muss, wenn das Formular übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-443">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="a61de-444">Auf der Seite für die Altersprüfung (*CheckAge.cshtml*) sind zwei Formulare vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a61de-444">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="a61de-445">Das erste Formular übermittelt einen `Age`-Wert von `99` als Abfragezeichenfolge: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="a61de-445">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="a61de-446">Wenn ein ordnungsgemäß formatierter `age`-Parameter aus der Abfragezeichenfolge übermittelt wird, wird das Formular überprüft.</span><span class="sxs-lookup"><span data-stu-id="a61de-446">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="a61de-447">Das zweite Formular auf der Seite für die Altersprüfung übermittelt den `Age`-Wert im Anforderungstext, worauf die Validierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="a61de-447">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="a61de-448">Die Bindung schlägt fehl, da der `age`-Parameter aus einer Abfragezeichenfolge stammen muss.</span><span class="sxs-lookup"><span data-stu-id="a61de-448">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="a61de-449">Bei Ausführung mit der Kompatibilitätsversion `CompatibilityVersion.Version_2_1` oder höher, ist die Knotenvalidierung der obersten Eben standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a61de-449">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="a61de-450">Andernfalls ist die Knotenvalidierung der obersten Ebene deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a61de-450">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="a61de-451">Die Standardoption kann überschrieben werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> in (`Startup.ConfigureServices`) festgelegt wird. Sehen Sie sich dazu das folgende Beispiel an:</span><span class="sxs-lookup"><span data-stu-id="a61de-451">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="a61de-452">Maximale Fehleranzahl</span><span class="sxs-lookup"><span data-stu-id="a61de-452">Maximum errors</span></span>

<span data-ttu-id="a61de-453">Die Validierung stoppt, wenn die maximale Anzahl an Fehlern erreicht wird. Diese liegt standardmäßig bei 200.</span><span class="sxs-lookup"><span data-stu-id="a61de-453">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="a61de-454">Sie können diese Zahl mit dem folgenden Code in `Startup.ConfigureServices` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a61de-454">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="a61de-455">Maximale Rekursion</span><span class="sxs-lookup"><span data-stu-id="a61de-455">Maximum recursion</span></span>

<span data-ttu-id="a61de-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> durchläuft den Objektgraph des Modells, das überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="a61de-457">Bei Modellen, die sehr umfassend oder unendlich rekursiv sind, führt die Validierung möglicherweise zu einem Stapelüberlauf.</span><span class="sxs-lookup"><span data-stu-id="a61de-457">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="a61de-458">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) stellt eine Möglichkeit dar, die Validierung frühzeitig zu stoppen, wenn die Besucherrekursion eine konfigurierte Tiefe überschreitet.</span><span class="sxs-lookup"><span data-stu-id="a61de-458">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="a61de-459">Der Standardwert von `MvcOptions.MaxValidationDepth` ist bei Ausführung mit `CompatibilityVersion.Version_2_2` oder höher „32“.</span><span class="sxs-lookup"><span data-stu-id="a61de-459">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="a61de-460">Bei früheren Versionen ist der Wert NULL, d.h. es gibt keine Tiefeneinschränkung.</span><span class="sxs-lookup"><span data-stu-id="a61de-460">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="a61de-461">Automatischer Kurzschluss</span><span class="sxs-lookup"><span data-stu-id="a61de-461">Automatic short-circuit</span></span>

<span data-ttu-id="a61de-462">Die Validierung wird automatisch kurzgeschlossen (übersprungen), wenn der Modellgraph keine Validierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="a61de-462">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="a61de-463">Objekte, deren Validierung durch die Runtime übersprungen wird, beinhalten Primitivsammlungen (z. B. `byte[]`, `string[]`, `Dictionary<string, string>`) und komplexe Objektgraphen, die keine Validierungssteuerelemente haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-463">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="a61de-464">Validierung deaktivieren</span><span class="sxs-lookup"><span data-stu-id="a61de-464">Disable validation</span></span>

<span data-ttu-id="a61de-465">So deaktivieren Sie die Validierung:</span><span class="sxs-lookup"><span data-stu-id="a61de-465">To disable validation:</span></span>

1. <span data-ttu-id="a61de-466">Erstellen Sie eine Implementierung von `IObjectModelValidator`, die keine Felder als ungültig kennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="a61de-466">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="a61de-467">Fügen Sie `Startup.ConfigureServices` den folgenden Code hinzu, sodass die Standardimplementierung von `IObjectModelValidator` im Abhängigkeitsinjektionscontainer ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-467">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="a61de-468">Möglicherweise werden weiterhin Modellstatusfehler angezeigt, die ihren Ursprung in der Modellbindung haben.</span><span class="sxs-lookup"><span data-stu-id="a61de-468">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="a61de-469">Clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-469">Client-side validation</span></span>

<span data-ttu-id="a61de-470">Die Überprüfung auf Clientseite verhindert die Übermittlung solange, bis das Formular gültig ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-470">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="a61de-471">Die Schaltfläche „Übermitteln“ führt JavaScript aus, das entweder das Formular übermittelt oder Fehlermeldungen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-471">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="a61de-472">Die clientseitige Validierung umgeht einen unnötigen Roundtrip zum Server, wenn es in einem Formular Eingabefehler gibt.</span><span class="sxs-lookup"><span data-stu-id="a61de-472">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="a61de-473">Die folgenden Skriptverweise in *_Layout.cshtml* und *_ValidationScriptsPartial.cshtml* unterstützen die clientseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="a61de-473">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="a61de-474">Bei dem [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive)-Skript handelt es sich um eine benutzerdefinierte Front-End-Bibliothek von Microsoft, die auf dem beliebten [jQuery Validate](https://jqueryvalidation.org/)-Plugin basiert.</span><span class="sxs-lookup"><span data-stu-id="a61de-474">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="a61de-475">Ohne jQuery Unobtrusive Validation müssten Sie dieselbe Validierungslogik an zwei unterschiedlichen Stellen codieren: einmal in den Attributen der Validierung auf Serverseite für Modelleigenschaften und einmal in den Skripts auf Clientseite.</span><span class="sxs-lookup"><span data-stu-id="a61de-475">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="a61de-476">Stattdessen verwenden die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und die [HTML-Hilfsprogramme](xref:mvc/views/overview) die Validierungsattribute und Typmetadaten aus den Modelleigenschaften, um HTML 5-Attribute des Typs `data-` in den Formularelementen zu rendern, die validiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="a61de-476">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="a61de-477">jQuery Unobtrusive Validation analysiert dann diese `data-`-Attribute und übergibt die Logik an jQuery Validate, wodurch die Logik der Validierung auf Serverseite in den Client „kopiert“ wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-477">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="a61de-478">Sie können Validierungsfehler im Client anzeigen, indem Sie die relevanten Taghilfsprogramme wie folgt verwenden:</span><span class="sxs-lookup"><span data-stu-id="a61de-478">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="a61de-479">Die vorangegangenen Taghilfsprogramme rendern die folgende HTML.</span><span class="sxs-lookup"><span data-stu-id="a61de-479">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="a61de-480">Beachten Sie, dass die `data-`-Attribute in der HTML-Ausgabe mit den Validierungsattributen für die `ReleaseDate`-Eigenschaft übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a61de-480">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="a61de-481">Das `data-val-required`-Attribut enthält eine Fehlermeldung, die angezeigt wird, wenn der Benutzer das Datumsfeld für die Veröffentlichung nicht ausfüllt.</span><span class="sxs-lookup"><span data-stu-id="a61de-481">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="a61de-482">„jQuery Unobtrusive Validation“ übergibt diesen Wert an die jQuery Validate-Methode [required()](https://jqueryvalidation.org/required-method/), die diese Meldung dann im zugehörigen **\<span>** -Element anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-482">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="a61de-483">Die Datentypvalidierung basiert auf dem .NET-Typ einer Eigenschaft, es sei denn, dieser wird von einem `[DataType]`-Attribut überschrieben.</span><span class="sxs-lookup"><span data-stu-id="a61de-483">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="a61de-484">Browser haben ihre eigenen Standardfehlermeldungen, aber das jQuery Validation Unobtrusive Validation-Paket kann diese Meldungen überschreiben.</span><span class="sxs-lookup"><span data-stu-id="a61de-484">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="a61de-485">Mithilfe von `[DataType]`-Attributen und Subklassen wie `[EmailAddress]` können Sie die Fehlermeldung angeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-485">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="a61de-486">Hinzufügen der Validierung zu dynamischen Formularen</span><span class="sxs-lookup"><span data-stu-id="a61de-486">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="a61de-487">jQuery Unobtrusive Validation übergibt die Validierungslogik und -parameter an jQuery Validate, wenn die Seite das erste Mal geladen wird.</span><span class="sxs-lookup"><span data-stu-id="a61de-487">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="a61de-488">Deshalb funktioniert die Validierung nicht automatisch bei dynamisch generierten Formularen.</span><span class="sxs-lookup"><span data-stu-id="a61de-488">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="a61de-489">Wenn Sie die Validierung aktivieren möchten, müssen Sie jQuery Unobtrusive Validation auffordern, das dynamische Formular direkt nach dem Erstellen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-489">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="a61de-490">Beispielweise wird im nachfolgenden Code dargestellt, wie Sie die Validierung auf Clientseite für ein Formular einrichten können, das über AJAX hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="a61de-490">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="a61de-491">Die `$.validator.unobtrusive.parse()`-Methode akzeptiert einen jQuery-Selektor für ihr einziges Argument.</span><span class="sxs-lookup"><span data-stu-id="a61de-491">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="a61de-492">Diese Methode fordert „jQuery Unobtrusive Validation“ auf, die `data-`-Attribute von Formularen in diesem Selektor zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-492">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="a61de-493">Die Werte diese Attribute werden dann an das jQuery Validate-Plugin übergeben.</span><span class="sxs-lookup"><span data-stu-id="a61de-493">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="a61de-494">Hinzufügen der Validierung zu dynamischen Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="a61de-494">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="a61de-495">Die `$.validator.unobtrusive.parse()`-Methode funktioniert für ein gesamtes Formular, nicht für individuelle, dynamisch erstellte Steuerelemente wie `<input>` und `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="a61de-495">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="a61de-496">Wenn das Formular erneut analysiert werden soll, entfernen Sie die Validierungsdaten, die bei der vorherigen Analyse des Formulars hinzugefügt wurden, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-496">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="a61de-497">Benutzerdefinierte clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-497">Custom client-side validation</span></span>

<span data-ttu-id="a61de-498">Die benutzerdefinierte clientseitige Validierung erfolgt über die Generierung von HTML-Attributen des Typs `data-`, die mit einem benutzerdefinierten jQuery Validate-Adapter funktionieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-498">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="a61de-499">Der folgende Beispieladaptercode wurde für die `ClassicMovie`- und `ClassicMovie2`-Attribute geschrieben, die zuvor in diesem Artikel bereits eingeführt wurden:</span><span class="sxs-lookup"><span data-stu-id="a61de-499">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="a61de-500">Informationen zum Schreiben dieser Adapter finden Sie in der [jQuery Validate-Dokumentation](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="a61de-500">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="a61de-501">Die Verwendung eines Adapters für ein bestimmtes Feld wird von `data-`-Attributen ausgelöst, die Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="a61de-501">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="a61de-502">Das Feld wird so gekennzeichnet, dass es validiert wird (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="a61de-502">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="a61de-503">Ein Name für die Validierungsregel und ein Text für die Fehlermeldung werden bestimmt (z. B. `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="a61de-503">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="a61de-504">Zusätzliche Parameter werden bereitgestellt, die vom Validierungssteuerelement benötigt werden (z. B. `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="a61de-504">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="a61de-505">Im folgenden Beispiel sehen Sie die `data-`-Attribute des `ClassicMovie`-Attributs der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="a61de-505">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="a61de-506">Wie bereits gesagt, verwenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und [HTML-Hilfsprogramme](xref:mvc/views/overview) Informationen der Validierungsattribute, um `data-`-Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="a61de-506">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="a61de-507">Es gibt zwei Möglichkeiten, Code zu schreiben, der zur Erstellung von benutzerdefinierten HTML-Attributen des Typs `data-` führt:</span><span class="sxs-lookup"><span data-stu-id="a61de-507">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="a61de-508">Erstellen einer Klasse, die von `AttributeAdapterBase<TAttribute>` ableitet, und einer Klasse, die `IValidationAttributeAdapterProvider` implementiert, und Registrieren Ihres Attributs und des dazugehörigen Adapters in der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="a61de-508">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="a61de-509">Diese Methode folgt dem [Single-Responsibility-Prinzip](https://wikipedia.org/wiki/Single_responsibility_principle), nach dem sich der serverbezogene und der clientbezogene Validierungscode in unterschiedlichen Klassen befinden.</span><span class="sxs-lookup"><span data-stu-id="a61de-509">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="a61de-510">Der Adapter bietet außerdem den Vorteil, dass andere Dienste in der Abhängigkeitsinjektion bei Bedarf zur Verfügung stehen, da der Adapter in der Abhängigkeitsinjektion registriert ist.</span><span class="sxs-lookup"><span data-stu-id="a61de-510">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="a61de-511">Implementieren von `IClientModelValidator` in Ihrer `ValidationAttribute`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="a61de-511">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="a61de-512">Diese Methode ist geeignet, wenn das Attribut für keinerlei serverseitige Validierung zuständig ist und keine Dienste aus der Abhängigkeitsinjektion benötigt.</span><span class="sxs-lookup"><span data-stu-id="a61de-512">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="a61de-513">AttributeAdapter für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-513">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="a61de-514">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-514">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="a61de-515">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="a61de-515">To add client validation by using this method:</span></span>

1. <span data-ttu-id="a61de-516">Erstellen Sie eine Attributadapterklasse für das benutzerdefinierte Validierungsattribut.</span><span class="sxs-lookup"><span data-stu-id="a61de-516">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="a61de-517">Leiten Sie die Klasse von [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) ab.</span><span class="sxs-lookup"><span data-stu-id="a61de-517">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="a61de-518">Erstellen Sie eine `AddValidation`-Methode, die der gerenderten Ausgabe `data-`-Attribute hinzufügt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-518">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="a61de-519">Erstellen Sie eine Adapteranbieterklasse, die <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> implementiert.</span><span class="sxs-lookup"><span data-stu-id="a61de-519">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="a61de-520">Übergeben Sie in der `GetAttributeAdapter`-Methode dem Konstruktor des Adapters das benutzerdefinierte Attribut, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-520">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="a61de-521">Registrieren Sie den Adapteranbieter für die Abhängigkeitsinjektion in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a61de-521">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="a61de-522">IClientModelValidator für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-522">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="a61de-523">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie2`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a61de-523">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="a61de-524">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="a61de-524">To add client validation by using this method:</span></span>

* <span data-ttu-id="a61de-525">Implementieren Sie im benutzerdefinierten Validierungsattribut die `IClientModelValidator`-Schnittstelle, und erstellen Sie eine `AddValidation`-Methode.</span><span class="sxs-lookup"><span data-stu-id="a61de-525">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="a61de-526">Fügen Sie in der `AddValidation`-Methode die `data-`-Attribute für die Validierung hinzu, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a61de-526">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="a61de-527">Deaktivieren der clientseitigen Validierung</span><span class="sxs-lookup"><span data-stu-id="a61de-527">Disable client-side validation</span></span>

<span data-ttu-id="a61de-528">Im folgenden Code wird die Clientvalidierung in MVC-Ansichten deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="a61de-528">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="a61de-529">Und in Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="a61de-529">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="a61de-530">Eine andere Möglichkeit, um die Clientvalidierung zu deaktivieren, ist es, den Verweis auf `_ValidationScriptsPartial` in Ihrer *.cshtml*-Datei auszukommentieren.</span><span class="sxs-lookup"><span data-stu-id="a61de-530">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a61de-531">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a61de-531">Additional resources</span></span>

* [<span data-ttu-id="a61de-532">System.ComponentModel.DataAnnotations-Namespace</span><span class="sxs-lookup"><span data-stu-id="a61de-532">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="a61de-533">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="a61de-533">Model Binding</span></span>](model-binding.md)

::: moniker-end
