---
title: Globalisierung und Lokalisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dienste und Middleware für das Lokalisieren von Inhalten in verschiedene Sprachen und Kulturen anbietet.
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: 36235e305037c0bbf20093327e2a0ff21b3de809
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963668"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="399ba-103">Globalisierung und Lokalisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="399ba-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="399ba-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="399ba-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="399ba-105">Wenn Sie eine mehrsprachige Website mit ASP.NET Core erstellen, können Sie mit Ihrer Website ein breiteres Publikum erreichen.</span><span class="sxs-lookup"><span data-stu-id="399ba-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="399ba-106">ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.</span><span class="sxs-lookup"><span data-stu-id="399ba-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="399ba-107">Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="399ba-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="399ba-108">Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="399ba-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="399ba-109">Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.</span><span class="sxs-lookup"><span data-stu-id="399ba-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="399ba-110">Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst.</span><span class="sxs-lookup"><span data-stu-id="399ba-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="399ba-111">Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="399ba-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="399ba-112">Die Lokalisierung von Apps umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="399ba-112">App localization involves the following:</span></span>

1. <span data-ttu-id="399ba-113">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="399ba-114">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="399ba-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="399ba-115">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="399ba-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="399ba-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="399ba-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="399ba-117">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-117">Make the app's content localizable</span></span>

<span data-ttu-id="399ba-118">`IStringLocalizer` und `IStringLocalizer<T>` wurden mit ASP.NET Core eingeführt und zur Förderung der Produktivität bei der Entwicklung von lokalisierten Apps entwickelt.</span><span class="sxs-lookup"><span data-stu-id="399ba-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="399ba-119">`IStringLocalizer` nutzt [ResourceManager](/dotnet/api/system.resources.resourcemanager) und [ResourceReader](/dotnet/api/system.resources.resourcereader), um kulturspezifische Ressourcen zur Laufzeit bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="399ba-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="399ba-120">Die einfache Schnittstelle besitzt einen Indexer und `IEnumerable` für die Rückgabe von lokalisierten Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="399ba-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="399ba-121">`IStringLocalizer` erfordert nicht, dass Sie die Zeichenfolgen der Standardsprache in einer Ressourcendatei speichern.</span><span class="sxs-lookup"><span data-stu-id="399ba-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="399ba-122">Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="399ba-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="399ba-123">Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="399ba-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="399ba-124">Im obigen Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="399ba-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="399ba-125">Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“.</span><span class="sxs-lookup"><span data-stu-id="399ba-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="399ba-126">Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können.</span><span class="sxs-lookup"><span data-stu-id="399ba-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="399ba-127">Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="399ba-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="399ba-128">Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben.</span><span class="sxs-lookup"><span data-stu-id="399ba-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="399ba-129">Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="399ba-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="399ba-130">Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="399ba-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="399ba-131">Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="399ba-132">Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="399ba-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="399ba-133">Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="399ba-134">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="399ba-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="399ba-135">Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:</span><span class="sxs-lookup"><span data-stu-id="399ba-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="399ba-136">Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="399ba-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="399ba-137">Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden.</span><span class="sxs-lookup"><span data-stu-id="399ba-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="399ba-138">In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="399ba-139">Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="399ba-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="399ba-140">Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:</span><span class="sxs-lookup"><span data-stu-id="399ba-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="399ba-141">Lokalisierung der Ansicht</span><span class="sxs-lookup"><span data-stu-id="399ba-141">View localization</span></span>

<span data-ttu-id="399ba-142">Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an.</span><span class="sxs-lookup"><span data-stu-id="399ba-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="399ba-143">Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="399ba-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="399ba-144">Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="399ba-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="399ba-145">Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="399ba-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="399ba-146">Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="399ba-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="399ba-147">`ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="399ba-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="399ba-148">Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="399ba-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="399ba-149">Beachten Sie das folgende Razor-Markup:</span><span class="sxs-lookup"><span data-stu-id="399ba-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="399ba-150">Eine französische Ressourcendatei könnte Folgendes beinhalten:</span><span class="sxs-lookup"><span data-stu-id="399ba-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="399ba-151">Key</span><span class="sxs-lookup"><span data-stu-id="399ba-151">Key</span></span> | <span data-ttu-id="399ba-152">Wert</span><span class="sxs-lookup"><span data-stu-id="399ba-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="399ba-153">Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="399ba-154">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="399ba-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="399ba-155">Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="399ba-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="399ba-156">Lokalisierung von DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="399ba-156">DataAnnotations localization</span></span>

