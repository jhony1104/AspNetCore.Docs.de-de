---
title: Taghilfsprogrammkomponenten in ASP.NET Core
author: scottaddie
description: Informationen zu Taghilfsprogrammkomponenten und deren Verwendung in ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 23e244649350b41e4112d10df63139864e5b4381
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022214"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="6abdd-103">Taghilfsprogrammkomponenten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6abdd-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="6abdd-104">Von [Scott Addie](https://twitter.com/Scott_Addie) und [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="6abdd-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="6abdd-105">Eine Taghilfsprogrammkomponente ist ein Taghilfsprogramm, mit dem Sie HTML-Elemente aus serverseitigem Code bedingt ändern oder hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="6abdd-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="6abdd-106">Dieses Feature ist in ASP.NET Core 2.0 oder höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6abdd-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="6abdd-107">ASP.NET Core enthält zwei integrierte Taghilfsprogrammkomponenten: `head` und `body`.</span><span class="sxs-lookup"><span data-stu-id="6abdd-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="6abdd-108">Sie befinden sich im Namespace <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> und können in MVC und Razor Pages verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="6abdd-109">Taghilfsprogrammkomponenten benötigen keine Registrierung bei der App in *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6abdd-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="6abdd-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6abdd-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="6abdd-111">Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="6abdd-111">Use cases</span></span>

<span data-ttu-id="6abdd-112">Zwei gängige Anwendungsfälle für Taghilfsprogrammkomponenten sind:</span><span class="sxs-lookup"><span data-stu-id="6abdd-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="6abdd-113">Einfügen eines `<link>` in `<head>`.</span><span class="sxs-lookup"><span data-stu-id="6abdd-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="6abdd-114">Einfügen eines `<script>` in `<body>`.</span><span class="sxs-lookup"><span data-stu-id="6abdd-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="6abdd-115">In den folgenden Abschnitten werden diese Anwendungsfälle beschrieben.</span><span class="sxs-lookup"><span data-stu-id="6abdd-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="6abdd-116">Einfügen in HTML-head-Element</span><span class="sxs-lookup"><span data-stu-id="6abdd-116">Inject into HTML head element</span></span>

<span data-ttu-id="6abdd-117">Im HTML-`<head>`-Element werden CSS-Dateien häufig mit dem HTML-`<link>`-Element importiert.</span><span class="sxs-lookup"><span data-stu-id="6abdd-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="6abdd-118">Der folgende Code fügt ein `<link>`-Element in das `<head>`-Element mit der `head`-Taghilfsprogrammkomponente ein:</span><span class="sxs-lookup"><span data-stu-id="6abdd-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="6abdd-119">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="6abdd-119">In the preceding code:</span></span>

* <span data-ttu-id="6abdd-120">`AddressStyleTagHelperComponent` implementiert <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="6abdd-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="6abdd-121">Die Abstraktion:</span><span class="sxs-lookup"><span data-stu-id="6abdd-121">The abstraction:</span></span>
  * <span data-ttu-id="6abdd-122">Ermöglicht die Initialisierung der Klasse mit einem <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="6abdd-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="6abdd-123">Ermöglicht die Verwendung von Taghilfsprogrammkomponenten zum Hinzufügen oder Ändern von HTML-Elementen.</span><span class="sxs-lookup"><span data-stu-id="6abdd-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="6abdd-124">Die <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>-Eigenschaft definiert die Reihenfolge, in der die Komponenten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="6abdd-125">`Order` ist erforderlich, wenn mehrere Verwendungsmöglichkeiten für Taghilfsprogrammkomponenten in einer App vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="6abdd-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="6abdd-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> vergleicht den <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*>-Eigenschaftswert des Ausführungskontexts mit `head`.</span><span class="sxs-lookup"><span data-stu-id="6abdd-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="6abdd-127">Wenn der Vergleich zu TRUE ausgewertet wird, wird der Inhalt des `_style`-Felds in das HTML`<head>`-Element eingefügt.</span><span class="sxs-lookup"><span data-stu-id="6abdd-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="6abdd-128">Einfügen in HTML-body-Element</span><span class="sxs-lookup"><span data-stu-id="6abdd-128">Inject into HTML body element</span></span>

