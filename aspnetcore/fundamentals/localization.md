---
<span data-ttu-id="8b040-101">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-102">'Blazor'</span></span>
- <span data-ttu-id="8b040-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-103">'Identity'</span></span>
- <span data-ttu-id="8b040-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-105">'Razor'</span></span>
- <span data-ttu-id="8b040-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="8b040-107">Globalisierung und Lokalisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b040-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8b040-108">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="8b040-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="8b040-109">Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="8b040-110">ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.</span><span class="sxs-lookup"><span data-stu-id="8b040-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="8b040-111">Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="8b040-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="8b040-112">Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8b040-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="8b040-113">Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.</span><span class="sxs-lookup"><span data-stu-id="8b040-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="8b040-114">Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst.</span><span class="sxs-lookup"><span data-stu-id="8b040-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="8b040-115">Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="8b040-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="8b040-116">Die Lokalisierung von Apps umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="8b040-116">App localization involves the following:</span></span>

1. <span data-ttu-id="8b040-117">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="8b040-118">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="8b040-119">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="8b040-120">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b040-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="8b040-121">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-121">Make the app's content localizable</span></span>

<span data-ttu-id="8b040-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="8b040-123">Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="8b040-124">Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="8b040-125">Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="8b040-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="8b040-126">Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“.</span><span class="sxs-lookup"><span data-stu-id="8b040-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="8b040-127">Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können.</span><span class="sxs-lookup"><span data-stu-id="8b040-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="8b040-128">Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="8b040-129">Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="8b040-130">Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="8b040-131">Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="8b040-132">Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="8b040-133">Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="8b040-134">Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="8b040-135">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-136">Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:</span><span class="sxs-lookup"><span data-stu-id="8b040-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="8b040-137">Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="8b040-138">Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="8b040-139">In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="8b040-140">Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="8b040-141">Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:</span><span class="sxs-lookup"><span data-stu-id="8b040-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="8b040-142">Lokalisierung der Ansicht</span><span class="sxs-lookup"><span data-stu-id="8b040-142">View localization</span></span>

<span data-ttu-id="8b040-143">Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an.</span><span class="sxs-lookup"><span data-stu-id="8b040-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="8b040-144">Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="8b040-145">Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="8b040-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="8b040-146">Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="8b040-147">Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="8b040-148">`ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="8b040-149">Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8b040-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="8b040-150">Sehen Sie sich das folgende Razor-Markup an:</span><span class="sxs-lookup"><span data-stu-id="8b040-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="8b040-151">Eine französische Ressourcendatei könnte Folgendes beinhalten:</span><span class="sxs-lookup"><span data-stu-id="8b040-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="8b040-152">Key</span><span class="sxs-lookup"><span data-stu-id="8b040-152">Key</span></span> | <span data-ttu-id="8b040-153">Wert</span><span class="sxs-lookup"><span data-stu-id="8b040-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="8b040-154">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-155">'Blazor'</span></span>
- <span data-ttu-id="8b040-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-156">'Identity'</span></span>
- <span data-ttu-id="8b040-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-158">'Razor'</span></span>
- <span data-ttu-id="8b040-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-159">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="8b040-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="8b040-161">Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="8b040-162">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-163">Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="8b040-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="8b040-164">Lokalisierung von DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="8b040-164">DataAnnotations localization</span></span>

<span data-ttu-id="8b040-165">Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="8b040-166">Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="8b040-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="8b040-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="8b040-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="8b040-169">In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="8b040-170">ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="8b040-171">Verwenden einer Ressourcenzeichenfolge für mehrere Klassen</span><span class="sxs-lookup"><span data-stu-id="8b040-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="8b040-172">Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8b040-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="8b040-173">Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="8b040-174">DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.</span><span class="sxs-lookup"><span data-stu-id="8b040-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="8b040-175">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="8b040-176">SupportedCultures und SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="8b040-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="8b040-177">ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="8b040-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="8b040-178">Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen.</span><span class="sxs-lookup"><span data-stu-id="8b040-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="8b040-179">`SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="8b040-180">Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="8b040-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="8b040-181">`SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="8b040-182">Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="8b040-183">Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="8b040-184">ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="8b040-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="8b040-185">Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="8b040-186">Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-186">Resource files</span></span>

<span data-ttu-id="8b040-187">Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="8b040-188">Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert.</span><span class="sxs-lookup"><span data-stu-id="8b040-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="8b040-189">Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="8b040-190">„es“ ist der Sprachcode für Spanisch.</span><span class="sxs-lookup"><span data-stu-id="8b040-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="8b040-191">Erstellen dieser Ressourcendatei in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b040-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="8b040-192">Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="8b040-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet.](localization/_static/newi.png)

2. <span data-ttu-id="8b040-195">Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="8b040-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

3. <span data-ttu-id="8b040-197">Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.</span><span class="sxs-lookup"><span data-stu-id="8b040-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

    <span data-ttu-id="8b040-199">Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8b040-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="8b040-201">Benennung von Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-201">Resource file naming</span></span>