<span data-ttu-id="399ba-157">Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="399ba-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="399ba-158">Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="399ba-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="399ba-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="399ba-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="399ba-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="399ba-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="399ba-161">In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="399ba-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="399ba-162">ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="399ba-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="399ba-163">Verwenden einer Ressourcenzeichenfolge für mehrere Klassen</span><span class="sxs-lookup"><span data-stu-id="399ba-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="399ba-164">Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="399ba-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="399ba-165">Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="399ba-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="399ba-166">DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.</span><span class="sxs-lookup"><span data-stu-id="399ba-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="399ba-167">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="399ba-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="399ba-168">SupportedCultures und SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="399ba-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="399ba-169">ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="399ba-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="399ba-170">Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen.</span><span class="sxs-lookup"><span data-stu-id="399ba-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="399ba-171">`SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen.</span><span class="sxs-lookup"><span data-stu-id="399ba-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="399ba-172">Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="399ba-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="399ba-173">`SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="399ba-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="399ba-174">Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="399ba-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="399ba-175">Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="399ba-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="399ba-176">ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="399ba-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="399ba-177">Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="399ba-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="399ba-178">Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="399ba-178">Resource files</span></span>

<span data-ttu-id="399ba-179">Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code.</span><span class="sxs-lookup"><span data-stu-id="399ba-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="399ba-180">Bei übersetzten Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, handelt es sich um isolierte *RESX*-Ressourcendateien.</span><span class="sxs-lookup"><span data-stu-id="399ba-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="399ba-181">Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält.</span><span class="sxs-lookup"><span data-stu-id="399ba-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="399ba-182">„es“ ist der Sprachcode für Spanisch.</span><span class="sxs-lookup"><span data-stu-id="399ba-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="399ba-183">Erstellen dieser Ressourcendatei in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="399ba-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="399ba-184">Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="399ba-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet.](localization/_static/newi.png)

2. <span data-ttu-id="399ba-187">Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="399ba-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

3. <span data-ttu-id="399ba-189">Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.</span><span class="sxs-lookup"><span data-stu-id="399ba-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

    <span data-ttu-id="399ba-191">Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.</span><span class="sxs-lookup"><span data-stu-id="399ba-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="399ba-193">Benennung von Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="399ba-193">Resource file naming</span></span>

<span data-ttu-id="399ba-194">Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt.</span><span class="sxs-lookup"><span data-stu-id="399ba-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="399ba-195">Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup` `LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="399ba-196">Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="399ba-197">Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen.</span><span class="sxs-lookup"><span data-stu-id="399ba-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="399ba-198">Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="399ba-199">Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="399ba-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="399ba-200">Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden.</span><span class="sxs-lookup"><span data-stu-id="399ba-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="399ba-201">Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="399ba-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="399ba-202">Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden.</span><span class="sxs-lookup"><span data-stu-id="399ba-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="399ba-203">Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="399ba-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="399ba-204">Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.</span><span class="sxs-lookup"><span data-stu-id="399ba-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="399ba-205">Ressourcenname</span><span class="sxs-lookup"><span data-stu-id="399ba-205">Resource name</span></span> | <span data-ttu-id="399ba-206">Punkt- oder Pfadbenennung</span><span class="sxs-lookup"><span data-stu-id="399ba-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="399ba-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="399ba-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="399ba-208">Punkt</span><span class="sxs-lookup"><span data-stu-id="399ba-208">Dot</span></span>  |
| <span data-ttu-id="399ba-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="399ba-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="399ba-210">Pfad</span><span class="sxs-lookup"><span data-stu-id="399ba-210">Path</span></span> |
|    |     |

