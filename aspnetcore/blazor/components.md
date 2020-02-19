---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447099"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c3aa5-103">Erstellen und Verwenden von ASP.net Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="c3aa5-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c3aa5-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c3aa5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c3aa5-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3aa5-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="c3aa5-106">-apps werden mithilfe von- *Komponenten*erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-106"> apps are built using *components*.</span></span> <span data-ttu-id="c3aa5-107">Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c3aa5-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c3aa5-109">Komponenten sind flexibel und leicht zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="c3aa5-110">Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c3aa5-111">Komponenten Klassen</span><span class="sxs-lookup"><span data-stu-id="c3aa5-111">Component classes</span></span>

<span data-ttu-id="c3aa5-112">Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c3aa5-113">Eine Komponente in Blazor wird formal als *Razor-Komponente*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="c3aa5-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="c3aa5-115">Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="c3aa5-116">Die Benutzeroberfläche für eine Komponente wird mit HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c3aa5-117">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c3aa5-118">Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c3aa5-119">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c3aa5-120">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c3aa5-121">Im `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c3aa5-122">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="c3aa5-123">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c3aa5-124">Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c3aa5-125">Im folgenden Beispiel wird ausgewertet und gerendert:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c3aa5-126">`_headingFontStyle` zum CSS-Eigenschafts Wert für `font-style`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c3aa5-127">`_headingText` auf den Inhalt des `<h1>` Elements.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c3aa5-128">Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="c3aa5-129"> vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen auf die Dokumentobjektmodell des Browsers (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="c3aa5-130">Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c3aa5-131">Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="c3aa5-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="c3aa5-132">Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="c3aa5-133">In der Regel wird der Namespace einer Komponente aus dem Stamm Namespace der APP und dem Speicherort (Ordner) der Komponente in der APP abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="c3aa5-134">Wenn der Stamm Namespace der APP `BlazorApp` ist und sich die `Counter` Komponente im Ordner " *pages* " befindet:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="c3aa5-135">Der Namespace der `Counter` Komponente ist `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="c3aa5-136">Der voll qualifizierte Typname der Komponente ist `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="c3aa5-137">Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="c3aa5-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="c3aa5-138">Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports Razor* -Datei der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="c3aa5-139">Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `BlazorApp`ist:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="c3aa5-140">Taghilfsprogramme werden in Komponenten nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-140">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="c3aa5-141">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden*in Razor-Komponenten (Razor* -Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-141">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="c3aa5-142">Um taghilfsobjekte in Blazorbereitzustellen, erstellen Sie eine-Komponente mit der gleichen Funktionalität wie das taghilfsprogramm, und verwenden Sie stattdessen die-Komponente.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-142">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="c3aa5-143">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="c3aa5-143">Use components</span></span>

<span data-ttu-id="c3aa5-144">Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c3aa5-145">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c3aa5-146">Bei der Attribut Bindung wird Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="c3aa5-146">Attribute binding is case sensitive.</span></span> <span data-ttu-id="c3aa5-147">Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-147">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="c3aa5-148">Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent` Instanz:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-148">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="c3aa5-149">*Komponenten/headingcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-149">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="c3aa5-150">Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-150">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="c3aa5-151">Durch das Hinzufügen einer `@using`-Direktive für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-151">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="c3aa5-152">Routing</span><span class="sxs-lookup"><span data-stu-id="c3aa5-152">Routing</span></span>

<span data-ttu-id="c3aa5-153">Das Routing in Blazor erfolgt durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der app.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-153">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="c3aa5-154">Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-154">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c3aa5-155">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-155">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="c3aa5-156">Weitere Informationen finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-156">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="c3aa5-157">Parameter</span><span class="sxs-lookup"><span data-stu-id="c3aa5-157">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="c3aa5-158">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="c3aa5-158">Route parameters</span></span>

<span data-ttu-id="c3aa5-159">Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-159">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="c3aa5-160">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-160">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="c3aa5-161">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-161">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="c3aa5-162">Optionale Parameter werden nicht unterstützt, sodass im vorherigen Beispiel zwei `@page` Direktiven angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-162">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="c3aa5-163">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-163">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c3aa5-164">Die zweite `@page`-Direktive empfängt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-164">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="c3aa5-165">Die *catch-all-* Parameter Syntax (`*`/`**`), die den Pfad über mehrere Ordner Grenzen hinweg erfasst, wird in Razor-*Komponenten (Razor*-Komponenten) **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-165">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="c3aa5-166">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="c3aa5-166">Component parameters</span></span>

