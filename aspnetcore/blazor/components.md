---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: 438b3802087e2ac3df4cbe69a700b878c1cbbf63
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037419"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="6ebfc-103">Erstellen und Verwenden von ASP.net Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="6ebfc-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6ebfc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6ebfc-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ebfc-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6ebfc-106">Blazor-apps werden mithilfe von *Komponenten*erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="6ebfc-107">Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="6ebfc-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="6ebfc-109">Komponenten sind flexibel und leicht zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="6ebfc-110">Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="6ebfc-111">Komponenten Klassen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-111">Component classes</span></span>

<span data-ttu-id="6ebfc-112">Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="6ebfc-113">Eine Komponente in blazor wird formal als *Razor-Komponente*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="6ebfc-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="6ebfc-115">Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="6ebfc-116">Die Benutzeroberfläche für eine Komponente wird mit HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="6ebfc-117">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="6ebfc-118">Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="6ebfc-119">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="6ebfc-120">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="6ebfc-121">Im Block "`@code`" wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden zur Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="6ebfc-122">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebfc-123">In früheren Vorschauen von ASP.net Core 3,0 wurden `@functions`-Blöcke für den gleichen Zweck wie `@code`-Blöcke in Razor-Komponenten verwendet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="6ebfc-124">`@functions`-Blöcke funktionieren weiterhin in Razor-Komponenten, aber es wird empfohlen, den Block "`@code`" in ASP.net Core 3,0 Preview 6 oder höher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="6ebfc-125">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden, die mit `@` beginnen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="6ebfc-126">Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="6ebfc-127">Im folgenden Beispiel wird ausgewertet und gerendert:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="6ebfc-128">`_headingFontStyle` zum CSS-Eigenschafts Wert für `font-style`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="6ebfc-129">`_headingText` auf den Inhalt des `<h1>`-Elements.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="6ebfc-130">Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="6ebfc-131">Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen am Dokumentobjektmodell des Browsers (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="6ebfc-132">Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="6ebfc-133">Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="6ebfc-134">Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="6ebfc-135">Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports. Razor* -Datei der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="6ebfc-136">Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `WebApplication` ist:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="6ebfc-137">Integrieren von Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="6ebfc-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="6ebfc-138">Verwenden Sie Komponenten mit vorhandenen Razor Pages und MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="6ebfc-139">Es ist nicht erforderlich, vorhandene Seiten oder Sichten für die Verwendung von Razor-Komponenten neu zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="6ebfc-140">Wenn die Seite oder die Ansicht gerendert wird, werden die Komponenten gleichzeitig vorab in eine Vorabversion übernommen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="6ebfc-141">Verwenden Sie die HTML-Hilfsmethode `RenderComponentAsync<TComponent>`, um eine Komponente aus einer Seite oder Sicht zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="6ebfc-142">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="6ebfc-143">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="6ebfc-144">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="6ebfc-145">Weitere Informationen zur Art und Weise, wie Komponenten gerendert und der Komponenten Status in blazor-Server-apps verwaltet wird, finden Sie im <xref:blazor/hosting-models>-Artikel.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-145">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="6ebfc-146">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-146">Use components</span></span>

<span data-ttu-id="6ebfc-147">Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="6ebfc-148">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="6ebfc-149">Bei der Attribut Bindung wird Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-149">Attribute binding is case sensitive.</span></span> <span data-ttu-id="6ebfc-150">Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-150">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="6ebfc-151">Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent`-Instanz:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="6ebfc-152">*Komponenten/headingcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="6ebfc-153">Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-153">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="6ebfc-154">Durch das Hinzufügen einer `@using`-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-154">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="6ebfc-155">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-155">Component parameters</span></span>

<span data-ttu-id="6ebfc-156">Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-156">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="6ebfc-157">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-157">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="6ebfc-158">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="6ebfc-159">Im folgenden Beispiel legt der `ParentComponent` den Wert der Eigenschaft `Title` der `ChildComponent` fest.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-159">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="6ebfc-160">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-160">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="6ebfc-161">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="6ebfc-161">Child content</span></span>

<span data-ttu-id="6ebfc-162">Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-162">Components can set the content of another component.</span></span> <span data-ttu-id="6ebfc-163">Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-163">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="6ebfc-164">Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment` darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-164">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="6ebfc-165">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, in dem der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-165">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="6ebfc-166">Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body` des Bootstrap-Panels gerendert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-166">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="6ebfc-167">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="6ebfc-168">Die Eigenschaft, die den `RenderFragment`-Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="6ebfc-169">Der folgende `ParentComponent` kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags platziert wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-169">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="6ebfc-170">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-170">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="6ebfc-171">Attribut-Verteilung und beliebige Parameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-171">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="6ebfc-172">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-172">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="6ebfc-173">Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mit der [@attributes-Razor-](xref:mvc/views/razor#attributes) Direktive gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-173">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="6ebfc-174">Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-174">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="6ebfc-175">Beispielsweise kann es mühsam sein, Attribute separat für eine `<input>` zu definieren, die viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-175">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="6ebfc-176">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attribut-splatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-176">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
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

<span data-ttu-id="6ebfc-177">Der Parametertyp muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichen folgen Schlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-177">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="6ebfc-178">Die Verwendung von `IReadOnlyDictionary<string, object>` ist auch in diesem Szenario eine Option.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-178">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="6ebfc-179">Die gerenderten `<input>`-Elemente mit beiden Ansätzen sind identisch:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-179">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="6ebfc-180">Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter mithilfe des `[Parameter]`-Attributs, wobei die Eigenschaft `CaptureUnmatchedValues` auf `true` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-180">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="6ebfc-181">Die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` ermöglicht dem Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-181">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="6ebfc-182">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues` definieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-182">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="6ebfc-183">Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichen folgen Schlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-183">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="6ebfc-184">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-184">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="6ebfc-185">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-185">Data binding</span></span>

<span data-ttu-id="6ebfc-186">Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [@bind-](xref:mvc/views/razor#bind) Attribut.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-186">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="6ebfc-187">Im folgenden Beispiel wird das Feld `_italicsCheck` an den aktivierten Zustand des Kontrollkästchens gebunden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-187">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="6ebfc-188">Wenn das Kontrollkästchen aktiviert und deaktiviert ist, wird der Wert der Eigenschaft auf `true` bzw. `false` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-188">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="6ebfc-189">Das Kontrollkästchen wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-189">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="6ebfc-190">Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen normalerweise sofort in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="6ebfc-190">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="6ebfc-191">Die Verwendung von `@bind` mit einer `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-191">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="6ebfc-192">Wenn die Komponente gerendert wird, wird der `value` des Eingabe Elements von der Eigenschaft `CurrentValue` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-192">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="6ebfc-193">Wenn der Benutzer in das Textfeld eingibt, wird das `onchange`-Ereignis ausgelöst, und die Eigenschaft `CurrentValue` wird auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-193">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="6ebfc-194">In Wirklichkeit ist die Codegenerierung etwas komplexer, da `@bind` einige Fälle behandelt, in denen Typkonvertierungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-194">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="6ebfc-195">Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-195">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="6ebfc-196">Zusätzlich zur Behandlung von `onchange`-Ereignissen mit `@bind`-Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [@bind-value](xref:mvc/views/razor#bind) -Attribut mit einem `event`-Parameter ([@bind-value:event](xref:mvc/views/razor#bind)) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-196">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="6ebfc-197">Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-197">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="6ebfc-198">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-198">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="6ebfc-199">**Nicht zu erteilbare Werte**</span><span class="sxs-lookup"><span data-stu-id="6ebfc-199">**Unparsable values**</span></span>

<span data-ttu-id="6ebfc-200">Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-200">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="6ebfc-201">Betrachten Sie das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-201">Consider the following scenario:</span></span>

* <span data-ttu-id="6ebfc-202">Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-202">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="6ebfc-203">Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-203">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="6ebfc-204">Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-204">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="6ebfc-205">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-205">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="6ebfc-206">Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-206">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="6ebfc-207">Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-207">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="6ebfc-208">Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-208">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="6ebfc-209">Wenn das `oninput`-Ereignis mit einem @no__t -1-gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.`-Zeichen eingegeben hat.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-209">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="6ebfc-210">Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-210">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="6ebfc-211">Es gibt Szenarios, in denen die Wiederherstellung des Werts für das `oninput`-Ereignis nicht ideal ist, z. b. wenn der Benutzer berechtigt sein sollte, einen nicht zu testbaren `<input>`-Wert zu löschen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-211">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="6ebfc-212">Zu Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-212">Alternatives include:</span></span>

* <span data-ttu-id="6ebfc-213">Verwenden Sie das Ereignis "`oninput`" nicht.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-213">Don't use the `oninput` event.</span></span> <span data-ttu-id="6ebfc-214">Verwenden Sie das Standard Ereignis `onchange` (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-214">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="6ebfc-215">Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie benutzerdefinierte Logik zur Behandlung Ungültiger</span><span class="sxs-lookup"><span data-stu-id="6ebfc-215">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="6ebfc-216">Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-216">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="6ebfc-217">Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-217">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="6ebfc-218">Komponenten Validierungs Komponenten:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-218">Form validation components:</span></span>
  * <span data-ttu-id="6ebfc-219">Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler für den zugeordneten `EditContext` zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-219">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="6ebfc-220">Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-220">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="6ebfc-221">**Globalisierung**</span><span class="sxs-lookup"><span data-stu-id="6ebfc-221">**Globalization**</span></span>

<span data-ttu-id="6ebfc-222">`@bind`-Werte werden für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-222">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="6ebfc-223">Der Zugriff auf die aktuelle Kultur ist über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft möglich.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-223">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="6ebfc-224">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-224">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="6ebfc-225">Die vorangehenden Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-225">The preceding field types:</span></span>

* <span data-ttu-id="6ebfc-226">Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-226">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="6ebfc-227">Darf keinen frei Form Text enthalten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-227">Can't contain free-form text.</span></span>
* <span data-ttu-id="6ebfc-228">Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-228">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="6ebfc-229">Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-229">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="6ebfc-230">`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName> zum Auswerten und Formatieren eines Werts bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-230">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="6ebfc-231">Die Angabe einer Kultur ist nicht empfehlenswert, wenn die Feldtypen `date` und `number` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-231">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="6ebfc-232">`date` und `number` verfügen über eine integrierte blazor-Unterstützung, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-232">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="6ebfc-233">Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-233">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="6ebfc-234">**Format Zeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="6ebfc-234">**Format strings**</span></span>

<span data-ttu-id="6ebfc-235">Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen, die [@bind:format](xref:mvc/views/razor#bind)verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-235">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="6ebfc-236">Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-236">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="6ebfc-237">Im vorangehenden Code ist der Feldtyp (`type`) des `<input>`-Elements standardmäßig `text`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-237">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="6ebfc-238">`@bind:format` wird für das Binden der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-238">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="6ebfc-239"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6ebfc-239"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="6ebfc-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6ebfc-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="6ebfc-241">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-241">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="6ebfc-242">Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange`-Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-242">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="6ebfc-243">Es wird nicht empfohlen, ein Format für den Feldtyp "`date`" anzugeben, da blazor über integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-243">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="6ebfc-244">**Komponenten Parameter**</span><span class="sxs-lookup"><span data-stu-id="6ebfc-244">**Component parameters**</span></span>

<span data-ttu-id="6ebfc-245">Die Bindung erkennt Komponenten Parameter, wobei `@bind-{property}` einen Eigenschafts Wert auf Komponenten binden kann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-245">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="6ebfc-246">Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year`-Komponenten Parameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-246">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="6ebfc-247">`EventCallback<T>` wird im Abschnitt " [EventCallback](#eventcallback) " erläutert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-247">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="6ebfc-248">Die folgende übergeordnete Komponente verwendet `ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten an den `Year`-Parameter in der untergeordneten Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-248">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="6ebfc-249">Beim Laden der `ParentComponent` wird das folgende Markup erzeugt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-249">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="6ebfc-250">Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent` geändert wird, wird die `Year`-Eigenschaft des `ChildComponent` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-250">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="6ebfc-251">Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-251">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="6ebfc-252">Der `Year`-Parameter ist bindbar, da er ein Begleit `YearChanged`-Ereignis aufweist, das mit dem Typ des `Year`-Parameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-252">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="6ebfc-253">Gemäß der Konvention entspricht `<ChildComponent @bind-Year="ParentYear" />` im Wesentlichen dem Schreiben:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-253">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="6ebfc-254">Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event`-Attributs an einen entsprechenden Ereignishandler gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-254">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="6ebfc-255">Beispielsweise kann die-Eigenschaft `MyProp` mithilfe der folgenden zwei Attribute an `MyEventHandler` gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-255">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="6ebfc-256">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-256">Event handling</span></span>

<span data-ttu-id="6ebfc-257">Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-257">Razor components provide event handling features.</span></span> <span data-ttu-id="6ebfc-258">Für ein HTML-Element Attribut mit dem Namen `on{event}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-258">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="6ebfc-259">Der Name des Attributs wird immer [@on {Event}](xref:mvc/views/razor#onevent)formatiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-259">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="6ebfc-260">Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-260">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="6ebfc-261">Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-261">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="6ebfc-262">Ereignishandler können auch asynchron sein und einen <xref:System.Threading.Tasks.Task> zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-262">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="6ebfc-263">Es ist nicht erforderlich, `StateHasChanged()` manuell aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-263">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="6ebfc-264">Ausnahmen werden protokolliert, wenn Sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-264">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="6ebfc-265">Im folgenden Beispiel wird "`UpdateHeading`" asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-265">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="6ebfc-266">Ereignis Argument Typen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-266">Event argument types</span></span>

<span data-ttu-id="6ebfc-267">Für einige Ereignisse sind Ereignis Argument Typen zulässig.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-267">For some events, event argument types are permitted.</span></span> <span data-ttu-id="6ebfc-268">Wenn der Zugriff auf einen dieser Ereignis Typen nicht erforderlich ist, ist er im Methoden Aufrufvorgang nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-268">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="6ebfc-269">Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-269">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="6ebfc-270">event</span><span class="sxs-lookup"><span data-stu-id="6ebfc-270">Event</span></span> | <span data-ttu-id="6ebfc-271">Klasse</span><span class="sxs-lookup"><span data-stu-id="6ebfc-271">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="6ebfc-272">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="6ebfc-272">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="6ebfc-273">Hinein</span><span class="sxs-lookup"><span data-stu-id="6ebfc-273">Drag</span></span>             | <span data-ttu-id="6ebfc-274">`DragEventArgs` &ndash; `DataTransfer` und `DataTransferItem` halten gezogene Elementdaten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-274">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="6ebfc-275">Fehler</span><span class="sxs-lookup"><span data-stu-id="6ebfc-275">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="6ebfc-276">Fokus</span><span class="sxs-lookup"><span data-stu-id="6ebfc-276">Focus</span></span>            | <span data-ttu-id="6ebfc-277">`FocusEventArgs` &ndash; umfasst keine Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-277">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="6ebfc-278">`<input>` -Änderung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-278">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="6ebfc-279">Tastatur</span><span class="sxs-lookup"><span data-stu-id="6ebfc-279">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="6ebfc-280">Maus</span><span class="sxs-lookup"><span data-stu-id="6ebfc-280">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="6ebfc-281">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="6ebfc-281">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="6ebfc-282">Mausrad</span><span class="sxs-lookup"><span data-stu-id="6ebfc-282">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="6ebfc-283">Status</span><span class="sxs-lookup"><span data-stu-id="6ebfc-283">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="6ebfc-284">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="6ebfc-284">Touch</span></span>            | <span data-ttu-id="6ebfc-285">`TouchEventArgs` &ndash; `TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-285">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="6ebfc-286">Informationen zu den Eigenschaften und dem Ereignis Behandlungs Verhalten der Ereignisse in der vorangehenden Tabelle finden Sie unter [EventArgs classes in the Reference Source (ASPNET/aspnetcore Release/3.0 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-286">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="6ebfc-287">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="6ebfc-287">Lambda expressions</span></span>

<span data-ttu-id="6ebfc-288">Lambda-Ausdrücke können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-288">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="6ebfc-289">Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-289">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="6ebfc-290">Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufruft, wobei ein Ereignis Argument (`MouseEventArgs`) und dessen Schaltflächen Nummer (`buttonNumber`) angezeigt werden, wenn es in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-290">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="6ebfc-291">Verwenden Sie die Schleifenvariable (`i`) **nicht** in einer `for`-Schleife direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-291">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="6ebfc-292">Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch der Wert von `i` in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-292">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="6ebfc-293">Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-293">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="6ebfc-294">EventCallback</span><span class="sxs-lookup"><span data-stu-id="6ebfc-294">EventCallback</span></span>

<span data-ttu-id="6ebfc-295">Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt @ no__t-0z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-295">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="6ebfc-296">Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie einen `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-296">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="6ebfc-297">Eine übergeordnete Komponente kann dem `EventCallback` einer untergeordneten Komponente eine Rückruf Methode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-297">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="6ebfc-298">Der `ChildComponent` in der Beispiel-App veranschaulicht, wie der `onclick`-Handler einer Schaltfläche so eingerichtet wird, dass er einen `EventCallback`-Delegaten aus dem `ParentComponent` des Beispiels empfängt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-298">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="6ebfc-299">Der `EventCallback` wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-299">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="6ebfc-300">Mit dem `ParentComponent` wird die `EventCallback<T>` des untergeordneten Elements auf die `ShowMessage`-Methode festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-300">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="6ebfc-301">Wenn die Schaltfläche im `ChildComponent` ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-301">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="6ebfc-302">Die `ShowMessage`-Methode des `ParentComponent` heißt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-302">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="6ebfc-303">`messageText` wird aktualisiert und in der `ParentComponent` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-303">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="6ebfc-304">Ein `StateHasChanged`-Aufrufs ist in der-Methode des Rückrufs (`ShowMessage`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-304">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="6ebfc-305">`StateHasChanged` wird automatisch aufgerufen, um den `ParentComponent` zu übertragen, genauso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern, die innerhalb des untergeordneten Elements ausgeführt werden, auslöst.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-305">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="6ebfc-306">`EventCallback` und `EventCallback<T>` erlauben asynchrone Delegaten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-306">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="6ebfc-307">`EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-307">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="6ebfc-308">`EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-308">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="6ebfc-309">Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-309">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="6ebfc-310">Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-310">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="6ebfc-311">Bevorzugen Sie die stark typisierte `EventCallback<T>` über `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-311">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="6ebfc-312">`EventCallback<T>` bietet Benutzern der Komponente ein besseres Fehler Feedback.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-312">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="6ebfc-313">Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-313">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="6ebfc-314">Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-314">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="6ebfc-315">Verkettete Bindung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-315">Chained bind</span></span>

<span data-ttu-id="6ebfc-316">Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-316">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="6ebfc-317">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-317">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="6ebfc-318">Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-318">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="6ebfc-319">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-319">The event handler and value must be specified separately.</span></span> <span data-ttu-id="6ebfc-320">Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-320">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="6ebfc-321">Die folgende `PasswordField`-Komponente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6ebfc-321">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="6ebfc-322">Legt den Wert eines `<input>`-Elements auf eine Eigenschaft `Password` fest.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-322">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="6ebfc-323">Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-323">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="6ebfc-324">Die Komponente "`PasswordField`" wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-324">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="6ebfc-325">Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-325">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="6ebfc-326">Erstellen Sie ein Unterstützungs Feld für `Password` (`password` im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-326">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="6ebfc-327">Führen Sie die Überprüfungen oder Trap Fehler im `Password`-Setter aus.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-327">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="6ebfc-328">Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-328">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="6ebfc-329">Verweise auf Komponenten erfassen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-329">Capture references to components</span></span>

<span data-ttu-id="6ebfc-330">Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, z. b. `Show` oder `Reset`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-330">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="6ebfc-331">So erfassen Sie einen Komponenten Verweis:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-331">To capture a component reference:</span></span>

* <span data-ttu-id="6ebfc-332">Fügen Sie der untergeordneten Komponente ein [@ref-](xref:mvc/views/razor#ref) Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-332">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="6ebfc-333">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-333">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="6ebfc-334">Wenn die Komponente gerendert wird, wird das Feld `loginDialog` mit der untergeordneten Komponenteninstanz `MyLoginDialog` aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-334">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="6ebfc-335">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-335">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ebfc-336">Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-336">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="6ebfc-337">Bis zu diesem Punkt sind keine Verweise mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-337">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="6ebfc-338">Um Komponenten Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, verwenden Sie die Methoden `OnAfterRenderAsync` oder `OnAfterRender`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-338">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="6ebfc-339">Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion</span><span class="sxs-lookup"><span data-stu-id="6ebfc-339">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="6ebfc-340">Komponenten Verweise werden nicht an den JavaScript-Code @ no__t-0Die werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-340">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebfc-341">Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-341">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="6ebfc-342">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-342">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="6ebfc-343">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-343">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="6ebfc-344">Komponenten Methoden extern aufrufen, um den Status zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="6ebfc-344">Invoke component methods externally to update state</span></span>

<span data-ttu-id="6ebfc-345">Blazor verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-345">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="6ebfc-346">Die Lebenszyklus Methoden einer Komponente und alle Ereignis Rückrufe, die von blazor ausgelöst werden, werden auf diesem `SynchronizationContext` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-346">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="6ebfc-347">Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den `SynchronizationContext` von blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-347">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="6ebfc-348">Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-348">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="6ebfc-349">Verwendung des `NotifierService` zum Aktualisieren einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-349">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="6ebfc-350">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb der `SynchronizationContext` von blazor auf.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-350">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="6ebfc-351">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-351">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="6ebfc-352">Verwenden Sie \@key, um die Beibehaltung von Elementen und Komponenten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-352">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="6ebfc-353">Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss der diffalling-Algorithmus von blazor entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-353">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="6ebfc-354">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-354">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="6ebfc-355">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-355">Consider the following example:</span></span>

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

<span data-ttu-id="6ebfc-356">Der Inhalt der Sammlung "`People`" kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-356">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="6ebfc-357">Wenn die Komponente erneut ausgeführt wird, kann sich die `<DetailsEditor>`-Komponente ändern, um unterschiedliche `Details`-Parameterwerte zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-357">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="6ebfc-358">Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-358">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="6ebfc-359">In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-359">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="6ebfc-360">Der Zuweisungs Prozess kann mit dem `@key`-direktivenattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-360">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="6ebfc-361">`@key` bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-361">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="6ebfc-362">Wenn sich die `People`-Auflistung ändert, behält der diff-Algorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-362">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="6ebfc-363">Wenn eine `Person` aus der Liste `People` gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz aus der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-363">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="6ebfc-364">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-364">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6ebfc-365">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an dieser entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-365">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="6ebfc-366">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-366">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6ebfc-367">Wenn `Person`-Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>`-Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-367">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="6ebfc-368">In einigen Szenarien minimiert die Verwendung von `@key` die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-368">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ebfc-369">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-369">Keys are local to each container element or component.</span></span> <span data-ttu-id="6ebfc-370">Schlüssel werden nicht global über das Dokument hinweg verglichen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-370">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="6ebfc-371">Verwendung \@key</span><span class="sxs-lookup"><span data-stu-id="6ebfc-371">When to use \@key</span></span>

<span data-ttu-id="6ebfc-372">In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. b. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um die `@key` zu definieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-372">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="6ebfc-373">Sie können auch `@key` verwenden, um zu verhindern, dass blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-373">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="6ebfc-374">Wenn `@currentPerson` geändert wird, erzwingt die Attribut Anweisung `@key`, dass blazor den gesamten `<div>` und seine Nachfolger verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-374">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="6ebfc-375">Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn sich `@currentPerson` ändert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-375">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="6ebfc-376">Wenn \@key nicht verwendet werden soll</span><span class="sxs-lookup"><span data-stu-id="6ebfc-376">When not to use \@key</span></span>

<span data-ttu-id="6ebfc-377">Bei der Durchführung von `@key` entstehen Kosten für die Leistung.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-377">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="6ebfc-378">Die Leistungskosten sind nicht groß, geben jedoch nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten von der APP profitiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-378">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="6ebfc-379">Auch wenn `@key` nicht verwendet wird, behält blazor das untergeordnete Element und die Komponenten Instanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-379">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="6ebfc-380">Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-380">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="6ebfc-381">Die Werte, die für \@key verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-381">What values to use for \@key</span></span>

<span data-ttu-id="6ebfc-382">Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key` bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-382">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="6ebfc-383">Modell Objektinstanzen (z. b. eine `Person`-Instanz wie im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-383">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="6ebfc-384">Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-384">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="6ebfc-385">Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string` oder `Guid`).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-385">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="6ebfc-386">Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht aufeinander stoßen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-386">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="6ebfc-387">Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-387">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="6ebfc-388">Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-388">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="6ebfc-389">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="6ebfc-389">Lifecycle methods</span></span>

<span data-ttu-id="6ebfc-390">`OnInitializedAsync` und `OnInitialized` führen Code aus, um die Komponente zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-390">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="6ebfc-391">Verwenden Sie zum Ausführen eines asynchronen Vorgangs `OnInitializedAsync` und das Schlüsselwort `await` für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-391">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="6ebfc-392">Verwenden Sie für einen synchronen Vorgang `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-392">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="6ebfc-393">`OnParametersSetAsync` und `OnParametersSet` werden aufgerufen, wenn eine Komponente Parameter von ihrem übergeordneten Element empfangen hat und die Werte Eigenschaften zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-393">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="6ebfc-394">Diese Methoden werden nach der Initialisierung der Komponente und jedes Mal, wenn die Komponente gerendert wird, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-394">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="6ebfc-395">`OnAfterRenderAsync` und `OnAfterRender` werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-395">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="6ebfc-396">Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-396">Element and component references are populated at this point.</span></span> <span data-ttu-id="6ebfc-397">Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom</span><span class="sxs-lookup"><span data-stu-id="6ebfc-397">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="6ebfc-398">`OnAfterRender` *wird nicht aufgerufen, wenn die vorab Erstellung auf dem Server erfolgt.*</span><span class="sxs-lookup"><span data-stu-id="6ebfc-398">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="6ebfc-399">Der Parameter "`firstRender`" für `OnAfterRenderAsync` und `OnAfterRender` lautet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-399">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="6ebfc-400">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-400">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="6ebfc-401">Stellt sicher, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-401">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="6ebfc-402">Unvollständige Async-Aktionen bei Rendering behandeln</span><span class="sxs-lookup"><span data-stu-id="6ebfc-402">Handle incomplete async actions at render</span></span>

<span data-ttu-id="6ebfc-403">Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert</span><span class="sxs-lookup"><span data-stu-id="6ebfc-403">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="6ebfc-404">Objekte können `null` oder nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-404">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="6ebfc-405">Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert</span><span class="sxs-lookup"><span data-stu-id="6ebfc-405">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="6ebfc-406">Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht) dar, während Objekte `null` sind.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-406">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="6ebfc-407">In der `FetchData`-Komponente der blazor-Vorlagen wird `OnInitializedAsync` für den asynchronen Empfang von Vorhersagedaten (`forecasts`) überschrieben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-407">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="6ebfc-408">Wenn `forecasts` `null` ist, wird dem Benutzer eine Meldung zum Laden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-408">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="6ebfc-409">Nachdem der von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-409">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="6ebfc-410">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-410">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="6ebfc-411">Ausführen von Code vor dem Festlegen von Parametern</span><span class="sxs-lookup"><span data-stu-id="6ebfc-411">Execute code before parameters are set</span></span>

<span data-ttu-id="6ebfc-412">`SetParameters` kann überschrieben werden, um Code vor dem Festlegen von Parametern auszuführen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-412">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="6ebfc-413">Wenn `base.SetParameters` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-413">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="6ebfc-414">Beispielsweise müssen die eingehenden Parameter nicht den Eigenschaften der Klasse zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-414">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="6ebfc-415">Aktualisierung der Benutzeroberfläche unterdrücken</span><span class="sxs-lookup"><span data-stu-id="6ebfc-415">Suppress refreshing of the UI</span></span>

<span data-ttu-id="6ebfc-416">`ShouldRender` kann überschrieben werden, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-416">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="6ebfc-417">Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-417">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="6ebfc-418">Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-418">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="6ebfc-419">Komponenten Beseitigung mit iverwerf</span><span class="sxs-lookup"><span data-stu-id="6ebfc-419">Component disposal with IDisposable</span></span>

<span data-ttu-id="6ebfc-420">Wenn eine Komponente <xref:System.IDisposable> implementiert, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-420">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="6ebfc-421">In der folgenden Komponente werden `@implements IDisposable` und die `Dispose`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-421">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="6ebfc-422">Routing</span><span class="sxs-lookup"><span data-stu-id="6ebfc-422">Routing</span></span>

<span data-ttu-id="6ebfc-423">Das Routing in blazor wird durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der APP erreicht.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-423">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="6ebfc-424">Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse einen <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, der die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-424">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="6ebfc-425">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-425">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="6ebfc-426">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-426">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6ebfc-427">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-427">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="6ebfc-428">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-428">Route parameters</span></span>

<span data-ttu-id="6ebfc-429">Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-429">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="6ebfc-430">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-430">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="6ebfc-431">Weiterleitungs *Parameter Komponente*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-431">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="6ebfc-432">Optionale Parameter werden nicht unterstützt. Daher werden im obigen Beispiel zwei `@page`-Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-432">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="6ebfc-433">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-433">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6ebfc-434">Die zweite `@page`-Direktive übernimmt den `{text}`-Routen Parameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-434">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="6ebfc-435">Basisklassen Vererbung für eine "Code Behind"-Funktion</span><span class="sxs-lookup"><span data-stu-id="6ebfc-435">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="6ebfc-436">Komponenten Dateien mischen HTML-Markup C# und Verarbeitungs Code in derselben Datei.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-436">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="6ebfc-437">Die `@inherits`-Direktive kann verwendet werden, um blazor-apps eine "Code Behind"-Funktion bereitzustellen, die das Komponenten Markup von der Code Verarbeitung trennt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-437">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="6ebfc-438">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) zeigt, wie eine Komponente eine Basisklasse, `BlazorRocksBase`, erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-438">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="6ebfc-439">*Pages/blazorrocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-439">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="6ebfc-440">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-440">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="6ebfc-441">Die Basisklasse sollte von `ComponentBase` abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-441">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="6ebfc-442">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-442">Import components</span></span>

<span data-ttu-id="6ebfc-443">Der Namespace einer mit Razor erstellten Komponente basiert auf (in Prioritäts Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="6ebfc-443">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="6ebfc-444">[@namespace-](xref:mvc/views/razor#namespace) Bezeichnung in Razor-Datei ( *. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-444">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="6ebfc-445">Die `RootNamespace` des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-445">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="6ebfc-446">Der Projektname, der aus dem Dateinamen der Projektdatei ( *. csproj*) und dem Pfad des Projekt Stamms zur Komponente stammt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-446">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="6ebfc-447">Das Framework löst z. b. *{Project root}/pages/index.Razor* (*blazorsample. csproj*) in den Namespace `BlazorSample.Pages` aus.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-447">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="6ebfc-448">Komponenten folgen C# den namens Bindungs Regeln.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-448">Components follow C# name binding rules.</span></span> <span data-ttu-id="6ebfc-449">Für die Komponente "`Index`" in diesem Beispiel sind die Komponenten im Bereich alle Komponenten:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-449">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="6ebfc-450">Im gleichen Ordner, *Seiten*.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-450">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="6ebfc-451">Die Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-451">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="6ebfc-452">Komponenten, die in einem anderen Namespace definiert sind, werden mit der [@using-](xref:mvc/views/razor#using) Direktive von Razor in den Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="6ebfc-452">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="6ebfc-453">Wenn eine andere Komponente (`NavMenu.razor`) im Ordner " *blazorsample/Shared/* " vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using`-Anweisung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-453">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="6ebfc-454">Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, die die [@using-](xref:mvc/views/razor#using) Direktive nicht benötigen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-454">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="6ebfc-455">Die `global::`-Qualifikation wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-455">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="6ebfc-456">Das Importieren von Komponenten mit Alias `using`-Anweisungen (z. b. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-456">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="6ebfc-457">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-457">Partially qualified names aren't supported.</span></span> <span data-ttu-id="6ebfc-458">Beispielsweise wird das Hinzufügen von `@using BlazorSample` und das verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-458">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="6ebfc-459">Attribute für bedingtes HTML-Element</span><span class="sxs-lookup"><span data-stu-id="6ebfc-459">Conditional HTML element attributes</span></span>

<span data-ttu-id="6ebfc-460">HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-460">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="6ebfc-461">Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-461">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="6ebfc-462">Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-462">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="6ebfc-463">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-463">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="6ebfc-464">Wenn `IsCompleted` `true` ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-464">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="6ebfc-465">Wenn `IsCompleted` `false` ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-465">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="6ebfc-466">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-466">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="6ebfc-467">Einige HTML-Attribute, wie z. b. " [Aria-Pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)", funktionieren nicht ordnungsgemäß, wenn der .NET-Typ ein `bool` ist.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-467">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="6ebfc-468">Verwenden Sie in diesen Fällen einen `string`-Typ anstelle eines `bool`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-468">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="6ebfc-469">Unformatierte HTML</span><span class="sxs-lookup"><span data-stu-id="6ebfc-469">Raw HTML</span></span>

<span data-ttu-id="6ebfc-470">Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-470">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="6ebfc-471">Um unformatierten HTML-Code zu erstellen, packen Sie den HTML-Inhalt in einen `MarkupString`-Wert</span><span class="sxs-lookup"><span data-stu-id="6ebfc-471">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="6ebfc-472">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-472">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="6ebfc-473">Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-473">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="6ebfc-474">Im folgenden Beispiel wird gezeigt, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-474">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="6ebfc-475">Komponenten mit Vorlagen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-475">Templated components</span></span>

<span data-ttu-id="6ebfc-476">Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-476">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="6ebfc-477">Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-477">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="6ebfc-478">Einige Beispiele sind:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-478">A couple of examples include:</span></span>

* <span data-ttu-id="6ebfc-479">Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-479">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="6ebfc-480">Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-480">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="6ebfc-481">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-481">Template parameters</span></span>

<span data-ttu-id="6ebfc-482">Eine auf Vorlagen basierende Komponente wird definiert, indem mindestens ein Komponenten Parameter vom Typ "`RenderFragment`" oder "`RenderFragment<T>`" angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-482">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="6ebfc-483">Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-483">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="6ebfc-484">`RenderFragment<T>` nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-484">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="6ebfc-485">`TableTemplate`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-485">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="6ebfc-486">Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können die Vorlagen Parameter mit untergeordneten Elementen angegeben werden, die den Namen der Parameter (`TableHeader` und `RowTemplate` im folgenden Beispiel) entsprechen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-486">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="6ebfc-487">Vorlagen Kontext Parameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-487">Template context parameters</span></span>

<span data-ttu-id="6ebfc-488">Komponenten Argumente vom Typ "`RenderFragment<T>`", die als Elemente übergeben wurden, haben einen impliziten Parameter mit dem Namen `context` (z. b. aus dem vorangehenden Codebeispiel `@context.PetId`), Sie können jedoch den Parameternamen mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-488">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="6ebfc-489">Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate`-Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-489">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="6ebfc-490">Alternativ können Sie das `Context`-Attribut für das Component-Element angeben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-490">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="6ebfc-491">Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagen Parameter.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-491">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="6ebfc-492">Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element).</span><span class="sxs-lookup"><span data-stu-id="6ebfc-492">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="6ebfc-493">Im folgenden Beispiel wird das `Context`-Attribut für das `TableTemplate`-Element angezeigt und gilt für alle Vorlagen Parameter:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-493">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="6ebfc-494">Generisch typisierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-494">Generic-typed components</span></span>

<span data-ttu-id="6ebfc-495">Auf Vorlagen basierende Komponenten werden oft generisch typisiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-495">Templated components are often generically typed.</span></span> <span data-ttu-id="6ebfc-496">Beispielsweise kann eine generische `ListViewTemplate`-Komponente verwendet werden, um `IEnumerable<T>`-Werte zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-496">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="6ebfc-497">Zum Definieren einer generischen Komponente verwenden Sie die [@typeparam-](xref:mvc/views/razor#typeparam) Direktive, um Typparameter anzugeben:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-497">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="6ebfc-498">Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-498">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="6ebfc-499">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-499">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="6ebfc-500">Im folgenden Beispiel gibt `TItem="Pet"` den Typ an:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-500">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="6ebfc-501">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="6ebfc-501">Cascading values and parameters</span></span>

<span data-ttu-id="6ebfc-502">In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-502">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="6ebfc-503">Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-503">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="6ebfc-504">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-504">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="6ebfc-505">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="6ebfc-505">Theme example</span></span>

<span data-ttu-id="6ebfc-506">Im folgenden Beispiel der Beispiel-App gibt die `ThemeInfo`-Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-506">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="6ebfc-507">*Uithmeclasses/themerfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-507">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="6ebfc-508">Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-508">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="6ebfc-509">Die Komponente `CascadingValue` umschließt eine Teilstruktur der Komponenten Hierarchie und stellt allen Komponenten innerhalb dieser Teilstruktur einen einzelnen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-509">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="6ebfc-510">Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-510">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="6ebfc-511">`ButtonClass` wird in der Layoutkomponente der Wert `btn-success` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-511">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="6ebfc-512">Jede Nachfolger Komponente kann diese Eigenschaft über das Cascading-Objekt `ThemeInfo` nutzen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-512">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="6ebfc-513">`CascadingValuesParametersLayout`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-513">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="6ebfc-514">Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-514">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="6ebfc-515">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-515">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="6ebfc-516">In der Beispiel-App bindet die Komponente "`CascadingValuesParametersTheme`" den `ThemeInfo`-kaskadierenden Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-516">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="6ebfc-517">Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-517">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="6ebfc-518">`CascadingValuesParametersTheme`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-518">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="6ebfc-519">Wenn Sie mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren möchten, stellen Sie eine eindeutige `Name`-Zeichenfolge für jede `CascadingValue`-Komponente und deren zugehörige `CascadingParameter` bereit.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-519">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="6ebfc-520">Im folgenden Beispiel kaskadieren zwei `CascadingValue`-Komponenten verschiedene Instanzen von `MyCascadingType` nach dem Namen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-520">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="6ebfc-521">In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-521">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="6ebfc-522">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="6ebfc-522">TabSet example</span></span>

<span data-ttu-id="6ebfc-523">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-523">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="6ebfc-524">Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-524">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="6ebfc-525">Die Beispiel-App verfügt über eine `ITab`-Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-525">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="6ebfc-526">Die Komponente "`CascadingValuesParametersTabSet`" verwendet die Komponente "`TabSet`", die mehrere `Tab`-Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-526">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="6ebfc-527">Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet` übermittelt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-527">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="6ebfc-528">Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-528">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="6ebfc-529">Die `TabSet` muss jedoch immer noch über jede `Tab`-Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code erforderlich ist, kann sich die Komponente "`TabSet`" *als kaskadierenden Wert bereitstellen* , der dann von den untergeordneten `Tab`-Komponenten abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-529">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="6ebfc-530">`TabSet`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-530">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="6ebfc-531">Die untergeordneten `Tab`-Komponenten erfassen den enthaltenden `TabSet` als kaskadierenden Parameter, sodass sich die Komponenten von `Tab` dem `TabSet` und der Koordinate, auf der die Registerkarte aktiv ist, hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-531">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="6ebfc-532">`Tab`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-532">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="6ebfc-533">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-533">Razor templates</span></span>

<span data-ttu-id="6ebfc-534">Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-534">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="6ebfc-535">Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-535">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="6ebfc-536">Im folgenden Beispiel wird veranschaulicht, wie `RenderFragment`-und `RenderFragment<T>`-Werte angegeben und Vorlagen direkt in einer-Komponente dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-536">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="6ebfc-537">Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-537">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="6ebfc-538">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-538">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="6ebfc-539">Manuelle rendertreebuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="6ebfc-539">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="6ebfc-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebfc-541">Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-541">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="6ebfc-542">Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-542">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="6ebfc-543">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-543">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="6ebfc-544">Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-544">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="6ebfc-545">Wenn Sie `RenderTreeBuilder`-Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-545">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="6ebfc-546">Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-546">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="6ebfc-547">Beim Erstellen einer Komponente mit `RenderTreeBuilder`-Methoden werden die Argumente für Sequenznummern hart codiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-547">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="6ebfc-548">**Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="6ebfc-548">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="6ebfc-549">Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-549">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="6ebfc-550">`BuiltContent`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-550">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> <span data-ttu-id="6ebfc-551">! Davor Die Typen in `Microsoft.AspNetCore.Components.RenderTree` ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-551">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="6ebfc-552">Hierbei handelt es sich um interne Details der blazor-Framework-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-552">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="6ebfc-553">Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-553">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="6ebfc-554">Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-554">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="6ebfc-555">Blazor-`.razor`-Dateien werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-555">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="6ebfc-556">Dies ist möglicherweise ein großer Vorteil für `.razor`, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-556">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="6ebfc-557">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-557">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="6ebfc-558">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-558">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="6ebfc-559">Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-559">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="6ebfc-560">Sehen Sie sich die folgende einfache `.razor`-Datei an:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-560">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="6ebfc-561">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-561">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="6ebfc-562">Wenn der Code zum ersten Mal ausgeführt wird, wenn `someFlag` `true` ist, empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-562">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="6ebfc-563">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ebfc-563">Sequence</span></span> | <span data-ttu-id="6ebfc-564">Typ</span><span class="sxs-lookup"><span data-stu-id="6ebfc-564">Type</span></span>      | <span data-ttu-id="6ebfc-565">Daten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-565">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6ebfc-566">0</span><span class="sxs-lookup"><span data-stu-id="6ebfc-566">0</span></span>        | <span data-ttu-id="6ebfc-567">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-567">Text node</span></span> | <span data-ttu-id="6ebfc-568">Erster</span><span class="sxs-lookup"><span data-stu-id="6ebfc-568">First</span></span>  |
| <span data-ttu-id="6ebfc-569">1</span><span class="sxs-lookup"><span data-stu-id="6ebfc-569">1</span></span>        | <span data-ttu-id="6ebfc-570">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-570">Text node</span></span> | <span data-ttu-id="6ebfc-571">Zweimal</span><span class="sxs-lookup"><span data-stu-id="6ebfc-571">Second</span></span> |

<span data-ttu-id="6ebfc-572">Stellen Sie sich vor, dass `someFlag` `false` ist, und dass das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-572">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="6ebfc-573">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-573">This time, the builder receives:</span></span>

| <span data-ttu-id="6ebfc-574">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ebfc-574">Sequence</span></span> | <span data-ttu-id="6ebfc-575">Typ</span><span class="sxs-lookup"><span data-stu-id="6ebfc-575">Type</span></span>       | <span data-ttu-id="6ebfc-576">Daten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-576">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="6ebfc-577">1</span><span class="sxs-lookup"><span data-stu-id="6ebfc-577">1</span></span>        | <span data-ttu-id="6ebfc-578">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-578">Text node</span></span>  | <span data-ttu-id="6ebfc-579">Zweimal</span><span class="sxs-lookup"><span data-stu-id="6ebfc-579">Second</span></span> |

<span data-ttu-id="6ebfc-580">Wenn die Laufzeit einen Diff-Vorgang ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-580">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="6ebfc-581">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-581">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="6ebfc-582">Was schief geht, wenn Sie Programm gesteuert Sequenznummern generieren</span><span class="sxs-lookup"><span data-stu-id="6ebfc-582">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="6ebfc-583">Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-583">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="6ebfc-584">Nun lautet die erste Ausgabe wie folgt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-584">Now, the first output is:</span></span>

| <span data-ttu-id="6ebfc-585">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ebfc-585">Sequence</span></span> | <span data-ttu-id="6ebfc-586">Typ</span><span class="sxs-lookup"><span data-stu-id="6ebfc-586">Type</span></span>      | <span data-ttu-id="6ebfc-587">Daten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-587">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6ebfc-588">0</span><span class="sxs-lookup"><span data-stu-id="6ebfc-588">0</span></span>        | <span data-ttu-id="6ebfc-589">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-589">Text node</span></span> | <span data-ttu-id="6ebfc-590">Erster</span><span class="sxs-lookup"><span data-stu-id="6ebfc-590">First</span></span>  |
| <span data-ttu-id="6ebfc-591">1</span><span class="sxs-lookup"><span data-stu-id="6ebfc-591">1</span></span>        | <span data-ttu-id="6ebfc-592">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-592">Text node</span></span> | <span data-ttu-id="6ebfc-593">Zweimal</span><span class="sxs-lookup"><span data-stu-id="6ebfc-593">Second</span></span> |

<span data-ttu-id="6ebfc-594">Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-594">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="6ebfc-595">`someFlag` ist für das zweite Rendering `false`, und die Ausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-595">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="6ebfc-596">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ebfc-596">Sequence</span></span> | <span data-ttu-id="6ebfc-597">Typ</span><span class="sxs-lookup"><span data-stu-id="6ebfc-597">Type</span></span>      | <span data-ttu-id="6ebfc-598">Daten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-598">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="6ebfc-599">0</span><span class="sxs-lookup"><span data-stu-id="6ebfc-599">0</span></span>        | <span data-ttu-id="6ebfc-600">Textknoten</span><span class="sxs-lookup"><span data-stu-id="6ebfc-600">Text node</span></span> | <span data-ttu-id="6ebfc-601">Zweimal</span><span class="sxs-lookup"><span data-stu-id="6ebfc-601">Second</span></span> |

<span data-ttu-id="6ebfc-602">Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-602">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="6ebfc-603">Ändern Sie den Wert des ersten Text Knotens in `Second`.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-603">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="6ebfc-604">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-604">Remove the second text node.</span></span>

<span data-ttu-id="6ebfc-605">Das Erzeugen der Sequenznummern hat alle nützlichen Informationen übergegangen, an denen die `if/else`-Verzweigungen und-Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-605">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="6ebfc-606">Dies führt **doppelt so lange** wie zuvor zu einem diff.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-606">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="6ebfc-607">Dies ist ein sehr einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-607">This is a trivial example.</span></span> <span data-ttu-id="6ebfc-608">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-608">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="6ebfc-609">Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-609">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="6ebfc-610">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-610">Guidance and conclusions</span></span>

* <span data-ttu-id="6ebfc-611">Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-611">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="6ebfc-612">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-612">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="6ebfc-613">Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder`-Logik.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-613">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="6ebfc-614">Bevorzugen Sie `.razor`-Dateien, und ermöglichen Sie es dem Compiler, die Sequenznummern zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-614">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="6ebfc-615">Wenn Sie keine manuelle `RenderTreeBuilder`-Logik vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile, die `OpenRegion` @ no__t-2 @ no__t-3-aufrufen umschließen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-615">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="6ebfc-616">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-616">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="6ebfc-617">Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-617">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="6ebfc-618">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-618">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="6ebfc-619">Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-619">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="6ebfc-620">Blazor verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-620">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="6ebfc-621">Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und blazor bietet den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die `.razor`-Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-621">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="6ebfc-622">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-622">Localization</span></span>

<span data-ttu-id="6ebfc-623">Blazor-Server-apps werden mithilfe der [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-623">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="6ebfc-624">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-624">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="6ebfc-625">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-625">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="6ebfc-626">Cookies</span><span class="sxs-lookup"><span data-stu-id="6ebfc-626">Cookies</span></span>](#cookies)
* [<span data-ttu-id="6ebfc-627">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="6ebfc-627">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="6ebfc-628">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-628">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="6ebfc-629">Cookies</span><span class="sxs-lookup"><span data-stu-id="6ebfc-629">Cookies</span></span>

<span data-ttu-id="6ebfc-630">Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-630">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="6ebfc-631">Das Cookie wird von der `OnGet`-Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-631">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="6ebfc-632">Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-632">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="6ebfc-633">Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-633">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="6ebfc-634">Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-634">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="6ebfc-635">Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-635">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="6ebfc-636">Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-636">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="6ebfc-637">Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-637">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="6ebfc-638">Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-638">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="6ebfc-639">Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der blazor-Server-App:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-639">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="6ebfc-640">Die Lokalisierung wird in der APP behandelt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-640">Localization is handled in the app:</span></span>

1. <span data-ttu-id="6ebfc-641">Der Browser sendet eine anfängliche http-Anforderung an die app.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-641">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="6ebfc-642">Die Kultur wird von der Lokalisierungs Middleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-642">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="6ebfc-643">Die `OnGet`-Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-643">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="6ebfc-644">Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive blazor-Server Sitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-644">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="6ebfc-645">Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-645">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="6ebfc-646">Die blazor-Server Sitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-646">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="6ebfc-647">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="6ebfc-647">Provide UI to choose the culture</span></span>

<span data-ttu-id="6ebfc-648">Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-648">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="6ebfc-649">Der Prozess ähnelt dem, was in einer Web-App geschieht, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen no__t-0der Benutzer wird zu einer Anmeldeseite umgeleitet und dann zurück an die ursprüngliche Ressource umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-649">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="6ebfc-650">Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-650">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="6ebfc-651">Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-651">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="6ebfc-652">Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-652">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="6ebfc-653">Verwenden Sie das Ergebnis der Aktion "`LocalRedirect`", um offene Umleitungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-653">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="6ebfc-654">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-654">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="6ebfc-655">Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-655">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="6ebfc-656">Verwenden von .net-Lokalisierungs Szenarien in blazor-apps</span><span class="sxs-lookup"><span data-stu-id="6ebfc-656">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="6ebfc-657">Innerhalb von blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-657">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="6ebfc-658">. NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="6ebfc-658">.NET's resources system</span></span>
* <span data-ttu-id="6ebfc-659">Kulturspezifische Zahlen-und Datums Formatierung</span><span class="sxs-lookup"><span data-stu-id="6ebfc-659">Culture-specific number and date formatting</span></span>

<span data-ttu-id="6ebfc-660">Die `@bind`-Funktionalität von blazor führt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-660">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="6ebfc-661">Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-661">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="6ebfc-662">Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-662">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="6ebfc-663">`IStringLocalizer<>` wird in blazor-apps *unterstützt* .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-663">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="6ebfc-664">`IHtmlLocalizer<>`, `IViewLocalizer<>` und Lokalisierung von Daten Anmerkungen sind ASP.net Core MVC-Szenarien und werden in blazor-apps **nicht unterstützt** .</span><span class="sxs-lookup"><span data-stu-id="6ebfc-664">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="6ebfc-665">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-665">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="6ebfc-666">SVG-Bilder (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="6ebfc-666">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="6ebfc-667">Da blazor HTML rendert, werden vom Browser unterstützte Bilder, einschließlich der SVG-Bilder (Scalable Vector Graphics *) (SVG*), über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-667">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="6ebfc-668">Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ebfc-668">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="6ebfc-669">Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-669">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="6ebfc-670">Wenn Sie ein `<svg>`-Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-670">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="6ebfc-671">Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und `@bind` kann nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-671">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="6ebfc-672">Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-672">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ebfc-673">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6ebfc-673">Additional resources</span></span>

* <span data-ttu-id="6ebfc-674"><xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von blazor-Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="6ebfc-674"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