<span data-ttu-id="399ba-211">Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="399ba-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="399ba-212">Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden.</span><span class="sxs-lookup"><span data-stu-id="399ba-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="399ba-213">Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="399ba-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="399ba-214">Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:</span><span class="sxs-lookup"><span data-stu-id="399ba-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="399ba-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="399ba-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="399ba-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="399ba-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="399ba-217">Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="399ba-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="399ba-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="399ba-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="399ba-219">Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="399ba-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="399ba-220">Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="399ba-221">Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt.</span><span class="sxs-lookup"><span data-stu-id="399ba-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="399ba-222">Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="399ba-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="399ba-223">Die Lokalisierung funktioniert standardmäßig nicht.</span><span class="sxs-lookup"><span data-stu-id="399ba-223">Localization does not work by default.</span></span>
* <span data-ttu-id="399ba-224">Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl.</span><span class="sxs-lookup"><span data-stu-id="399ba-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="399ba-225">`RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="399ba-226">Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):</span><span class="sxs-lookup"><span data-stu-id="399ba-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="399ba-227">Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.</span><span class="sxs-lookup"><span data-stu-id="399ba-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="399ba-228">Kulturfallbackverhalten</span><span class="sxs-lookup"><span data-stu-id="399ba-228">Culture fallback behavior</span></span>

<span data-ttu-id="399ba-229">Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“.</span><span class="sxs-lookup"><span data-stu-id="399ba-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="399ba-230">Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück.</span><span class="sxs-lookup"><span data-stu-id="399ba-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="399ba-231">Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar.</span><span class="sxs-lookup"><span data-stu-id="399ba-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="399ba-232">Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="399ba-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="399ba-233">Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“.</span><span class="sxs-lookup"><span data-stu-id="399ba-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="399ba-234">„es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.</span><span class="sxs-lookup"><span data-stu-id="399ba-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="399ba-235">Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält.</span><span class="sxs-lookup"><span data-stu-id="399ba-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="399ba-236">Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:</span><span class="sxs-lookup"><span data-stu-id="399ba-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="399ba-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="399ba-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="399ba-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="399ba-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="399ba-239">*Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)</span><span class="sxs-lookup"><span data-stu-id="399ba-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="399ba-240">Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="399ba-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="399ba-241">Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="399ba-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="399ba-242">Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.</span><span class="sxs-lookup"><span data-stu-id="399ba-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="399ba-243">Erstellen von Ressourcendateien mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="399ba-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="399ba-244">Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="399ba-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="399ba-245">Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen.</span><span class="sxs-lookup"><span data-stu-id="399ba-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="399ba-246">In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen).</span><span class="sxs-lookup"><span data-stu-id="399ba-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="399ba-247">Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="399ba-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="399ba-248">Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="399ba-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="399ba-249">Hinzufügen von anderen Kulturen</span><span class="sxs-lookup"><span data-stu-id="399ba-249">Add other cultures</span></span>

<span data-ttu-id="399ba-250">Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="399ba-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="399ba-251">Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="399ba-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="399ba-252">Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="399ba-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="399ba-253">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="399ba-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="399ba-254">Konfigurieren der Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="399ba-254">Configure localization</span></span>