<span data-ttu-id="c3aa5-167">Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-167">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c3aa5-168">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-168">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c3aa5-169">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-169">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="c3aa5-170">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft der `ChildComponent`fest.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-170">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c3aa5-171">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-171">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="c3aa5-172">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="c3aa5-172">Child content</span></span>

<span data-ttu-id="c3aa5-173">Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-173">Components can set the content of another component.</span></span> <span data-ttu-id="c3aa5-174">Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-174">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c3aa5-175">Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-175">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="c3aa5-176">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-176">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c3aa5-177">Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`des Bootstrap-Panels gerendert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-177">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c3aa5-178">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c3aa5-179">Die Eigenschaft, die den `RenderFragment` Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-179">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c3aa5-180">Der `ParentComponent` in der Beispiel-App kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>` Tags platziert wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-180">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c3aa5-181">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-181">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c3aa5-182">Attribut-Verteilung und beliebige Parameter</span><span class="sxs-lookup"><span data-stu-id="c3aa5-182">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c3aa5-183">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-183">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c3aa5-184">Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`](xref:mvc/views/razor#attributes) Razor-Direktive gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-184">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="c3aa5-185">Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-185">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c3aa5-186">Beispielsweise kann es mühsam sein, Attribute für eine `<input>`, die viele Parameter unterstützt, separat zu definieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-186">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c3aa5-187">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attribut-splatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-187">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="c3aa5-188">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichen folgen Schlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-188">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="c3aa5-189">Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch eine Option.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-189">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="c3aa5-190">Die gerenderten `<input>` Elemente mit beiden Ansätzen sind identisch:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-190">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="c3aa5-191">Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter mithilfe des `[Parameter]`-Attributs, wobei die `CaptureUnmatchedValues`-Eigenschaft auf `true`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-191">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c3aa5-192">Die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` ermöglicht dem Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-192">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c3aa5-193">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-193">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="c3aa5-194">Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichen folgen Schlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-194">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c3aa5-195">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-195">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="c3aa5-196">Die Position `@attributes` relativ zur Position von Element Attributen ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-196">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="c3aa5-197">Wenn `@attributes` auf dem Element splatted sind, werden die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-197">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="c3aa5-198">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child` Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-198">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="c3aa5-199">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="c3aa5-199">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c3aa5-200">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-200">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c3aa5-201">Das `extra`-Attribut der `Child` Komponente ist rechts von `@attributes`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-201">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="c3aa5-202">Die gerenderte `<div>` der `Parent` Komponente enthält `extra="5"`, wenn Sie über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-202">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="c3aa5-203">Im folgenden Beispiel wird die Reihenfolge der `extra` und `@attributes` im `<div>`der `Child` Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-203">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="c3aa5-204">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="c3aa5-204">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c3aa5-205">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-205">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c3aa5-206">Der gerenderte `<div>` in der `Parent` Komponente enthält beim Durchlaufen des zusätzlichen Attributs `extra="10"`:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-206">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="c3aa5-207">Verweise auf Komponenten erfassen</span><span class="sxs-lookup"><span data-stu-id="c3aa5-207">Capture references to components</span></span>

<span data-ttu-id="c3aa5-208">Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, z. b. `Show` oder `Reset`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-208">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c3aa5-209">So erfassen Sie einen Komponenten Verweis:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-209">To capture a component reference:</span></span>