<span data-ttu-id="6abdd-129">Die `body`-Taghilfsprogrammkomponente kann ein `<script>`-Element in das `<body>`-Element einfügen.</span><span class="sxs-lookup"><span data-stu-id="6abdd-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="6abdd-130">Im folgenden Code wird diese Technik veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6abdd-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="6abdd-131">Eine separate HTML-Datei wird zum Speichern des `<script>`-Elements verwendet.</span><span class="sxs-lookup"><span data-stu-id="6abdd-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="6abdd-132">Durch die HTML-Datei wird der Code übersichtlicher und besser verwaltbar.</span><span class="sxs-lookup"><span data-stu-id="6abdd-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="6abdd-133">Der vorangehende Code liest den Inhalt von *TagHelpers/Templates/AddressToolTipScript.html* und fügt diesen mit der Ausgabe des Taghilfsprogramms an.</span><span class="sxs-lookup"><span data-stu-id="6abdd-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="6abdd-134">Die Datei *AddressToolTipScript.html* enthält das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="6abdd-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="6abdd-135">Der vorangehende Code bindet ein [Bootstrap-QuickInfo-Widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) an ein beliebiges `<address>`-Element, das ein `printable`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="6abdd-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="6abdd-136">Der Effekt wird sichtbar, wenn ein Mauszeiger über das Element bewegt wird.</span><span class="sxs-lookup"><span data-stu-id="6abdd-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="6abdd-137">Registrieren einer Komponente</span><span class="sxs-lookup"><span data-stu-id="6abdd-137">Register a Component</span></span>

<span data-ttu-id="6abdd-138">Eine Taghilfsprogrammkomponente muss der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="6abdd-139">Es gibt drei Möglichkeiten, der Sammlung Taghilfsprogrammkomponenten hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="6abdd-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="6abdd-140">Registrierung über Dienstcontainer</span><span class="sxs-lookup"><span data-stu-id="6abdd-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="6abdd-141">Registrierung über Razor-Datei</span><span class="sxs-lookup"><span data-stu-id="6abdd-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="6abdd-142">Registrierung über Seitenmodell oder Controller</span><span class="sxs-lookup"><span data-stu-id="6abdd-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="6abdd-143">Registrierung über Dienstcontainer</span><span class="sxs-lookup"><span data-stu-id="6abdd-143">Registration via services container</span></span>