<span data-ttu-id="399ba-255">Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="399ba-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="399ba-256">`AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer zu.</span><span class="sxs-lookup"><span data-stu-id="399ba-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="399ba-257">Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="399ba-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="399ba-258">`AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien zu.</span><span class="sxs-lookup"><span data-stu-id="399ba-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="399ba-259">In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="399ba-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="399ba-260">Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="399ba-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="399ba-261">`AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.</span><span class="sxs-lookup"><span data-stu-id="399ba-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="399ba-262">Lokalisierungsmiddleware</span><span class="sxs-lookup"><span data-stu-id="399ba-262">Localization middleware</span></span>

<span data-ttu-id="399ba-263">Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="399ba-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="399ba-264">Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="399ba-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="399ba-265">Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="399ba-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

<span data-ttu-id="399ba-266">`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="399ba-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="399ba-267">Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="399ba-268">Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="399ba-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="399ba-269">Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten.</span><span class="sxs-lookup"><span data-stu-id="399ba-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="399ba-270">Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="399ba-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="399ba-271">Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="399ba-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="399ba-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="399ba-273">Einige Apps verwenden eine Abfragezeichenfolge, um die [Kultur und Benutzeroberflächenkultur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="399ba-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="399ba-274">Bei Apps, die die Ansätze „Cookie“ oder „Accept-Language-Header“ verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich.</span><span class="sxs-lookup"><span data-stu-id="399ba-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="399ba-275">Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert.</span><span class="sxs-lookup"><span data-stu-id="399ba-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="399ba-276">Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="399ba-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="399ba-277">Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:</span><span class="sxs-lookup"><span data-stu-id="399ba-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="399ba-278">Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest.</span><span class="sxs-lookup"><span data-stu-id="399ba-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="399ba-279">Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:</span><span class="sxs-lookup"><span data-stu-id="399ba-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="399ba-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="399ba-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="399ba-281">Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies.</span><span class="sxs-lookup"><span data-stu-id="399ba-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="399ba-282">Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines Cookies.</span><span class="sxs-lookup"><span data-stu-id="399ba-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="399ba-283">`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, der verwendet wird, um zu ermitteln, welche Kulturinformationen vom Benutzer bevorzugt werden.</span><span class="sxs-lookup"><span data-stu-id="399ba-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="399ba-284">Der Standardname für Cookies ist `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="399ba-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="399ba-285">Das Cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="399ba-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="399ba-286">Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="399ba-287">Der Accept-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="399ba-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="399ba-288">Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben.</span><span class="sxs-lookup"><span data-stu-id="399ba-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="399ba-289">Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde.</span><span class="sxs-lookup"><span data-stu-id="399ba-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="399ba-290">Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="399ba-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="399ba-291">In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.</span><span class="sxs-lookup"><span data-stu-id="399ba-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="399ba-292">Festlegen des Accept-Language-HTTP-Headers in Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="399ba-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="399ba-293">Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.</span><span class="sxs-lookup"><span data-stu-id="399ba-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="399ba-294">Klicken Sie auf **Sprachen**.</span><span class="sxs-lookup"><span data-stu-id="399ba-294">Tap **Languages**.</span></span>

    ![Internetoptionen](localization/_static/lang.png)

3. <span data-ttu-id="399ba-296">Klicken Sie auf **Spracheinstellungen festlegen**.</span><span class="sxs-lookup"><span data-stu-id="399ba-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="399ba-297">Klicken Sie auf **Sprache hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="399ba-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="399ba-298">Fügen Sie die Sprache hinzu.</span><span class="sxs-lookup"><span data-stu-id="399ba-298">Add the language.</span></span>

6. <span data-ttu-id="399ba-299">Klicken Sie auf die Sprache und dann auf **Nach oben**.</span><span class="sxs-lookup"><span data-stu-id="399ba-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range=">= aspnetcore-3.0"
### <a name="the-content-language-http-header"></a><span data-ttu-id="399ba-300">Der Content-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="399ba-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="399ba-301">Der [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)-Entitätsheader:</span><span class="sxs-lookup"><span data-stu-id="399ba-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="399ba-302">Wird verwendet, um die für die Zielgruppe vorgesehenen Sprachen zu beschreiben.</span><span class="sxs-lookup"><span data-stu-id="399ba-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="399ba-303">Ermöglicht einem Benutzer, gemäß seiner bevorzugten Sprache zu differenzieren.</span><span class="sxs-lookup"><span data-stu-id="399ba-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="399ba-304">Entitätsheader werden in HTTP-Anforderungen und -Antworten verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="399ba-305">In ASP.NET Core 3.0 kann der `Content-Language`-Header hinzugefügt werden, indem die Eigenschaft `ApplyCurrentCultureToResponseHeaders` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="399ba-305">In ASP.NET Core 3.0 the `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="399ba-306">Hinzufügen des `Content-Language`-Headers:</span><span class="sxs-lookup"><span data-stu-id="399ba-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="399ba-307">Ermöglicht RequestLocalizationMiddleware, den `Content-Language`-Header mit der `CurrentUICulture` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="399ba-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="399ba-308">Macht das explizite Festlegen des `Content-Language`-Antwortheaders überflüssig.</span><span class="sxs-lookup"><span data-stu-id="399ba-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="399ba-309">Verwenden eines benutzerdefinierten Anbieters</span><span class="sxs-lookup"><span data-stu-id="399ba-309">Use a custom provider</span></span>

<span data-ttu-id="399ba-310">Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="399ba-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="399ba-311">In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft.</span><span class="sxs-lookup"><span data-stu-id="399ba-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="399ba-312">Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="399ba-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="399ba-313">Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="399ba-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="399ba-314">Programmgesteuertes Festlegen der Kultur</span><span class="sxs-lookup"><span data-stu-id="399ba-314">Set the culture programmatically</span></span>

<span data-ttu-id="399ba-315">Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`.</span><span class="sxs-lookup"><span data-stu-id="399ba-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="399ba-316">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:</span><span class="sxs-lookup"><span data-stu-id="399ba-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="399ba-317">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="399ba-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="399ba-318">Die Methode `SetLanguage` legt das Kulturcookie fest.</span><span class="sxs-lookup"><span data-stu-id="399ba-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="399ba-319">Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="399ba-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="399ba-320">Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eine Razor-Teilansicht übermittelt.</span><span class="sxs-lookup"><span data-stu-id="399ba-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="399ba-321">Begriffe für die Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="399ba-321">Globalization and localization terms</span></span>

<span data-ttu-id="399ba-322">Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen.</span><span class="sxs-lookup"><span data-stu-id="399ba-322">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="399ba-323">Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen.</span><span class="sxs-lookup"><span data-stu-id="399ba-323">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="399ba-324">Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.</span><span class="sxs-lookup"><span data-stu-id="399ba-324">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="399ba-325">[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-325">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="399ba-326">Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode.</span><span class="sxs-lookup"><span data-stu-id="399ba-326">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="399ba-327">Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien).</span><span class="sxs-lookup"><span data-stu-id="399ba-327">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="399ba-328">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="399ba-328">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="399ba-329">Weitere Informationen finden Sie unter [Language Culture Name (Sprachen- und Kulturbezeichnungen)](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="399ba-329">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="399ba-330">Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt.</span><span class="sxs-lookup"><span data-stu-id="399ba-330">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="399ba-331">Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“.</span><span class="sxs-lookup"><span data-stu-id="399ba-331">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="399ba-332">Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).</span><span class="sxs-lookup"><span data-stu-id="399ba-332">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="399ba-333">Begriffe:</span><span class="sxs-lookup"><span data-stu-id="399ba-333">Terms:</span></span>

* <span data-ttu-id="399ba-334">Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.</span><span class="sxs-lookup"><span data-stu-id="399ba-334">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="399ba-335">Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="399ba-335">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="399ba-336">Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="399ba-336">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="399ba-337">Kultur: Beschreibt eine Sprache und optional auch eine Region.</span><span class="sxs-lookup"><span data-stu-id="399ba-337">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="399ba-338">Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region.</span><span class="sxs-lookup"><span data-stu-id="399ba-338">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="399ba-339">(Zum Beispiel „en“, „es“)</span><span class="sxs-lookup"><span data-stu-id="399ba-339">(for example "en", "es")</span></span>
* <span data-ttu-id="399ba-340">Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt.</span><span class="sxs-lookup"><span data-stu-id="399ba-340">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="399ba-341">(Zum Beispiel „en-US“, „en-GB“, „es-CL“)</span><span class="sxs-lookup"><span data-stu-id="399ba-341">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="399ba-342">Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält.</span><span class="sxs-lookup"><span data-stu-id="399ba-342">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="399ba-343">(„en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)</span><span class="sxs-lookup"><span data-stu-id="399ba-343">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="399ba-344">Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.</span><span class="sxs-lookup"><span data-stu-id="399ba-344">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="399ba-345">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="399ba-345">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="399ba-346">[Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.</span><span class="sxs-lookup"><span data-stu-id="399ba-346">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="399ba-347">Globalisieren und Lokalisieren von .NET-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="399ba-347">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="399ba-348">Ressourcen in RESX-Dateien</span><span class="sxs-lookup"><span data-stu-id="399ba-348">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="399ba-349">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="399ba-349">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="399ba-350">Lokalisierung und Generics</span><span class="sxs-lookup"><span data-stu-id="399ba-350">Localization & Generics</span></span>](https://github.com/hishamco/hishambinateya.com/blob/master/Posts/localization-and-generics.md)
* [<span data-ttu-id="399ba-351">Neues bei der Lokalisierung von ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="399ba-351">What is new in Localization in ASP.NET Core 3.0</span></span>](http://hishambinateya.com/what-is-new-in-localization-in-asp.net-core-3.0)