* <span data-ttu-id="c3aa5-210">Fügen Sie der untergeordneten Komponente ein [`@ref`](xref:mvc/views/razor#ref) Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-210">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="c3aa5-211">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-211">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="c3aa5-212">Wenn die Komponente gerendert wird, wird das `_loginDialog` Feld mit der `MyLoginDialog` untergeordneten Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-212">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="c3aa5-213">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-213">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3aa5-214">Die `_loginDialog` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-214">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c3aa5-215">Bis zu diesem Punkt sind keine Verweise mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-215">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="c3aa5-216">Um Komponenten Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, verwenden Sie die [onafterrenderasync-Methode oder die onafterrendering-Methode](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="c3aa5-216">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="c3aa5-217">Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion</span><span class="sxs-lookup"><span data-stu-id="c3aa5-217">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="c3aa5-218">Komponenten Verweise werden nicht an JavaScript-Code übermittelt,&mdash;Sie nur in .NET-Code verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-218">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c3aa5-219">Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-219">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c3aa5-220">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-220">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c3aa5-221">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-221">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="c3aa5-222">Komponenten Methoden extern aufrufen, um den Status zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="c3aa5-222">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="c3aa5-223"> verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-223"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="c3aa5-224">Die [Lebenszyklus Methoden](xref:blazor/lifecycle) einer Komponente und alle Ereignis Rückrufe, die von Blazor ausgelöst werden, werden für diese `SynchronizationContext`ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-224">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="c3aa5-225">Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an Blazor-`SynchronizationContext`weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-225">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="c3aa5-226">Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-226">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="c3aa5-227">Registrieren Sie den `NotifierService` als singletion:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-227">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="c3aa5-228">Registrieren Sie den Dienst in Blazor Webassembly in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-228">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="c3aa5-229">Registrieren Sie in Blazor Server den Dienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-229">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="c3aa5-230">Verwenden Sie die `NotifierService`, um eine Komponente zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-230">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="c3aa5-231">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify` Methode der Komponente außerhalb des `SynchronizationContext`der Blazorauf.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-231">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="c3aa5-232">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-232">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c3aa5-233">Verwenden Sie \@Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-233">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c3aa5-234">Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss Blazorder diffingalgorithmus entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-234">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c3aa5-235">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-235">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c3aa5-236">Betrachten Sie das folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-236">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="c3aa5-237">Der Inhalt der `People` Sammlung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-237">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c3aa5-238">Wenn die Komponente erneut ausgeführt wird, kann sich die `<DetailsEditor>` Komponente ändern, um unterschiedliche `Details` Parameterwerte zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-238">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c3aa5-239">Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-239">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c3aa5-240">In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-240">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c3aa5-241">Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-241">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="c3aa5-242">`@key` bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-242">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="c3aa5-243">Wenn sich die `People`-Auflistung ändert, behält der diff-Algorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-243">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c3aa5-244">Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>` Instanz aus der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-244">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c3aa5-245">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-245">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3aa5-246">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>` Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-246">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c3aa5-247">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-247">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3aa5-248">Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-248">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c3aa5-249">In einigen Szenarios minimiert die Verwendung von `@key` die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-249">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3aa5-250">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-250">Keys are local to each container element or component.</span></span> <span data-ttu-id="c3aa5-251">Schlüssel werden nicht global über das Dokument hinweg verglichen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-251">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c3aa5-252">Verwendung \@Schlüssels</span><span class="sxs-lookup"><span data-stu-id="c3aa5-252">When to use \@key</span></span>

<span data-ttu-id="c3aa5-253">In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. b. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um die `@key`zu definieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-253">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="c3aa5-254">Sie können `@key` auch verwenden, um zu verhindern, dass Blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-254">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="c3aa5-255">Wenn `@currentPerson` geändert wird, erzwingt die `@key` Attribute-Direktive Blazor, den gesamten `<div>` und seine Nachfolger zu verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-255">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c3aa5-256">Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-256">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c3aa5-257">Verwendung \@Schlüssels nicht</span><span class="sxs-lookup"><span data-stu-id="c3aa5-257">When not to use \@key</span></span>

<span data-ttu-id="c3aa5-258">Beim Durchlaufen der `@key`werden Kosten für die Leistung anfallen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-258">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="c3aa5-259">Die Leistungskosten sind nicht groß, sondern geben nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-259">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c3aa5-260">Auch wenn `@key` nicht verwendet wird, behält Blazor die untergeordneten Elemente und Komponenten Instanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-260">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c3aa5-261">Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-261">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c3aa5-262">Welche Werte müssen für \@Schlüssel verwendet werden?</span><span class="sxs-lookup"><span data-stu-id="c3aa5-262">What values to use for \@key</span></span>

<span data-ttu-id="c3aa5-263">Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-263">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="c3aa5-264">Modell Objektinstanzen (z. b. eine `Person`-Instanz, wie im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="c3aa5-264">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c3aa5-265">Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-265">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c3aa5-266">Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string`oder `Guid`).</span><span class="sxs-lookup"><span data-stu-id="c3aa5-266">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c3aa5-267">Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-267">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="c3aa5-268">Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst Blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-268">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="c3aa5-269">Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-269">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="c3aa5-270">Unterstützung für Teil Klassen</span><span class="sxs-lookup"><span data-stu-id="c3aa5-270">Partial class support</span></span>

<span data-ttu-id="c3aa5-271">Razor-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-271">Razor components are generated as partial classes.</span></span> <span data-ttu-id="c3aa5-272">Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-272">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="c3aa5-273">C#Code wird in einem [`@code`](xref:mvc/views/razor#code) -Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-273">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="c3aa5-274">Mithilfe dieses Ansatzes definieren Blazor Vorlagen ihre Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-274">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="c3aa5-275">C#der Code wird in einer Code Behind-Datei platziert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-275">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="c3aa5-276">Das folgende Beispiel zeigt die Standard `Counter` Komponente mit einem `@code`-Block in einer APP, die aus einer Blazor Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-276">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="c3aa5-277">HTML-Markup, Razor-Code C# und Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-277">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="c3aa5-278">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-278">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="c3aa5-279">Die `Counter` Komponente kann auch mit einer Code Behind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-279">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="c3aa5-280">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-280">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="c3aa5-281">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-281">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="c3aa5-282">Fügen Sie alle erforderlichen Namespaces der partiellen Klassendatei nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-282">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="c3aa5-283">Typische Namespaces, die von Razor-Komponenten verwendet werden, sind:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-283">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="c3aa5-284">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="c3aa5-284">Specify a base class</span></span>

<span data-ttu-id="c3aa5-285">Die [`@inherits`](xref:mvc/views/razor#inherits) -Direktive kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-285">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="c3aa5-286">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse erben kann, `BlazorRocksBase`, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-286">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="c3aa5-287">Die Basisklasse sollte von `ComponentBase`abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-287">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="c3aa5-288">*Pages/blazorrocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-288">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="c3aa5-289">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-289">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="c3aa5-290">Attribut angeben</span><span class="sxs-lookup"><span data-stu-id="c3aa5-290">Specify an attribute</span></span>

<span data-ttu-id="c3aa5-291">Attribute können in Razor-Komponenten mit der [`@attribute`](xref:mvc/views/razor#attribute) -Direktive angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-291">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="c3aa5-292">Im folgenden Beispiel wird das `[Authorize]`-Attribut auf die-Komponenten Klasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-292">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="c3aa5-293">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="c3aa5-293">Import components</span></span>

<span data-ttu-id="c3aa5-294">Der Namespace einer mit Razor erstellten Komponente basiert auf (in Prioritäts Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="c3aa5-294">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="c3aa5-295">[`@namespace`](xref:mvc/views/razor#namespace) Bezeichnung in Razor-Datei ( *. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="c3aa5-295">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="c3aa5-296">Das Projekt `RootNamespace` in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="c3aa5-296">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="c3aa5-297">Der Projektname, der aus dem Dateinamen der Projektdatei ( *. csproj*) und dem Pfad des Projekt Stamms zur Komponente stammt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-297">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="c3aa5-298">Das Framework löst z. b. *{Project root}/pages/index.Razor* (*blazorsample. csproj*) in den Namespace `BlazorSample.Pages`auf.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-298">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="c3aa5-299">Komponenten folgen C# den namens Bindungs Regeln.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-299">Components follow C# name binding rules.</span></span> <span data-ttu-id="c3aa5-300">Für die `Index` Komponente in diesem Beispiel sind die Komponenten im Bereich alle Komponenten:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-300">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="c3aa5-301">Im gleichen Ordner, *Seiten*.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-301">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="c3aa5-302">Die Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-302">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="c3aa5-303">Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der [`@using`](xref:mvc/views/razor#using) Direktive von Razor in den Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="c3aa5-303">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="c3aa5-304">Wenn eine andere Komponente, `NavMenu.razor`im Ordner " *blazorsample/Shared/* " vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using`-Anweisung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-304">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="c3aa5-305">Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, die die [`@using`](xref:mvc/views/razor#using) -Direktive nicht benötigen:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-305">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="c3aa5-306">Die `global::` Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-306">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c3aa5-307">Das Importieren von Komponenten mit Alias `using` Anweisungen (z. b. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-307">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c3aa5-308">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-308">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c3aa5-309">Beispielsweise wird das Hinzufügen von `@using BlazorSample` und verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-309">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c3aa5-310">Attribute für bedingtes HTML-Element</span><span class="sxs-lookup"><span data-stu-id="c3aa5-310">Conditional HTML element attributes</span></span>

<span data-ttu-id="c3aa5-311">HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-311">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c3aa5-312">Wenn der Wert `false` oder `null`ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-312">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c3aa5-313">Wenn der Wert `true`ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-313">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c3aa5-314">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-314">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c3aa5-315">Wenn `IsCompleted` `true`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-315">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c3aa5-316">Wenn `IsCompleted` `false`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-316">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c3aa5-317">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-317">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="c3aa5-318">Einige HTML-Attribute, wie z. b. [Aria-gedrückt](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), funktionieren nicht ordnungsgemäß, wenn der .NET-Typ ein `bool`ist.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-318">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="c3aa5-319">Verwenden Sie in diesen Fällen einen `string` Typ anstelle eines `bool`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-319">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c3aa5-320">Unformatierte HTML</span><span class="sxs-lookup"><span data-stu-id="c3aa5-320">Raw HTML</span></span>

<span data-ttu-id="c3aa5-321">Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-321">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c3aa5-322">Um unformatierten HTML-Code zu erstellen, packen Sie den HTML-Inhalt in einen `MarkupString` Wert</span><span class="sxs-lookup"><span data-stu-id="c3aa5-322">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c3aa5-323">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-323">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c3aa5-324">Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-324">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c3aa5-325">Im folgenden Beispiel wird gezeigt, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-325">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="c3aa5-326">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="c3aa5-326">Cascading values and parameters</span></span>

<span data-ttu-id="c3aa5-327">In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-327">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="c3aa5-328">Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-328">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="c3aa5-329">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-329">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="c3aa5-330">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="c3aa5-330">Theme example</span></span>

<span data-ttu-id="c3aa5-331">Im folgenden Beispiel der Beispiel-App gibt die `ThemeInfo`-Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-331">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="c3aa5-332">*Uithmeclasses/themerfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-332">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="c3aa5-333">Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-333">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="c3aa5-334">Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-334">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="c3aa5-335">Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-335">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="c3aa5-336">`ButtonClass` wird der Wert `btn-success` in der Layoutkomponente zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-336">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="c3aa5-337">Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierenden Objekts nutzen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-337">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="c3aa5-338">`CascadingValuesParametersLayout` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-338">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="c3aa5-339">Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-339">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="c3aa5-340">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-340">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="c3aa5-341">In der Beispiel-App bindet die `CascadingValuesParametersTheme` Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-341">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="c3aa5-342">Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-342">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="c3aa5-343">`CascadingValuesParametersTheme` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-343">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="c3aa5-344">Um mehrere Werte desselben Typs innerhalb derselben Unterstruktur zu kaskadieren, stellen Sie jeder `CascadingValue` Komponente und der entsprechenden `CascadingParameter`eine eindeutige `Name` Zeichenfolge bereit.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-344">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="c3aa5-345">Im folgenden Beispiel werden zwei `CascadingValue` Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen kaskadieren:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-345">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="c3aa5-346">In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-346">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="c3aa5-347">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="c3aa5-347">TabSet example</span></span>

<span data-ttu-id="c3aa5-348">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-348">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="c3aa5-349">Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-349">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="c3aa5-350">Die Beispiel-App verfügt über eine `ITab` Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-350">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="c3aa5-351">Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab` Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-351">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="c3aa5-352">Die untergeordneten `Tab` Komponenten werden nicht explizit als Parameter an die `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-352">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="c3aa5-353">Stattdessen sind die untergeordneten `Tab` Komponenten Teil des untergeordneten Inhalts des `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-353">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="c3aa5-354">Allerdings müssen die `TabSet` weiterhin über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code erforderlich ist, kann die `TabSet` Komponente *sich selbst als kaskadierenden Wert bereitstellen* , der dann von den Nachfolger `Tab` Komponenten abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-354">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="c3aa5-355">`TabSet` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-355">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="c3aa5-356">Die Nachfolger `Tab` Komponenten erfassen den enthaltenden `TabSet` als kaskadierenden Parameter, sodass sich die `Tab` Komponenten dem `TabSet` und der Koordinate, auf der die Registerkarte aktiv ist, hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-356">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="c3aa5-357">`Tab` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-357">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="c3aa5-358">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="c3aa5-358">Razor templates</span></span>

<span data-ttu-id="c3aa5-359">Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-359">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c3aa5-360">Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-360">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c3aa5-361">Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`-und `RenderFragment<T>` Werte angeben und Vorlagen direkt in einer-Komponente darstellen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-361">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="c3aa5-362">Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](xref:blazor/templated-components)übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-362">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="c3aa5-363">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-363">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="c3aa5-364">SVG-Bilder (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="c3aa5-364">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="c3aa5-365">Seit Blazor rendert HTML-Bilder, die vom Browser unterstützt werden, einschließlich der SVG (Scalable Vector Graphics)-*Bilder (SVG),* werden über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-365">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="c3aa5-366">Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c3aa5-366">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="c3aa5-367">Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-367">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="c3aa5-368">Wenn Sie ein `<svg>`-Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-368">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="c3aa5-369">Beispielsweise werden `<use>` Tags derzeit nicht beachtet, und `@bind` können nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-369">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="c3aa5-370">Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-370">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3aa5-371">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c3aa5-371">Additional resources</span></span>

* <span data-ttu-id="c3aa5-372"><xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von Blazor Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="c3aa5-372"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