<span data-ttu-id="6abdd-144">Wenn die Klasse der Taghilfsprogrammkomponente nicht mit <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> verwaltet wird, muss sie mit dem [Abhängigkeitsinjektionssystem (Dependency Injection, DI)](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="6abdd-145">Der folgende `Startup.ConfigureServices`-Code registriert die `AddressStyleTagHelperComponent`- und `AddressScriptTagHelperComponent`- -Klassen mit einer [vorübergehenden Lebensdauer](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="6abdd-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="6abdd-146">Registrierung über Razor-Datei</span><span class="sxs-lookup"><span data-stu-id="6abdd-146">Registration via Razor file</span></span>

<span data-ttu-id="6abdd-147">Wenn die Taghilfsprogrammkomponente nicht mit Abhängigkeitsinjektion registriert wird, kann sie über eine Razor Pages-Seite oder eine MVC-Ansicht registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="6abdd-148">Diese Technik wird verwendet, um das injizierte Markup und die Reihenfolge der Komponentenausführung aus einer Razor-Datei zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6abdd-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="6abdd-149">`ITagHelperComponentManager` dient zum Hinzufügen von Taghilfsprogrammkomponenten oder zum Entfernen aus der App.</span><span class="sxs-lookup"><span data-stu-id="6abdd-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="6abdd-150">Im folgenden Code wird diese Technik mit `AddressTagHelperComponent` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6abdd-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="6abdd-151">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="6abdd-151">In the preceding code:</span></span>

* <span data-ttu-id="6abdd-152">Die `@inject`-Direktive stellt eine Instanz von `ITagHelperComponentManager` zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6abdd-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="6abdd-153">Die Instanz wird einer Variablen mit dem Namen `manager` für den Downstreamzugriff in der Razor-Datei zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="6abdd-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="6abdd-154">Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6abdd-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="6abdd-155">`AddressTagHelperComponent` wird geändert, um einen Konstruktor zu berücksichtigen, der die `markup`- und `order`-Parameter annimmt:</span><span class="sxs-lookup"><span data-stu-id="6abdd-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="6abdd-156">Der bereitgestellte `markup`-Parameter wird in `ProcessAsync` wie folgt verwendet:</span><span class="sxs-lookup"><span data-stu-id="6abdd-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="6abdd-157">Registrierung über Seitenmodell oder Controller</span><span class="sxs-lookup"><span data-stu-id="6abdd-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="6abdd-158">Wenn die Taghilfsprogrammkomponente nicht mit Abhängigkeitsinjektion registriert wird, kann sie über ein Razor Pages-Seitenmodell oder einen MVC-Controller registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6abdd-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="6abdd-159">Diese Methode eignet sich zum Trennen von C#-Logik von Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="6abdd-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="6abdd-160">Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="6abdd-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="6abdd-161">Die Taghilfsprogrammkomponente wird der Sammlung „Taghilfsprogrammkomponenten“ der Instanz hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6abdd-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="6abdd-162">Das folgende Razor Pages-Seitenmodell veranschaulicht diese Technik mit `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="6abdd-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="6abdd-163">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="6abdd-163">In the preceding code:</span></span>

* <span data-ttu-id="6abdd-164">Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="6abdd-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="6abdd-165">Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6abdd-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="6abdd-166">Erstellen einer Komponente</span><span class="sxs-lookup"><span data-stu-id="6abdd-166">Create a Component</span></span>

<span data-ttu-id="6abdd-167">So erstellen Sie eine benutzerdefinierte Taghilfsprogrammkomponente:</span><span class="sxs-lookup"><span data-stu-id="6abdd-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="6abdd-168">Erstellen Sie eine öffentliche Klasse, die von <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> ableitet ist.</span><span class="sxs-lookup"><span data-stu-id="6abdd-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="6abdd-169">Wenden Sie ein [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute)-Attribut auf die Klasse an.</span><span class="sxs-lookup"><span data-stu-id="6abdd-169">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="6abdd-170">Geben Sie den Namen des HTML-Zielelements an.</span><span class="sxs-lookup"><span data-stu-id="6abdd-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="6abdd-171">*Optional:* Wenden Sie ein [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute)-Attribut auf die Klasse an, um die Anzeige des Typs in IntelliSense zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="6abdd-171">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="6abdd-172">Der folgende Code erstellt eine benutzerdefinierte Taghilfsprogrammkomponente, das als Ziel das `<address>`-HTML-Element aufweist:</span><span class="sxs-lookup"><span data-stu-id="6abdd-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="6abdd-173">Verwenden Sie die benutzerdefinierte `address`-Taghilfsprogrammkomponente zum Einfügen von HTML-Markup wie hier gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6abdd-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="6abdd-174">Die obige `ProcessAsync`-Methode injiziert den für <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> bereitgestellten HTML-Code in das entsprechende `<address>`-Element.</span><span class="sxs-lookup"><span data-stu-id="6abdd-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="6abdd-175">Die Einfügung tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="6abdd-175">The injection occurs when:</span></span>

* <span data-ttu-id="6abdd-176">Der `TagName`-Eigenschaftswert des Ausführungskontexts `address` entspricht.</span><span class="sxs-lookup"><span data-stu-id="6abdd-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="6abdd-177">Das entsprechende `<address>`-Element über ein `printable`-Attribut verfügt.</span><span class="sxs-lookup"><span data-stu-id="6abdd-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="6abdd-178">Beispielsweise wird die `if`-Anweisung bei der Verarbeitung des folgenden `<address>`-Elements zu TRUE ausgewertet:</span><span class="sxs-lookup"><span data-stu-id="6abdd-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="6abdd-179">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6abdd-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