<span data-ttu-id="8b040-202">Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt.</span><span class="sxs-lookup"><span data-stu-id="8b040-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="8b040-203">Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="8b040-204">Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-205">Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen.</span><span class="sxs-lookup"><span data-stu-id="8b040-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="8b040-206">Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="8b040-207">Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-208">Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="8b040-209">Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-210">Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden.</span><span class="sxs-lookup"><span data-stu-id="8b040-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="8b040-211">Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-212">Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.</span><span class="sxs-lookup"><span data-stu-id="8b040-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="8b040-213">Ressourcenname</span><span class="sxs-lookup"><span data-stu-id="8b040-213">Resource name</span></span> | <span data-ttu-id="8b040-214">Punkt- oder Pfadbenennung</span><span class="sxs-lookup"><span data-stu-id="8b040-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="8b040-215">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-216">'Blazor'</span></span>
- <span data-ttu-id="8b040-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-217">'Identity'</span></span>
- <span data-ttu-id="8b040-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-219">'Razor'</span></span>
- <span data-ttu-id="8b040-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-221">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-222">'Blazor'</span></span>
- <span data-ttu-id="8b040-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-223">'Identity'</span></span>
- <span data-ttu-id="8b040-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-225">'Razor'</span></span>
- <span data-ttu-id="8b040-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-227">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-228">'Blazor'</span></span>
- <span data-ttu-id="8b040-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-229">'Identity'</span></span>
- <span data-ttu-id="8b040-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-231">'Razor'</span></span>
- <span data-ttu-id="8b040-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-233">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-234">'Blazor'</span></span>
- <span data-ttu-id="8b040-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-235">'Identity'</span></span>
- <span data-ttu-id="8b040-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-237">'Razor'</span></span>
- <span data-ttu-id="8b040-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-238">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-240">'Blazor'</span></span>
- <span data-ttu-id="8b040-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-241">'Identity'</span></span>
- <span data-ttu-id="8b040-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-243">'Razor'</span></span>
- <span data-ttu-id="8b040-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-245">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-246">'Blazor'</span></span>
- <span data-ttu-id="8b040-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-247">'Identity'</span></span>
- <span data-ttu-id="8b040-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-249">'Razor'</span></span>
- <span data-ttu-id="8b040-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-251">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-252">'Blazor'</span></span>
- <span data-ttu-id="8b040-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-253">'Identity'</span></span>
- <span data-ttu-id="8b040-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-255">'Razor'</span></span>
- <span data-ttu-id="8b040-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-257">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-258">'Blazor'</span></span>
- <span data-ttu-id="8b040-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-259">'Identity'</span></span>
- <span data-ttu-id="8b040-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-261">'Razor'</span></span>
- <span data-ttu-id="8b040-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-262">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="8b040-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="8b040-264">Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="8b040-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="8b040-265">Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-265">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="8b040-266">Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-266">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="8b040-267">Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:</span><span class="sxs-lookup"><span data-stu-id="8b040-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="8b040-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="8b040-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="8b040-270">Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="8b040-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="8b040-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="8b040-272">Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="8b040-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="8b040-273">Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="8b040-274">Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt.</span><span class="sxs-lookup"><span data-stu-id="8b040-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="8b040-275">Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8b040-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="8b040-276">Die Lokalisierung funktioniert standardmäßig nicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-276">Localization does not work by default.</span></span>
* <span data-ttu-id="8b040-277">Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl.</span><span class="sxs-lookup"><span data-stu-id="8b040-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="8b040-278">`RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="8b040-279">Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):</span><span class="sxs-lookup"><span data-stu-id="8b040-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="8b040-280">Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8b040-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="8b040-281">Kulturfallbackverhalten</span><span class="sxs-lookup"><span data-stu-id="8b040-281">Culture fallback behavior</span></span>

<span data-ttu-id="8b040-282">Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“.</span><span class="sxs-lookup"><span data-stu-id="8b040-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="8b040-283">Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück.</span><span class="sxs-lookup"><span data-stu-id="8b040-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="8b040-284">Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar.</span><span class="sxs-lookup"><span data-stu-id="8b040-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="8b040-285">Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="8b040-286">Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“.</span><span class="sxs-lookup"><span data-stu-id="8b040-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="8b040-287">„es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.</span><span class="sxs-lookup"><span data-stu-id="8b040-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="8b040-288">Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält.</span><span class="sxs-lookup"><span data-stu-id="8b040-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="8b040-289">Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:</span><span class="sxs-lookup"><span data-stu-id="8b040-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="8b040-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="8b040-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="8b040-292">*Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)</span><span class="sxs-lookup"><span data-stu-id="8b040-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="8b040-293">Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="8b040-294">Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="8b040-295">Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.</span><span class="sxs-lookup"><span data-stu-id="8b040-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="8b040-296">Erstellen von Ressourcendateien mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b040-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="8b040-297">Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="8b040-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="8b040-298">Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen.</span><span class="sxs-lookup"><span data-stu-id="8b040-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="8b040-299">In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen).</span><span class="sxs-lookup"><span data-stu-id="8b040-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="8b040-300">Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="8b040-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="8b040-301">Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="8b040-302">Hinzufügen von anderen Kulturen</span><span class="sxs-lookup"><span data-stu-id="8b040-302">Add other cultures</span></span>

<span data-ttu-id="8b040-303">Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="8b040-304">Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="8b040-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="8b040-305">Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="8b040-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="8b040-306">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="8b040-307">Konfigurieren der Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-307">Configure localization</span></span>

<span data-ttu-id="8b040-308">Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8b040-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="8b040-309">`AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="8b040-310">Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="8b040-311">`AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="8b040-312">In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="8b040-313">Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8b040-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="8b040-314">`AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="8b040-315">Lokalisierungsmiddleware</span><span class="sxs-lookup"><span data-stu-id="8b040-315">Localization middleware</span></span>

<span data-ttu-id="8b040-316">Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="8b040-317">Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8b040-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="8b040-318">Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="8b040-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8b040-319">`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="8b040-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="8b040-320">Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="8b040-321">Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="8b040-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="8b040-322">Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten.</span><span class="sxs-lookup"><span data-stu-id="8b040-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="8b040-323">Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="8b040-324">Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="8b040-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="8b040-326">Einige Apps verwenden eine Abfragezeichenfolge, um die [Kultur und Benutzeroberflächenkultur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8b040-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="8b040-327">Bei Apps, die die Ansätze „Cookie“ oder „Accept-Language-Header“ verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich.</span><span class="sxs-lookup"><span data-stu-id="8b040-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="8b040-328">Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert.</span><span class="sxs-lookup"><span data-stu-id="8b040-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="8b040-329">Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="8b040-330">Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="8b040-331">Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="8b040-332">Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="8b040-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="8b040-334">Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="8b040-335">Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines Cookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="8b040-336">`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, der verwendet wird, um zu ermitteln, welche Kulturinformationen vom Benutzer bevorzugt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="8b040-337">Der Standardname für Cookies ist `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="8b040-338">Das Cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8b040-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="8b040-339">Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="8b040-340">Der Accept-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="8b040-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="8b040-341">Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="8b040-342">Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde.</span><span class="sxs-lookup"><span data-stu-id="8b040-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="8b040-343">Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="8b040-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="8b040-344">In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.</span><span class="sxs-lookup"><span data-stu-id="8b040-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="8b040-345">Festlegen des Accept-Language-HTTP-Headers in Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8b040-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="8b040-346">Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="8b040-347">Klicken Sie auf **Sprachen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-347">Tap **Languages**.</span></span>

    ![Internetoptionen](localization/_static/lang.png)

3. <span data-ttu-id="8b040-349">Klicken Sie auf **Spracheinstellungen festlegen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="8b040-350">Klicken Sie auf **Sprache hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="8b040-351">Fügen Sie die Sprache hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-351">Add the language.</span></span>

6. <span data-ttu-id="8b040-352">Klicken Sie auf die Sprache und dann auf **Nach oben**.</span><span class="sxs-lookup"><span data-stu-id="8b040-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="8b040-353">Verwenden eines benutzerdefinierten Anbieters</span><span class="sxs-lookup"><span data-stu-id="8b040-353">Use a custom provider</span></span>

<span data-ttu-id="8b040-354">Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="8b040-355">In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft.</span><span class="sxs-lookup"><span data-stu-id="8b040-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="8b040-356">Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="8b040-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="8b040-357">Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="8b040-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="8b040-358">Programmgesteuertes Festlegen der Kultur</span><span class="sxs-lookup"><span data-stu-id="8b040-358">Set the culture programmatically</span></span>

<span data-ttu-id="8b040-359">Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="8b040-360">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:</span><span class="sxs-lookup"><span data-stu-id="8b040-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="8b040-361">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="8b040-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="8b040-362">Die Methode `SetLanguage` legt das Kulturcookie fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="8b040-363">Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="8b040-364">Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.</span><span class="sxs-lookup"><span data-stu-id="8b040-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="8b040-365">Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="8b040-365">Model binding route data and query strings</span></span>

<span data-ttu-id="8b040-366">Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="8b040-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="8b040-367">Begriffe für die Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-367">Globalization and localization terms</span></span>

<span data-ttu-id="8b040-368">Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen.</span><span class="sxs-lookup"><span data-stu-id="8b040-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="8b040-369">Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen.</span><span class="sxs-lookup"><span data-stu-id="8b040-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="8b040-370">Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.</span><span class="sxs-lookup"><span data-stu-id="8b040-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="8b040-371">[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="8b040-372">Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode.</span><span class="sxs-lookup"><span data-stu-id="8b040-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="8b040-373">Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien).</span><span class="sxs-lookup"><span data-stu-id="8b040-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="8b040-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="8b040-375">Weitere Informationen finden Sie unter [Language Culture Name (Sprachen- und Kulturbezeichnungen)](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="8b040-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="8b040-376">Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt.</span><span class="sxs-lookup"><span data-stu-id="8b040-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="8b040-377">Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“.</span><span class="sxs-lookup"><span data-stu-id="8b040-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="8b040-378">Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).</span><span class="sxs-lookup"><span data-stu-id="8b040-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="8b040-379">Begriffe:</span><span class="sxs-lookup"><span data-stu-id="8b040-379">Terms:</span></span>

* <span data-ttu-id="8b040-380">Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.</span><span class="sxs-lookup"><span data-stu-id="8b040-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="8b040-381">Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="8b040-382">Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="8b040-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="8b040-383">Kultur: Beschreibt eine Sprache und optional auch eine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="8b040-384">Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="8b040-385">(Zum Beispiel „en“, „es“)</span><span class="sxs-lookup"><span data-stu-id="8b040-385">(for example "en", "es")</span></span>
* <span data-ttu-id="8b040-386">Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt.</span><span class="sxs-lookup"><span data-stu-id="8b040-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="8b040-387">(Zum Beispiel „en-US“, „en-GB“, „es-CL“)</span><span class="sxs-lookup"><span data-stu-id="8b040-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="8b040-388">Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="8b040-389">(„en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)</span><span class="sxs-lookup"><span data-stu-id="8b040-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="8b040-390">Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.</span><span class="sxs-lookup"><span data-stu-id="8b040-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="8b040-391">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8b040-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="8b040-392">[Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="8b040-393">Globalisieren und Lokalisieren von .NET-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="8b040-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="8b040-394">Ressourcen in RESX-Dateien</span><span class="sxs-lookup"><span data-stu-id="8b040-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="8b040-395">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="8b040-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="8b040-396">Lokalisierung und Generics</span><span class="sxs-lookup"><span data-stu-id="8b040-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b040-397">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="8b040-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="8b040-398">Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="8b040-399">ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.</span><span class="sxs-lookup"><span data-stu-id="8b040-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="8b040-400">Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="8b040-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="8b040-401">Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8b040-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="8b040-402">Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.</span><span class="sxs-lookup"><span data-stu-id="8b040-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="8b040-403">Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst.</span><span class="sxs-lookup"><span data-stu-id="8b040-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="8b040-404">Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="8b040-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="8b040-405">Die Lokalisierung von Apps umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="8b040-405">App localization involves the following:</span></span>

1. <span data-ttu-id="8b040-406">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="8b040-407">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="8b040-408">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="8b040-409">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b040-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="8b040-410">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-410">Make the app's content localizable</span></span>

<span data-ttu-id="8b040-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="8b040-412">Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="8b040-413">Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="8b040-414">Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="8b040-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="8b040-415">Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“.</span><span class="sxs-lookup"><span data-stu-id="8b040-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="8b040-416">Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können.</span><span class="sxs-lookup"><span data-stu-id="8b040-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="8b040-417">Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="8b040-418">Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="8b040-419">Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="8b040-420">Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="8b040-421">Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="8b040-422">Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="8b040-423">Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="8b040-424">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-425">Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:</span><span class="sxs-lookup"><span data-stu-id="8b040-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="8b040-426">Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="8b040-427">Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="8b040-428">In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="8b040-429">Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="8b040-430">Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:</span><span class="sxs-lookup"><span data-stu-id="8b040-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="8b040-431">Lokalisierung der Ansicht</span><span class="sxs-lookup"><span data-stu-id="8b040-431">View localization</span></span>

<span data-ttu-id="8b040-432">Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an.</span><span class="sxs-lookup"><span data-stu-id="8b040-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="8b040-433">Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="8b040-434">Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="8b040-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="8b040-435">Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="8b040-436">Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="8b040-437">`ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="8b040-438">Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8b040-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="8b040-439">Sehen Sie sich das folgende Razor-Markup an:</span><span class="sxs-lookup"><span data-stu-id="8b040-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="8b040-440">Eine französische Ressourcendatei könnte Folgendes beinhalten:</span><span class="sxs-lookup"><span data-stu-id="8b040-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="8b040-441">Key</span><span class="sxs-lookup"><span data-stu-id="8b040-441">Key</span></span> | <span data-ttu-id="8b040-442">Wert</span><span class="sxs-lookup"><span data-stu-id="8b040-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="8b040-443">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-444">'Blazor'</span></span>
- <span data-ttu-id="8b040-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-445">'Identity'</span></span>
- <span data-ttu-id="8b040-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-447">'Razor'</span></span>
- <span data-ttu-id="8b040-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-448">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="8b040-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="8b040-450">Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="8b040-451">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-452">Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="8b040-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="8b040-453">Lokalisierung von DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="8b040-453">DataAnnotations localization</span></span>

<span data-ttu-id="8b040-454">Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="8b040-455">Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="8b040-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="8b040-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="8b040-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="8b040-458">In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="8b040-459">ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="8b040-460">Verwenden einer Ressourcenzeichenfolge für mehrere Klassen</span><span class="sxs-lookup"><span data-stu-id="8b040-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="8b040-461">Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8b040-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="8b040-462">Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="8b040-463">DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.</span><span class="sxs-lookup"><span data-stu-id="8b040-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="8b040-464">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="8b040-465">SupportedCultures und SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="8b040-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="8b040-466">ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="8b040-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="8b040-467">Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen.</span><span class="sxs-lookup"><span data-stu-id="8b040-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="8b040-468">`SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="8b040-469">Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="8b040-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="8b040-470">`SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="8b040-471">Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="8b040-472">Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="8b040-473">ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="8b040-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="8b040-474">Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="8b040-475">Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-475">Resource files</span></span>

<span data-ttu-id="8b040-476">Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="8b040-477">Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert.</span><span class="sxs-lookup"><span data-stu-id="8b040-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="8b040-478">Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="8b040-479">„es“ ist der Sprachcode für Spanisch.</span><span class="sxs-lookup"><span data-stu-id="8b040-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="8b040-480">Erstellen dieser Ressourcendatei in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b040-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="8b040-481">Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="8b040-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet.](localization/_static/newi.png)

2. <span data-ttu-id="8b040-484">Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="8b040-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

3. <span data-ttu-id="8b040-486">Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.</span><span class="sxs-lookup"><span data-stu-id="8b040-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

    <span data-ttu-id="8b040-488">Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8b040-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="8b040-490">Benennung von Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-490">Resource file naming</span></span>

<span data-ttu-id="8b040-491">Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt.</span><span class="sxs-lookup"><span data-stu-id="8b040-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="8b040-492">Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="8b040-493">Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-494">Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen.</span><span class="sxs-lookup"><span data-stu-id="8b040-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="8b040-495">Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="8b040-496">Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-497">Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="8b040-498">Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-499">Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden.</span><span class="sxs-lookup"><span data-stu-id="8b040-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="8b040-500">Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-501">Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.</span><span class="sxs-lookup"><span data-stu-id="8b040-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="8b040-502">Ressourcenname</span><span class="sxs-lookup"><span data-stu-id="8b040-502">Resource name</span></span> | <span data-ttu-id="8b040-503">Punkt- oder Pfadbenennung</span><span class="sxs-lookup"><span data-stu-id="8b040-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="8b040-504">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-505">'Blazor'</span></span>
- <span data-ttu-id="8b040-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-506">'Identity'</span></span>
- <span data-ttu-id="8b040-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-508">'Razor'</span></span>
- <span data-ttu-id="8b040-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-510">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-511">'Blazor'</span></span>
- <span data-ttu-id="8b040-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-512">'Identity'</span></span>
- <span data-ttu-id="8b040-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-514">'Razor'</span></span>
- <span data-ttu-id="8b040-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-516">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-517">'Blazor'</span></span>
- <span data-ttu-id="8b040-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-518">'Identity'</span></span>
- <span data-ttu-id="8b040-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-520">'Razor'</span></span>
- <span data-ttu-id="8b040-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-522">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-523">'Blazor'</span></span>
- <span data-ttu-id="8b040-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-524">'Identity'</span></span>
- <span data-ttu-id="8b040-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-526">'Razor'</span></span>
- <span data-ttu-id="8b040-527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-527">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-529">'Blazor'</span></span>
- <span data-ttu-id="8b040-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-530">'Identity'</span></span>
- <span data-ttu-id="8b040-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-532">'Razor'</span></span>
- <span data-ttu-id="8b040-533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-534">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-535">'Blazor'</span></span>
- <span data-ttu-id="8b040-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-536">'Identity'</span></span>
- <span data-ttu-id="8b040-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-538">'Razor'</span></span>
- <span data-ttu-id="8b040-539">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-540">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-541">'Blazor'</span></span>
- <span data-ttu-id="8b040-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-542">'Identity'</span></span>
- <span data-ttu-id="8b040-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-544">'Razor'</span></span>
- <span data-ttu-id="8b040-545">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-546">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-547">'Blazor'</span></span>
- <span data-ttu-id="8b040-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-548">'Identity'</span></span>
- <span data-ttu-id="8b040-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-550">'Razor'</span></span>
- <span data-ttu-id="8b040-551">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-551">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="8b040-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="8b040-553">Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="8b040-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="8b040-554">Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-554">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="8b040-555">Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-555">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="8b040-556">Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:</span><span class="sxs-lookup"><span data-stu-id="8b040-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="8b040-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="8b040-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="8b040-559">Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="8b040-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="8b040-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="8b040-561">Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="8b040-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="8b040-562">Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="8b040-563">Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt.</span><span class="sxs-lookup"><span data-stu-id="8b040-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="8b040-564">Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8b040-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="8b040-565">Die Lokalisierung funktioniert standardmäßig nicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-565">Localization does not work by default.</span></span>
* <span data-ttu-id="8b040-566">Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl.</span><span class="sxs-lookup"><span data-stu-id="8b040-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="8b040-567">`RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="8b040-568">Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):</span><span class="sxs-lookup"><span data-stu-id="8b040-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="8b040-569">Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8b040-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="8b040-570">Kulturfallbackverhalten</span><span class="sxs-lookup"><span data-stu-id="8b040-570">Culture fallback behavior</span></span>

<span data-ttu-id="8b040-571">Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“.</span><span class="sxs-lookup"><span data-stu-id="8b040-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="8b040-572">Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück.</span><span class="sxs-lookup"><span data-stu-id="8b040-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="8b040-573">Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar.</span><span class="sxs-lookup"><span data-stu-id="8b040-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="8b040-574">Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="8b040-575">Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“.</span><span class="sxs-lookup"><span data-stu-id="8b040-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="8b040-576">„es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.</span><span class="sxs-lookup"><span data-stu-id="8b040-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="8b040-577">Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält.</span><span class="sxs-lookup"><span data-stu-id="8b040-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="8b040-578">Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:</span><span class="sxs-lookup"><span data-stu-id="8b040-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="8b040-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="8b040-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="8b040-581">*Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)</span><span class="sxs-lookup"><span data-stu-id="8b040-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="8b040-582">Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="8b040-583">Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="8b040-584">Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.</span><span class="sxs-lookup"><span data-stu-id="8b040-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="8b040-585">Erstellen von Ressourcendateien mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b040-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="8b040-586">Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="8b040-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="8b040-587">Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen.</span><span class="sxs-lookup"><span data-stu-id="8b040-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="8b040-588">In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen).</span><span class="sxs-lookup"><span data-stu-id="8b040-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="8b040-589">Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="8b040-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="8b040-590">Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="8b040-591">Hinzufügen von anderen Kulturen</span><span class="sxs-lookup"><span data-stu-id="8b040-591">Add other cultures</span></span>

<span data-ttu-id="8b040-592">Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="8b040-593">Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="8b040-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="8b040-594">Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="8b040-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="8b040-595">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="8b040-596">Konfigurieren der Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-596">Configure localization</span></span>

<span data-ttu-id="8b040-597">Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8b040-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="8b040-598">`AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="8b040-599">Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="8b040-600">`AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="8b040-601">In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="8b040-602">Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8b040-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="8b040-603">`AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="8b040-604">Lokalisierungsmiddleware</span><span class="sxs-lookup"><span data-stu-id="8b040-604">Localization middleware</span></span>

<span data-ttu-id="8b040-605">Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="8b040-606">Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8b040-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="8b040-607">Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="8b040-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8b040-608">`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="8b040-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="8b040-609">Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="8b040-610">Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="8b040-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="8b040-611">Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten.</span><span class="sxs-lookup"><span data-stu-id="8b040-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="8b040-612">Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="8b040-613">Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="8b040-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="8b040-615">Einige Apps verwenden eine Abfragezeichenfolge, um die [Kultur und Benutzeroberflächenkultur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8b040-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="8b040-616">Bei Apps, die die Ansätze „Cookie“ oder „Accept-Language-Header“ verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich.</span><span class="sxs-lookup"><span data-stu-id="8b040-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="8b040-617">Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert.</span><span class="sxs-lookup"><span data-stu-id="8b040-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="8b040-618">Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="8b040-619">Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="8b040-620">Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="8b040-621">Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="8b040-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="8b040-623">Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="8b040-624">Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines Cookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="8b040-625">`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, der verwendet wird, um zu ermitteln, welche Kulturinformationen vom Benutzer bevorzugt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="8b040-626">Der Standardname für Cookies ist `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="8b040-627">Das Cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8b040-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="8b040-628">Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="8b040-629">Der Accept-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="8b040-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="8b040-630">Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="8b040-631">Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde.</span><span class="sxs-lookup"><span data-stu-id="8b040-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="8b040-632">Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="8b040-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="8b040-633">In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.</span><span class="sxs-lookup"><span data-stu-id="8b040-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="8b040-634">Festlegen des Accept-Language-HTTP-Headers in Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8b040-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="8b040-635">Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="8b040-636">Klicken Sie auf **Sprachen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-636">Tap **Languages**.</span></span>

    ![Internetoptionen](localization/_static/lang.png)

3. <span data-ttu-id="8b040-638">Klicken Sie auf **Spracheinstellungen festlegen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="8b040-639">Klicken Sie auf **Sprache hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="8b040-640">Fügen Sie die Sprache hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-640">Add the language.</span></span>

6. <span data-ttu-id="8b040-641">Klicken Sie auf die Sprache und dann auf **Nach oben**.</span><span class="sxs-lookup"><span data-stu-id="8b040-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="8b040-642">Verwenden eines benutzerdefinierten Anbieters</span><span class="sxs-lookup"><span data-stu-id="8b040-642">Use a custom provider</span></span>

<span data-ttu-id="8b040-643">Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="8b040-644">In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft.</span><span class="sxs-lookup"><span data-stu-id="8b040-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="8b040-645">Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="8b040-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="8b040-646">Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="8b040-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="8b040-647">Programmgesteuertes Festlegen der Kultur</span><span class="sxs-lookup"><span data-stu-id="8b040-647">Set the culture programmatically</span></span>

<span data-ttu-id="8b040-648">Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="8b040-649">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:</span><span class="sxs-lookup"><span data-stu-id="8b040-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="8b040-650">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="8b040-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="8b040-651">Die Methode `SetLanguage` legt das Kulturcookie fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="8b040-652">Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="8b040-653">Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.</span><span class="sxs-lookup"><span data-stu-id="8b040-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="8b040-654">Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="8b040-654">Model binding route data and query strings</span></span>

<span data-ttu-id="8b040-655">Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="8b040-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="8b040-656">Begriffe für die Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-656">Globalization and localization terms</span></span>

<span data-ttu-id="8b040-657">Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen.</span><span class="sxs-lookup"><span data-stu-id="8b040-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="8b040-658">Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen.</span><span class="sxs-lookup"><span data-stu-id="8b040-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="8b040-659">Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.</span><span class="sxs-lookup"><span data-stu-id="8b040-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="8b040-660">[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="8b040-661">Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode.</span><span class="sxs-lookup"><span data-stu-id="8b040-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="8b040-662">Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien).</span><span class="sxs-lookup"><span data-stu-id="8b040-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="8b040-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="8b040-664">Weitere Informationen finden Sie unter [Language Culture Name (Sprachen- und Kulturbezeichnungen)](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="8b040-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="8b040-665">Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt.</span><span class="sxs-lookup"><span data-stu-id="8b040-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="8b040-666">Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“.</span><span class="sxs-lookup"><span data-stu-id="8b040-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="8b040-667">Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).</span><span class="sxs-lookup"><span data-stu-id="8b040-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="8b040-668">Begriffe:</span><span class="sxs-lookup"><span data-stu-id="8b040-668">Terms:</span></span>

* <span data-ttu-id="8b040-669">Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.</span><span class="sxs-lookup"><span data-stu-id="8b040-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="8b040-670">Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="8b040-671">Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="8b040-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="8b040-672">Kultur: Beschreibt eine Sprache und optional auch eine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="8b040-673">Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="8b040-674">(Zum Beispiel „en“, „es“)</span><span class="sxs-lookup"><span data-stu-id="8b040-674">(for example "en", "es")</span></span>
* <span data-ttu-id="8b040-675">Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt.</span><span class="sxs-lookup"><span data-stu-id="8b040-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="8b040-676">(Zum Beispiel „en-US“, „en-GB“, „es-CL“)</span><span class="sxs-lookup"><span data-stu-id="8b040-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="8b040-677">Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="8b040-678">(„en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)</span><span class="sxs-lookup"><span data-stu-id="8b040-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="8b040-679">Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.</span><span class="sxs-lookup"><span data-stu-id="8b040-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="8b040-680">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8b040-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="8b040-681">[Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="8b040-682">Globalisieren und Lokalisieren von .NET-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="8b040-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="8b040-683">Ressourcen in RESX-Dateien</span><span class="sxs-lookup"><span data-stu-id="8b040-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="8b040-684">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="8b040-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="8b040-685">Lokalisierung und Generics</span><span class="sxs-lookup"><span data-stu-id="8b040-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="8b040-686">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="8b040-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="8b040-687">Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="8b040-688">ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.</span><span class="sxs-lookup"><span data-stu-id="8b040-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="8b040-689">Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="8b040-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="8b040-690">Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8b040-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="8b040-691">Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.</span><span class="sxs-lookup"><span data-stu-id="8b040-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="8b040-692">Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst.</span><span class="sxs-lookup"><span data-stu-id="8b040-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="8b040-693">Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="8b040-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="8b040-694">Die Lokalisierung von Apps umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="8b040-694">App localization involves the following:</span></span>

1. <span data-ttu-id="8b040-695">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="8b040-696">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="8b040-697">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="8b040-698">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b040-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="8b040-699">Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-699">Make the app's content localizable</span></span>

<span data-ttu-id="8b040-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="8b040-701">Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="8b040-702">Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="8b040-703">Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="8b040-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="8b040-704">Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“.</span><span class="sxs-lookup"><span data-stu-id="8b040-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="8b040-705">Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können.</span><span class="sxs-lookup"><span data-stu-id="8b040-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="8b040-706">Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="8b040-707">Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="8b040-708">Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="8b040-709">Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="8b040-710">Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="8b040-711">Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="8b040-712">Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="8b040-713">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-714">Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:</span><span class="sxs-lookup"><span data-stu-id="8b040-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="8b040-715">Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="8b040-716">Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="8b040-717">In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="8b040-718">Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="8b040-719">Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:</span><span class="sxs-lookup"><span data-stu-id="8b040-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="8b040-720">Lokalisierung der Ansicht</span><span class="sxs-lookup"><span data-stu-id="8b040-720">View localization</span></span>

<span data-ttu-id="8b040-721">Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an.</span><span class="sxs-lookup"><span data-stu-id="8b040-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="8b040-722">Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="8b040-723">Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="8b040-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="8b040-724">Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="8b040-725">Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="8b040-726">`ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="8b040-727">Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8b040-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="8b040-728">Sehen Sie sich das folgende Razor-Markup an:</span><span class="sxs-lookup"><span data-stu-id="8b040-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="8b040-729">Eine französische Ressourcendatei könnte Folgendes beinhalten:</span><span class="sxs-lookup"><span data-stu-id="8b040-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="8b040-730">Key</span><span class="sxs-lookup"><span data-stu-id="8b040-730">Key</span></span> | <span data-ttu-id="8b040-731">Wert</span><span class="sxs-lookup"><span data-stu-id="8b040-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="8b040-732">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-733">'Blazor'</span></span>
- <span data-ttu-id="8b040-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-734">'Identity'</span></span>
- <span data-ttu-id="8b040-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-736">'Razor'</span></span>
- <span data-ttu-id="8b040-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-737">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="8b040-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="8b040-739">Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="8b040-740">**Hinweis**: Normalerweise sollten Sie nur den Text lokalisieren, nicht den HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="8b040-741">Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="8b040-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="8b040-742">Lokalisierung von DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="8b040-742">DataAnnotations localization</span></span>

<span data-ttu-id="8b040-743">Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="8b040-744">Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="8b040-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="8b040-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="8b040-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="8b040-747">In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="8b040-748">ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b040-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="8b040-749">Verwenden einer Ressourcenzeichenfolge für mehrere Klassen</span><span class="sxs-lookup"><span data-stu-id="8b040-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="8b040-750">Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8b040-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="8b040-751">Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="8b040-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="8b040-752">DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.</span><span class="sxs-lookup"><span data-stu-id="8b040-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="8b040-753">Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.</span><span class="sxs-lookup"><span data-stu-id="8b040-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="8b040-754">SupportedCultures und SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="8b040-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="8b040-755">ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="8b040-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="8b040-756">Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen.</span><span class="sxs-lookup"><span data-stu-id="8b040-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="8b040-757">`SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="8b040-758">Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="8b040-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="8b040-759">`SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="8b040-760">Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="8b040-761">Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="8b040-762">ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="8b040-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="8b040-763">Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="8b040-764">Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-764">Resource files</span></span>

<span data-ttu-id="8b040-765">Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code.</span><span class="sxs-lookup"><span data-stu-id="8b040-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="8b040-766">Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert.</span><span class="sxs-lookup"><span data-stu-id="8b040-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="8b040-767">Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="8b040-768">„es“ ist der Sprachcode für Spanisch.</span><span class="sxs-lookup"><span data-stu-id="8b040-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="8b040-769">Erstellen dieser Ressourcendatei in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8b040-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="8b040-770">Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="8b040-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet.](localization/_static/newi.png)

2. <span data-ttu-id="8b040-773">Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="8b040-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

3. <span data-ttu-id="8b040-775">Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.</span><span class="sxs-lookup"><span data-stu-id="8b040-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

    <span data-ttu-id="8b040-777">Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8b040-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="8b040-779">Benennung von Ressourcendateien</span><span class="sxs-lookup"><span data-stu-id="8b040-779">Resource file naming</span></span>

<span data-ttu-id="8b040-780">Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt.</span><span class="sxs-lookup"><span data-stu-id="8b040-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="8b040-781">Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="8b040-782">Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-783">Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen.</span><span class="sxs-lookup"><span data-stu-id="8b040-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="8b040-784">Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="8b040-785">Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-786">Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b040-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="8b040-787">Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="8b040-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-788">Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden.</span><span class="sxs-lookup"><span data-stu-id="8b040-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="8b040-789">Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b040-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="8b040-790">Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.</span><span class="sxs-lookup"><span data-stu-id="8b040-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="8b040-791">Ressourcenname</span><span class="sxs-lookup"><span data-stu-id="8b040-791">Resource name</span></span> | <span data-ttu-id="8b040-792">Punkt- oder Pfadbenennung</span><span class="sxs-lookup"><span data-stu-id="8b040-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="8b040-793">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-794">'Blazor'</span></span>
- <span data-ttu-id="8b040-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-795">'Identity'</span></span>
- <span data-ttu-id="8b040-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-797">'Razor'</span></span>
- <span data-ttu-id="8b040-798">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-799">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-800">'Blazor'</span></span>
- <span data-ttu-id="8b040-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-801">'Identity'</span></span>
- <span data-ttu-id="8b040-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-803">'Razor'</span></span>
- <span data-ttu-id="8b040-804">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-805">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-806">'Blazor'</span></span>
- <span data-ttu-id="8b040-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-807">'Identity'</span></span>
- <span data-ttu-id="8b040-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-809">'Razor'</span></span>
- <span data-ttu-id="8b040-810">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-811">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-812">'Blazor'</span></span>
- <span data-ttu-id="8b040-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-813">'Identity'</span></span>
- <span data-ttu-id="8b040-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-815">'Razor'</span></span>
- <span data-ttu-id="8b040-816">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-816">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-818">'Blazor'</span></span>
- <span data-ttu-id="8b040-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-819">'Identity'</span></span>
- <span data-ttu-id="8b040-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-821">'Razor'</span></span>
- <span data-ttu-id="8b040-822">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-823">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-824">'Blazor'</span></span>
- <span data-ttu-id="8b040-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-825">'Identity'</span></span>
- <span data-ttu-id="8b040-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-827">'Razor'</span></span>
- <span data-ttu-id="8b040-828">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-829">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-830">'Blazor'</span></span>
- <span data-ttu-id="8b040-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-831">'Identity'</span></span>
- <span data-ttu-id="8b040-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-833">'Razor'</span></span>
- <span data-ttu-id="8b040-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b040-835">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8b040-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b040-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b040-836">'Blazor'</span></span>
- <span data-ttu-id="8b040-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b040-837">'Identity'</span></span>
- <span data-ttu-id="8b040-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b040-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b040-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b040-839">'Razor'</span></span>
- <span data-ttu-id="8b040-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b040-840">'SignalR' uid:</span></span> 

<span data-ttu-id="8b040-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="8b040-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="8b040-842">Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="8b040-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="8b040-843">Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-843">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="8b040-844">Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-844">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="8b040-845">Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:</span><span class="sxs-lookup"><span data-stu-id="8b040-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="8b040-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="8b040-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="8b040-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="8b040-848">Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="8b040-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="8b040-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="8b040-850">Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="8b040-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="8b040-851">Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="8b040-852">Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt.</span><span class="sxs-lookup"><span data-stu-id="8b040-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="8b040-853">Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8b040-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="8b040-854">Die Lokalisierung funktioniert standardmäßig nicht.</span><span class="sxs-lookup"><span data-stu-id="8b040-854">Localization does not work by default.</span></span>
* <span data-ttu-id="8b040-855">Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl.</span><span class="sxs-lookup"><span data-stu-id="8b040-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="8b040-856">`RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="8b040-857">Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):</span><span class="sxs-lookup"><span data-stu-id="8b040-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="8b040-858">Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8b040-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="8b040-859">Kulturfallbackverhalten</span><span class="sxs-lookup"><span data-stu-id="8b040-859">Culture fallback behavior</span></span>

<span data-ttu-id="8b040-860">Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“.</span><span class="sxs-lookup"><span data-stu-id="8b040-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="8b040-861">Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück.</span><span class="sxs-lookup"><span data-stu-id="8b040-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="8b040-862">Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar.</span><span class="sxs-lookup"><span data-stu-id="8b040-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="8b040-863">Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="8b040-864">Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“.</span><span class="sxs-lookup"><span data-stu-id="8b040-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="8b040-865">„es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.</span><span class="sxs-lookup"><span data-stu-id="8b040-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="8b040-866">Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält.</span><span class="sxs-lookup"><span data-stu-id="8b040-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="8b040-867">Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:</span><span class="sxs-lookup"><span data-stu-id="8b040-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="8b040-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="8b040-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="8b040-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="8b040-870">*Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)</span><span class="sxs-lookup"><span data-stu-id="8b040-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="8b040-871">Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="8b040-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="8b040-872">Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="8b040-873">Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.</span><span class="sxs-lookup"><span data-stu-id="8b040-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="8b040-874">Erstellen von Ressourcendateien mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b040-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="8b040-875">Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="8b040-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="8b040-876">Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen.</span><span class="sxs-lookup"><span data-stu-id="8b040-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="8b040-877">In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen).</span><span class="sxs-lookup"><span data-stu-id="8b040-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="8b040-878">Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="8b040-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="8b040-879">Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="8b040-880">Hinzufügen von anderen Kulturen</span><span class="sxs-lookup"><span data-stu-id="8b040-880">Add other cultures</span></span>

<span data-ttu-id="8b040-881">Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="8b040-882">Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="8b040-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="8b040-883">Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="8b040-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="8b040-884">Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8b040-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="8b040-885">Konfigurieren der Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-885">Configure localization</span></span>

<span data-ttu-id="8b040-886">Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8b040-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="8b040-887">`AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="8b040-888">Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="8b040-889">`AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien zu.</span><span class="sxs-lookup"><span data-stu-id="8b040-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="8b040-890">In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="8b040-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="8b040-891">Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8b040-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="8b040-892">`AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="8b040-893">Lokalisierungsmiddleware</span><span class="sxs-lookup"><span data-stu-id="8b040-893">Localization middleware</span></span>

<span data-ttu-id="8b040-894">Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b040-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="8b040-895">Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8b040-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="8b040-896">Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="8b040-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8b040-897">`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="8b040-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="8b040-898">Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="8b040-899">Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="8b040-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="8b040-900">Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten.</span><span class="sxs-lookup"><span data-stu-id="8b040-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="8b040-901">Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="8b040-902">Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="8b040-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="8b040-904">Einige Apps verwenden eine Abfragezeichenfolge, um die [Kultur und Benutzeroberflächenkultur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8b040-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="8b040-905">Bei Apps, die die Ansätze „Cookie“ oder „Accept-Language-Header“ verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich.</span><span class="sxs-lookup"><span data-stu-id="8b040-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="8b040-906">Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert.</span><span class="sxs-lookup"><span data-stu-id="8b040-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="8b040-907">Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="8b040-908">Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="8b040-909">Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="8b040-910">Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b040-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="8b040-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="8b040-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="8b040-912">Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="8b040-913">Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines Cookies.</span><span class="sxs-lookup"><span data-stu-id="8b040-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="8b040-914">`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, der verwendet wird, um zu ermitteln, welche Kulturinformationen vom Benutzer bevorzugt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="8b040-915">Der Standardname für Cookies ist `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="8b040-916">Das Cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8b040-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="8b040-917">Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="8b040-918">Der Accept-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="8b040-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="8b040-919">Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8b040-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="8b040-920">Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde.</span><span class="sxs-lookup"><span data-stu-id="8b040-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="8b040-921">Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="8b040-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="8b040-922">In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.</span><span class="sxs-lookup"><span data-stu-id="8b040-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="8b040-923">Festlegen des Accept-Language-HTTP-Headers in Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8b040-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="8b040-924">Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="8b040-925">Klicken Sie auf **Sprachen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-925">Tap **Languages**.</span></span>

    ![Internetoptionen](localization/_static/lang.png)

3. <span data-ttu-id="8b040-927">Klicken Sie auf **Spracheinstellungen festlegen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="8b040-928">Klicken Sie auf **Sprache hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8b040-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="8b040-929">Fügen Sie die Sprache hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b040-929">Add the language.</span></span>

6. <span data-ttu-id="8b040-930">Klicken Sie auf die Sprache und dann auf **Nach oben**.</span><span class="sxs-lookup"><span data-stu-id="8b040-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="8b040-931">Der Content-Language-HTTP-Header</span><span class="sxs-lookup"><span data-stu-id="8b040-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="8b040-932">Der [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)-Entitätsheader:</span><span class="sxs-lookup"><span data-stu-id="8b040-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="8b040-933">Wird verwendet, um die für die Zielgruppe vorgesehenen Sprachen zu beschreiben.</span><span class="sxs-lookup"><span data-stu-id="8b040-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="8b040-934">Ermöglicht einem Benutzer, gemäß seiner bevorzugten Sprache zu differenzieren.</span><span class="sxs-lookup"><span data-stu-id="8b040-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="8b040-935">Entitätsheader werden in HTTP-Anforderungen und -Antworten verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="8b040-936">Der `Content-Language`-Header kann durch Festlegen der Eigenschaft `ApplyCurrentCultureToResponseHeaders` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8b040-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="8b040-937">Hinzufügen des `Content-Language`-Headers:</span><span class="sxs-lookup"><span data-stu-id="8b040-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="8b040-938">Ermöglicht RequestLocalizationMiddleware, den `Content-Language`-Header mit der `CurrentUICulture` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8b040-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="8b040-939">Macht das explizite Festlegen des `Content-Language`-Antwortheaders überflüssig.</span><span class="sxs-lookup"><span data-stu-id="8b040-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="8b040-940">Verwenden eines benutzerdefinierten Anbieters</span><span class="sxs-lookup"><span data-stu-id="8b040-940">Use a custom provider</span></span>

<span data-ttu-id="8b040-941">Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="8b040-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="8b040-942">In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft.</span><span class="sxs-lookup"><span data-stu-id="8b040-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="8b040-943">Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="8b040-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="8b040-944">Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="8b040-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="8b040-945">Programmgesteuertes Festlegen der Kultur</span><span class="sxs-lookup"><span data-stu-id="8b040-945">Set the culture programmatically</span></span>

<span data-ttu-id="8b040-946">Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`.</span><span class="sxs-lookup"><span data-stu-id="8b040-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="8b040-947">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:</span><span class="sxs-lookup"><span data-stu-id="8b040-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="8b040-948">Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="8b040-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="8b040-949">Die Methode `SetLanguage` legt das Kulturcookie fest.</span><span class="sxs-lookup"><span data-stu-id="8b040-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="8b040-950">Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8b040-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="8b040-951">Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.</span><span class="sxs-lookup"><span data-stu-id="8b040-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="8b040-952">Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="8b040-952">Model binding route data and query strings</span></span>

<span data-ttu-id="8b040-953">Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="8b040-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="8b040-954">Begriffe für die Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="8b040-954">Globalization and localization terms</span></span>

<span data-ttu-id="8b040-955">Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen.</span><span class="sxs-lookup"><span data-stu-id="8b040-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="8b040-956">Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen.</span><span class="sxs-lookup"><span data-stu-id="8b040-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="8b040-957">Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.</span><span class="sxs-lookup"><span data-stu-id="8b040-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="8b040-958">[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="8b040-959">Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode.</span><span class="sxs-lookup"><span data-stu-id="8b040-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="8b040-960">Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien).</span><span class="sxs-lookup"><span data-stu-id="8b040-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="8b040-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="8b040-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="8b040-962">Weitere Informationen finden Sie unter [Language Culture Name (Sprachen- und Kulturbezeichnungen)](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="8b040-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="8b040-963">Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt.</span><span class="sxs-lookup"><span data-stu-id="8b040-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="8b040-964">Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“.</span><span class="sxs-lookup"><span data-stu-id="8b040-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="8b040-965">Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).</span><span class="sxs-lookup"><span data-stu-id="8b040-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="8b040-966">Begriffe:</span><span class="sxs-lookup"><span data-stu-id="8b040-966">Terms:</span></span>

* <span data-ttu-id="8b040-967">Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.</span><span class="sxs-lookup"><span data-stu-id="8b040-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="8b040-968">Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="8b040-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="8b040-969">Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="8b040-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="8b040-970">Kultur: Beschreibt eine Sprache und optional auch eine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="8b040-971">Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region.</span><span class="sxs-lookup"><span data-stu-id="8b040-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="8b040-972">(Zum Beispiel „en“, „es“)</span><span class="sxs-lookup"><span data-stu-id="8b040-972">(for example "en", "es")</span></span>
* <span data-ttu-id="8b040-973">Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt.</span><span class="sxs-lookup"><span data-stu-id="8b040-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="8b040-974">(Zum Beispiel „en-US“, „en-GB“, „es-CL“)</span><span class="sxs-lookup"><span data-stu-id="8b040-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="8b040-975">Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält.</span><span class="sxs-lookup"><span data-stu-id="8b040-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="8b040-976">(„en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)</span><span class="sxs-lookup"><span data-stu-id="8b040-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="8b040-977">Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.</span><span class="sxs-lookup"><span data-stu-id="8b040-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="8b040-978">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8b040-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="8b040-979">[Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b040-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="8b040-980">Globalisieren und Lokalisieren von .NET-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="8b040-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="8b040-981">Ressourcen in RESX-Dateien</span><span class="sxs-lookup"><span data-stu-id="8b040-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="8b040-982">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="8b040-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="8b040-983">Lokalisierung und Generics</span><span class="sxs-lookup"><span data-stu-id="8b040-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
