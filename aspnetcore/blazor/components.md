---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/19/2019
uid: blazor/components
ms.openlocfilehash: 55b40bc640715bf4052fa99ed68f63250b67e8d1
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168220"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="aeb22-103">Erstellen und Verwenden von ASP.net Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="aeb22-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="aeb22-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aeb22-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="aeb22-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aeb22-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="aeb22-106">Blazor-apps werden mithilfe von *Komponenten*erstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="aeb22-107">Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="aeb22-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="aeb22-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="aeb22-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="aeb22-109">Komponenten sind flexibel und leicht zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="aeb22-110">Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="aeb22-111">Komponenten Klassen</span><span class="sxs-lookup"><span data-stu-id="aeb22-111">Component classes</span></span>

<span data-ttu-id="aeb22-112">Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="aeb22-113">Eine Komponente in blazor wird formal als *Razor-Komponente*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="aeb22-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="aeb22-115">Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="aeb22-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="aeb22-116">Komponenten können mit der Dateierweiterung *. cshtml* erstellt werden, sofern die Dateien mithilfe der `_RazorComponentInclude` MSBuild-Eigenschaft als Razor-Komponenten Dateien identifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="aeb22-117">Beispielsweise eine APP, die angibt, dass alle *cshtml* -Dateien unter dem Ordner *pages* als Razor-Komponenten Dateien behandelt werden sollen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="aeb22-118">Die Benutzeroberfläche für eine Komponente wird mit HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="aeb22-119">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="aeb22-120">Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="aeb22-121">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="aeb22-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="aeb22-122">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="aeb22-123">Im- `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="aeb22-124">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="aeb22-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="aeb22-125">In früheren Vorschauen von ASP.net Core 3,0 `@functions` wurden Blöcke für denselben Zweck verwendet wie `@code` Blöcke in Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="aeb22-126">`@functions`-Blöcke funktionieren weiterhin in Razor-Komponenten, aber es wird empfohlen `@code` , den-Block in ASP.net Core 3,0 Preview 6 oder höher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="aeb22-127">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden `@`, die mit beginnen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="aeb22-128">Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="aeb22-129">Im folgenden Beispiel wird ausgewertet und gerendert:</span><span class="sxs-lookup"><span data-stu-id="aeb22-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="aeb22-130">`_headingFontStyle`zum CSS-Eigenschafts Wert `font-style`für.</span><span class="sxs-lookup"><span data-stu-id="aeb22-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="aeb22-131">`_headingText`zum Inhalt des `<h1>` -Elements.</span><span class="sxs-lookup"><span data-stu-id="aeb22-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="aeb22-132">Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="aeb22-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="aeb22-133">Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen am Dokumentobjektmodell des Browsers (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="aeb22-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="aeb22-134">Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="aeb22-135">Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="aeb22-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="aeb22-136">Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="aeb22-137">Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports. Razor* -Datei der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="aeb22-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="aeb22-138">Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace `WebApplication`der APP lautet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="aeb22-139">Integrieren von Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="aeb22-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="aeb22-140">Verwenden Sie Komponenten mit vorhandenen Razor Pages und MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="aeb22-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="aeb22-141">Es ist nicht erforderlich, vorhandene Seiten oder Sichten für die Verwendung von Razor-Komponenten neu zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="aeb22-142">Wenn die Seite oder die Ansicht gerendert wird, werden die Komponenten gleichzeitig vorab in eine Vorabversion übernommen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="aeb22-143">Verwenden Sie die `RenderComponentAsync<TComponent>` HTML-Hilfsmethode, um eine Komponente von einer Seite oder Sicht zu Rendering:</span><span class="sxs-lookup"><span data-stu-id="aeb22-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="aeb22-144">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="aeb22-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="aeb22-145">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="aeb22-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="aeb22-146">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="aeb22-147">Weitere Informationen zum Rendern von Komponenten und zum Verwalten des <xref:blazor/hosting-models> Komponenten Status in blazor-Server-apps finden Sie im Artikel.</span><span class="sxs-lookup"><span data-stu-id="aeb22-147">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="aeb22-148">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="aeb22-148">Use components</span></span>

<span data-ttu-id="aeb22-149">Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="aeb22-150">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="aeb22-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="aeb22-151">Bei der Attribut Bindung wird Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="aeb22-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="aeb22-152">Beispielsweise `@bind` ist gültig, und `@Bind` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="aeb22-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="aeb22-153">Das folgende Markup in *Index. Razor* rendert `HeadingComponent` eine-Instanz:</span><span class="sxs-lookup"><span data-stu-id="aeb22-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="aeb22-154">*Komponenten/headingcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="aeb22-155">Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="aeb22-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="aeb22-156">Wenn Sie `@using` eine-Anweisung für den-Namespace der Komponente hinzufügen, wird die-Komponente verfügbar, wodurch die Warnung entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="aeb22-157">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-157">Component parameters</span></span>

<span data-ttu-id="aeb22-158">Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]` -Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="aeb22-159">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="aeb22-160">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="aeb22-161">Im folgenden Beispiel `ParentComponent` legt den Wert `Title` der-Eigenschaft von `ChildComponent`fest.</span><span class="sxs-lookup"><span data-stu-id="aeb22-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="aeb22-162">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="aeb22-163">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="aeb22-163">Child content</span></span>

<span data-ttu-id="aeb22-164">Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-164">Components can set the content of another component.</span></span> <span data-ttu-id="aeb22-165">Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="aeb22-166">Im folgenden Beispiel `ChildComponent` verfügt über eine `ChildContent` -Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="aeb22-167">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="aeb22-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="aeb22-168">Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des Bootstrap-Panels `panel-body`gerendert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="aeb22-169">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="aeb22-170">Die Eigenschaft, die `RenderFragment` den Inhalt empfängt, `ChildContent` muss gemäß der Konvention benannt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="aeb22-171">Im folgenden `ParentComponent` können Inhalte zum `ChildComponent` Rendern von bereitgestellt werden, indem der `<ChildComponent>` Inhalt innerhalb der Tags platziert wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="aeb22-172">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="aeb22-173">Attribut-Verteilung und beliebige Parameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="aeb22-174">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering.</span><span class="sxs-lookup"><span data-stu-id="aeb22-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="aeb22-175">Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe [@attributes](xref:mvc/views/razor#attributes) der Razor-Direktive gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="aeb22-176">Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="aeb22-177">Beispielsweise kann es mühsam sein, Attribute separat für einen `<input>` zu definieren, der viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="aeb22-178">Im folgenden `<input>` Beispiel verwendet das erste-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>` -Element (`id="useAttributesDict"`) Attribut-splatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="aeb22-179">Der Typ des Parameters muss mit `IEnumerable<KeyValuePair<string, object>>` Zeichen folgen Schlüsseln implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="aeb22-180">Die `IReadOnlyDictionary<string, object>` Verwendung von ist in diesem Szenario auch eine Option.</span><span class="sxs-lookup"><span data-stu-id="aeb22-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="aeb22-181">Die gerenderten `<input>` Elemente mit beiden Ansätzen sind identisch:</span><span class="sxs-lookup"><span data-stu-id="aeb22-181">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="aeb22-182">Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter `[Parameter]` mit dem- `CaptureUnmatchedValues` Attribut, wobei `true`die-Eigenschaft auf festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="aeb22-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="aeb22-183">Die `CaptureUnmatchedValues` -Eigenschaft `[Parameter]` in ermöglicht dem-Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="aeb22-184">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="aeb22-185">Der Eigenschaftentyp `CaptureUnmatchedValues` , der mit verwendet wird `Dictionary<string, object>` , muss mit Zeichen folgen Schlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="aeb22-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="aeb22-186">`IEnumerable<KeyValuePair<string, object>>`oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.</span><span class="sxs-lookup"><span data-stu-id="aeb22-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="aeb22-187">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="aeb22-187">Data binding</span></span>

<span data-ttu-id="aeb22-188">Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit [@bind](xref:mvc/views/razor#bind) dem-Attribut.</span><span class="sxs-lookup"><span data-stu-id="aeb22-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="aeb22-189">Im folgenden Beispiel wird das `_italicsCheck` Feld an den aktivierten Zustand des Kontrollkästchens gebunden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="aeb22-190">Wenn das Kontrollkästchen aktiviert und deaktiviert ist, wird der Wert der Eigenschaft auf `true` `false`bzw. aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="aeb22-191">Das Kontrollkästchen wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="aeb22-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="aeb22-192">Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen normalerweise sofort in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="aeb22-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="aeb22-193">Die `@bind` Verwendung von `CurrentValue` mit einer`<input @bind="CurrentValue" />`-Eigenschaft () entspricht im wesentlichen folgendem:</span><span class="sxs-lookup"><span data-stu-id="aeb22-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="aeb22-194">Wenn die Komponente gerendert wird `value` , stammt der des Eingabe Elements aus `CurrentValue` der-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="aeb22-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="aeb22-195">Wenn der Benutzer in das Textfeld eingibt, `onchange` wird das-Ereignis ausgelöst `CurrentValue` , und die-Eigenschaft wird auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="aeb22-196">Tatsächlich ist die Codegenerierung etwas komplexer, da `@bind` einige Fälle behandelt werden, in denen Typkonvertierungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="aeb22-197">Im Prinzip `@bind` ordnet den aktuellen Wert eines Ausdrucks einem `value` -Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.</span><span class="sxs-lookup"><span data-stu-id="aeb22-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="aeb22-198">Zusätzlich `onchange` zur Behandlung von Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [@bind-value](xref:mvc/views/razor#bind) Attribut mit einem `event` -Parameter[@bind-value:event](xref:mvc/views/razor#bind)() angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="aeb22-199">Im folgenden Beispiel wird die `CurrentValue` -Eigenschaft für `oninput` das-Ereignis gebunden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="aeb22-200">Anders `onchange`als, das ausgelöst wird, wenn das Element `oninput` den Fokus verliert, wird ausgelöst, wenn der Wert des Textfelds geändert wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="aeb22-201">**Nicht zu erteilbare Werte**</span><span class="sxs-lookup"><span data-stu-id="aeb22-201">**Unparsable values**</span></span>

<span data-ttu-id="aeb22-202">Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-202">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="aeb22-203">Betrachten Sie das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="aeb22-203">Consider the following scenario:</span></span>

* <span data-ttu-id="aeb22-204">Ein `<input>` Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-204">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="aeb22-205">Der Benutzer aktualisiert den Wert des-Elements auf `123.45` der Seite auf und ändert den Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="aeb22-205">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="aeb22-206">Im vorangehenden Szenario wird der Wert des-Elements auf zurück `123`gesetzt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-206">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="aeb22-207">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="aeb22-207">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="aeb22-208">Standardmäßig gilt die-Bindung für das- `onchange` Ereignis des`@bind="{PROPERTY OR FIELD}"`-Elements ().</span><span class="sxs-lookup"><span data-stu-id="aeb22-208">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="aeb22-209">Verwenden `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` Sie, um ein anderes Ereignis festzulegen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-209">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="aeb22-210">Für das `oninput` -Ereignis`@bind-value:event="oninput"`() tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-210">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="aeb22-211">Wenn das `oninput` Ereignis mit einem `int`-gebundenen Typ als Ziel verwendet wird, wird verhindert, dass `.` ein Benutzer ein Zeichen eingibt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-211">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="aeb22-212">Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-212">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="aeb22-213">Es gibt Szenarien, in denen das Wiederherstellen des `oninput` Werts für das Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht `<input>` zu testbaren Wert löschen darf.</span><span class="sxs-lookup"><span data-stu-id="aeb22-213">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="aeb22-214">Zu Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="aeb22-214">Alternatives include:</span></span>

* <span data-ttu-id="aeb22-215">Verwenden Sie das `oninput` Ereignis nicht.</span><span class="sxs-lookup"><span data-stu-id="aeb22-215">Don't use the `oninput` event.</span></span> <span data-ttu-id="aeb22-216">Verwenden Sie das `onchange` Standard Ereignis`@bind="{PROPERTY OR FIELD}"`(), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-216">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="aeb22-217">Binden Sie an einen Typ, der NULL- `int?` Werte `string`zulässt, wie z. b. oder, und stellen Sie eine benutzerdefinierte Logik bereit,</span><span class="sxs-lookup"><span data-stu-id="aeb22-217">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="aeb22-218">Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z `InputNumber` . `InputDate`b. oder.</span><span class="sxs-lookup"><span data-stu-id="aeb22-218">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="aeb22-219">Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-219">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="aeb22-220">Komponenten Validierungs Komponenten:</span><span class="sxs-lookup"><span data-stu-id="aeb22-220">Form validation components:</span></span>
  * <span data-ttu-id="aeb22-221">Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler für `EditContext`die zugeordnete zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-221">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="aeb22-222">Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-222">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="aeb22-223">**Globalisierung**</span><span class="sxs-lookup"><span data-stu-id="aeb22-223">**Globalization**</span></span>

<span data-ttu-id="aeb22-224">`@bind`Werte werden für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-224">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="aeb22-225">Der Zugriff auf die aktuelle Kultur ist über <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> die-Eigenschaft möglich.</span><span class="sxs-lookup"><span data-stu-id="aeb22-225">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="aeb22-226">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-226">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="aeb22-227">Die vorangehenden Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-227">The preceding field types:</span></span>

* <span data-ttu-id="aeb22-228">Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-228">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="aeb22-229">Darf keinen frei Form Text enthalten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-229">Can't contain free-form text.</span></span>
* <span data-ttu-id="aeb22-230">Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.</span><span class="sxs-lookup"><span data-stu-id="aeb22-230">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="aeb22-231">Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-231">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="aeb22-232">`@bind`unterstützt `@bind:culture` den-Parameter, <xref:System.Globalization.CultureInfo?displayProperty=fullName> um einen zum Auswerten und Formatieren eines-Werts bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-232">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="aeb22-233">Die Angabe einer Kultur ist nicht empfehlenswert, `date` Wenn `number` die Feldtypen und verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-233">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="aeb22-234">`date`und `number` verfügen über eine integrierte Unterstützung für blazor, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-234">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="aeb22-235">Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .</span><span class="sxs-lookup"><span data-stu-id="aeb22-235">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="aeb22-236">**Format Zeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="aeb22-236">**Format strings**</span></span>

<span data-ttu-id="aeb22-237">Die Datenbindung funktioniert <xref:System.DateTime> mit Format Zeichenfolgen mithilfe [@bind:format](xref:mvc/views/razor#bind)von.</span><span class="sxs-lookup"><span data-stu-id="aeb22-237">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="aeb22-238">Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="aeb22-238">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="aeb22-239">Im vorangehenden Code wird für `<input>` `text`den Feldtyp des Elements`type`() der Standardwert verwendet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-239">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="aeb22-240">`@bind:format`wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-240">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="aeb22-241"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="aeb22-241"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="aeb22-242"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="aeb22-242"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="aeb22-243">Das `@bind:format` -Attribut gibt das Datumsformat an, das `value` auf den `<input>` des Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="aeb22-243">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="aeb22-244">Das-Format wird auch verwendet, um den Wert zu analysieren `onchange` , wenn ein Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-244">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="aeb22-245">Es wird nicht empfohlen, `date` ein Format für den Feldtyp anzugeben, da blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-245">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="aeb22-246">**Komponenten Parameter**</span><span class="sxs-lookup"><span data-stu-id="aeb22-246">**Component parameters**</span></span>

<span data-ttu-id="aeb22-247">Die Bindung erkennt Komponenten Parameter, `@bind-{property}` wobei einen Eigenschafts Wert über Komponenten hinweg binden kann.</span><span class="sxs-lookup"><span data-stu-id="aeb22-247">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="aeb22-248">Die folgende untergeordnete Komponente`ChildComponent`() verfügt `Year` über einen Komponenten `YearChanged` Parameter und einen Rückruf:</span><span class="sxs-lookup"><span data-stu-id="aeb22-248">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="aeb22-249">`EventCallback<T>`wird im Abschnitt " [EventCallback](#eventcallback) " erläutert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-249">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="aeb22-250">Die folgende übergeordnete Komponente `ChildComponent` verwendet und bindet `ParentYear` den-Parameter vom übergeordneten `Year` an den-Parameter in der untergeordneten Komponente:</span><span class="sxs-lookup"><span data-stu-id="aeb22-250">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="aeb22-251">Das Laden von erzeugt das folgende Markup: `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="aeb22-251">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="aeb22-252">`ParentYear` Wenn der Wert der Eigenschaft durch Auswählen der Schaltfläche `ParentComponent`im geändert wird `ChildComponent` , wird die `Year` -Eigenschaft von aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-252">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="aeb22-253">Der neue Wert von `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-253">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="aeb22-254">Der `Year` -Parameter ist bindbar, da er über `YearChanged` ein Begleit Ereignis verfügt, das mit `Year` dem Typ des Parameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-254">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="aeb22-255">Gemäß der Konvention `<ChildComponent @bind-Year="ParentYear" />` entspricht im Wesentlichen dem Schreiben:</span><span class="sxs-lookup"><span data-stu-id="aeb22-255">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="aeb22-256">Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` des-Attributs an einen entsprechenden Ereignishandler gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-256">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="aeb22-257">Beispielsweise kann die- `MyProp` Eigenschaft mithilfe der folgenden `MyEventHandler` zwei Attribute an gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-257">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="aeb22-258">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="aeb22-258">Event handling</span></span>

<span data-ttu-id="aeb22-259">Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit.</span><span class="sxs-lookup"><span data-stu-id="aeb22-259">Razor components provide event handling features.</span></span> <span data-ttu-id="aeb22-260">Für ein HTML-Element Attribut `on{event}` mit dem Namen ( `onclick` z `onsubmit`. b. und) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="aeb22-260">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="aeb22-261">Der Name des Attributs ist immer mit " [ @on{Event}](xref:mvc/views/razor#onevent)" formatiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-261">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="aeb22-262">Der folgende Code Ruft die `UpdateHeading` -Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-262">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="aeb22-263">Der folgende Code Ruft die `CheckChanged` -Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-263">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="aeb22-264">Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-264">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="aeb22-265">Es ist nicht erforderlich, manuell aufzurufen `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="aeb22-265">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="aeb22-266">Ausnahmen werden protokolliert, wenn Sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-266">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="aeb22-267">Im folgenden Beispiel wird asynchron aufgerufen, `UpdateHeading` wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-267">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

### <a name="event-argument-types"></a><span data-ttu-id="aeb22-268">Ereignis Argument Typen</span><span class="sxs-lookup"><span data-stu-id="aeb22-268">Event argument types</span></span>

<span data-ttu-id="aeb22-269">Für einige Ereignisse sind Ereignis Argument Typen zulässig.</span><span class="sxs-lookup"><span data-stu-id="aeb22-269">For some events, event argument types are permitted.</span></span> <span data-ttu-id="aeb22-270">Wenn der Zugriff auf einen dieser Ereignis Typen nicht erforderlich ist, ist er im Methoden Aufrufvorgang nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="aeb22-270">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="aeb22-271">Die unterstützten [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-271">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="aeb22-272">event</span><span class="sxs-lookup"><span data-stu-id="aeb22-272">Event</span></span> | <span data-ttu-id="aeb22-273">Klasse</span><span class="sxs-lookup"><span data-stu-id="aeb22-273">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="aeb22-274">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="aeb22-274">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="aeb22-275">Hinein</span><span class="sxs-lookup"><span data-stu-id="aeb22-275">Drag</span></span>             | <span data-ttu-id="aeb22-276">`DragEventArgs`&ndash; und`DataTransferItem`halten gezogene Elementdaten. `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="aeb22-276">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="aeb22-277">Fehler</span><span class="sxs-lookup"><span data-stu-id="aeb22-277">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="aeb22-278">Fokus</span><span class="sxs-lookup"><span data-stu-id="aeb22-278">Focus</span></span>            | <span data-ttu-id="aeb22-279">`FocusEventArgs`Beinhaltet keine unter `relatedTarget`Stützung für. &ndash;</span><span class="sxs-lookup"><span data-stu-id="aeb22-279">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="aeb22-280">`<input>` -Änderung</span><span class="sxs-lookup"><span data-stu-id="aeb22-280">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="aeb22-281">Tastatur</span><span class="sxs-lookup"><span data-stu-id="aeb22-281">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="aeb22-282">Maus</span><span class="sxs-lookup"><span data-stu-id="aeb22-282">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="aeb22-283">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="aeb22-283">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="aeb22-284">Mausrad</span><span class="sxs-lookup"><span data-stu-id="aeb22-284">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="aeb22-285">Status</span><span class="sxs-lookup"><span data-stu-id="aeb22-285">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="aeb22-286">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="aeb22-286">Touch</span></span>            | <span data-ttu-id="aeb22-287">`TouchEventArgs`&ndash; stellteineneinzelnenKontaktpunktaufeinem`TouchPoint` Berührungs sensiblen Gerät dar.</span><span class="sxs-lookup"><span data-stu-id="aeb22-287">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="aeb22-288">Informationen zu den Eigenschaften und dem Ereignis Behandlungs Verhalten der Ereignisse in der vorangehenden Tabelle finden Sie unter [EventArgs classes in the Reference Source (ASPNET/aspnetcore Release/3.0-preview9 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="aeb22-288">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="aeb22-289">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="aeb22-289">Lambda expressions</span></span>

<span data-ttu-id="aeb22-290">Lambda-Ausdrücke können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-290">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="aeb22-291">Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-291">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="aeb22-292">Im folgenden Beispiel werden drei Schaltflächen erstellt, die jeweils `UpdateHeading` aufrufen, wenn ein Ereignis`MouseEventArgs`Argument () und ihre Schalt`buttonNumber`Flächen Nummer () übergeben werden, wenn es in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-292">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="aeb22-293">Verwenden Sie die Schleifenvariable (`i`) nicht in einer `for` Schleife direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="aeb22-293">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="aeb22-294">Andernfalls wird die gleiche Variable von allen Lambda Ausdrücken `i`verwendet, die bewirken, dass der Wert in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="aeb22-294">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="aeb22-295">Erfassen Sie den Wert stets in einer lokalen Variablen`buttonNumber` (im vorherigen Beispiel), und verwenden Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="aeb22-295">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="aeb22-296">EventCallback</span><span class="sxs-lookup"><span data-stu-id="aeb22-296">EventCallback</span></span>

<span data-ttu-id="aeb22-297">Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein unter&mdash;geordnetes Komponenten Ereignis auftritt, `onclick` z. b. Wenn ein Ereignis im untergeordneten auftritt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-297">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="aeb22-298">Um Ereignisse über Komponenten hinweg verfügbar zu machen `EventCallback`, verwenden Sie eine.</span><span class="sxs-lookup"><span data-stu-id="aeb22-298">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="aeb22-299">Eine übergeordnete Komponente kann eine Rückruf Methode zu einer untergeordneten Komponente `EventCallback`zuweisen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-299">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="aeb22-300">Die `ChildComponent` in der Beispiel-App veranschaulicht, wie der `onclick` Handler einer Schaltfläche so eingerichtet wird `EventCallback` , dass er einen Delegaten aus dem des `ParentComponent`Beispiels empfängt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-300">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="aeb22-301">Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="aeb22-301">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="aeb22-302">Der `ParentComponent` legt die- `ShowMessage` Methode `EventCallback<T>` des untergeordneten-Elements auf fest:</span><span class="sxs-lookup"><span data-stu-id="aeb22-302">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="aeb22-303">Wenn die Schaltfläche in der `ChildComponent`folgenden Option ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="aeb22-303">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="aeb22-304">Die `ParentComponent` -`ShowMessage` Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-304">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="aeb22-305">`messageText`wird aktualisiert und in der `ParentComponent`angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-305">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="aeb22-306">Ein-`ShowMessage`Befehl istinder-MethodedesRückrufs(`StateHasChanged` ) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="aeb22-306">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="aeb22-307">`StateHasChanged`wird automatisch aufgerufen, um den erneut `ParentComponent`zu starten, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-307">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="aeb22-308">`EventCallback`und `EventCallback<T>` erlauben asynchrone Delegaten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-308">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="aeb22-309">`EventCallback<T>`ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="aeb22-309">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="aeb22-310">`EventCallback`ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="aeb22-310">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="aeb22-311">Rufen Sie `EventCallback` ein `EventCallback<T>` oder `InvokeAsync` mit auf, <xref:System.Threading.Tasks.Task>und erwarten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aeb22-311">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="aeb22-312">Verwenden `EventCallback` Sie `EventCallback<T>` und für die Parameter für die Ereignis Behandlung und die bindungskomponente.</span><span class="sxs-lookup"><span data-stu-id="aeb22-312">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="aeb22-313">Bevorzugen Sie die stark `EventCallback<T>` typisierte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="aeb22-313">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="aeb22-314">`EventCallback<T>`bietet eine bessere Fehlermeldung für Benutzer der Komponente.</span><span class="sxs-lookup"><span data-stu-id="aeb22-314">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="aeb22-315">Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional.</span><span class="sxs-lookup"><span data-stu-id="aeb22-315">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="aeb22-316">Verwenden `EventCallback` Sie, wenn kein Wert an den Rückruf übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-316">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="aeb22-317">Verkettete Bindung</span><span class="sxs-lookup"><span data-stu-id="aeb22-317">Chained bind</span></span>

<span data-ttu-id="aeb22-318">Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="aeb22-318">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="aeb22-319">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-319">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="aeb22-320">Eine verkettete Bindung kann nicht `@bind` mit Syntax im-Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-320">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="aeb22-321">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-321">The event handler and value must be specified separately.</span></span> <span data-ttu-id="aeb22-322">Eine übergeordnete Komponente kann jedoch Syntax mit `@bind` dem-Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-322">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="aeb22-323">Die folgende `PasswordField` Komponente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="aeb22-323">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="aeb22-324">Legt den `<input>` Wert eines Elements auf eine `Password` Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="aeb22-324">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="aeb22-325">Macht Änderungen der `Password` Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="aeb22-325">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

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

<span data-ttu-id="aeb22-326">Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-326">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="aeb22-327">Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aeb22-327">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="aeb22-328">Erstellen Sie ein Unterstützungs Feld `Password` für`password` (im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="aeb22-328">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="aeb22-329">Führen Sie die Überprüfungen oder Trap Fehler `Password` im Setter aus.</span><span class="sxs-lookup"><span data-stu-id="aeb22-329">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="aeb22-330">Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-330">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="capture-references-to-components"></a><span data-ttu-id="aeb22-331">Verweise auf Komponenten erfassen</span><span class="sxs-lookup"><span data-stu-id="aeb22-331">Capture references to components</span></span>

<span data-ttu-id="aeb22-332">Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, `Show` z `Reset`. b. oder.</span><span class="sxs-lookup"><span data-stu-id="aeb22-332">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="aeb22-333">So erfassen Sie einen Komponenten Verweis:</span><span class="sxs-lookup"><span data-stu-id="aeb22-333">To capture a component reference:</span></span>

* <span data-ttu-id="aeb22-334">Fügen Sie [@ref](xref:mvc/views/razor#ref) der untergeordneten Komponente ein Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="aeb22-334">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="aeb22-335">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="aeb22-335">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="aeb22-336">Wenn die Komponente gerendert wird `loginDialog` , wird das Feld mit `MyLoginDialog` der untergeordneten Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-336">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="aeb22-337">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-337">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aeb22-338">Die `loginDialog` -Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und `MyLoginDialog` die Ausgabe das-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="aeb22-338">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="aeb22-339">Bis zu diesem Punkt sind keine Verweise mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-339">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="aeb22-340">Verwenden Sie die-Methode oder `OnAfterRenderAsync` `OnAfterRender` die-Methode, um Komponenten Verweise nach dem Rendering der Komponente zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-340">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="aeb22-341">Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion</span><span class="sxs-lookup"><span data-stu-id="aeb22-341">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="aeb22-342">Komponenten Verweise werden nicht an JavaScript-&mdash;Code übermittelt, sondern nur in .NET-Code.</span><span class="sxs-lookup"><span data-stu-id="aeb22-342">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="aeb22-343">Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-343">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="aeb22-344">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-344">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="aeb22-345">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-345">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="aeb22-346">Komponenten Methoden extern aufrufen, um den Status zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="aeb22-346">Invoke component methods externally to update state</span></span>

<span data-ttu-id="aeb22-347">Blazor verwendet einen `SynchronizationContext` , um einen einzelnen logischen Ausführungs Thread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-347">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="aeb22-348">Die Lebenszyklus Methoden einer Komponente und alle Ereignis Rückrufe, die von blazor ausgelöst werden, werden `SynchronizationContext`für dieses ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-348">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="aeb22-349">Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, `InvokeAsync` verwenden Sie die- `SynchronizationContext`Methode, die an blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-349">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="aeb22-350">Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="aeb22-350">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="aeb22-351">Verwendung des `NotifierService` zum Aktualisieren einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="aeb22-351">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="aeb22-352">Im vorherigen Beispiel `NotifierService` Ruft die- `OnNotify` Methode der Komponente `SynchronizationContext`außerhalb von blazor auf.</span><span class="sxs-lookup"><span data-stu-id="aeb22-352">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="aeb22-353">`InvokeAsync`wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-353">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="aeb22-354">Verwenden \@Sie den Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-354">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="aeb22-355">Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss der diffalling-Algorithmus von blazor entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-355">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="aeb22-356">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-356">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="aeb22-357">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aeb22-357">Consider the following example:</span></span>

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

<span data-ttu-id="aeb22-358">Der Inhalt `People` der Auflistung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-358">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="aeb22-359">Wenn die Komponente erneut ausgeführt wird, `<DetailsEditor>` kann sich die Komponente ändern, `Details` um unterschiedliche Parameterwerte zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-359">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="aeb22-360">Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-360">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="aeb22-361">In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="aeb22-361">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="aeb22-362">Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-362">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="aeb22-363">`@key`bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-363">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="aeb22-364">Wenn sich `People` die Auflistung ändert, behält der diff-Algorithmus die Zuordnung `<DetailsEditor>` zwischen Instanzen `person` und Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="aeb22-364">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="aeb22-365">Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die `<DetailsEditor>` entsprechende-Instanz aus der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-365">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="aeb22-366">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-366">Other instances are left unchanged.</span></span>
* <span data-ttu-id="aeb22-367">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird `<DetailsEditor>` eine neue-Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-367">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="aeb22-368">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-368">Other instances are left unchanged.</span></span>
* <span data-ttu-id="aeb22-369">Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-369">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="aeb22-370">In einigen Szenarien minimiert die Verwendung `@key` von die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. die Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="aeb22-370">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aeb22-371">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="aeb22-371">Keys are local to each container element or component.</span></span> <span data-ttu-id="aeb22-372">Schlüssel werden nicht global über das Dokument hinweg verglichen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-372">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="aeb22-373">Verwendung \@des Schlüssels</span><span class="sxs-lookup"><span data-stu-id="aeb22-373">When to use \@key</span></span>

<span data-ttu-id="aeb22-374">In der Regel ist es sinnvoll, `@key` immer dann zu verwenden, wenn eine Liste gerendert `@foreach` wird (z. b. in einem-Block `@key`) und ein geeigneter Wert zum Definieren von vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="aeb22-374">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="aeb22-375">Sie können auch verwenden `@key` , um zu verhindern, dass blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="aeb22-375">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="aeb22-376">Wenn `@currentPerson` sich ändert, `@key` erzwingt die Attribut Direktive, dass blazor den gesamten `<div>` und seine Nachfolger verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-376">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="aeb22-377">Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand `@currentPerson` beibehalten wird, wenn Änderungen vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-377">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="aeb22-378">Verwendung \@des Schlüssels nicht</span><span class="sxs-lookup"><span data-stu-id="aeb22-378">When not to use \@key</span></span>

<span data-ttu-id="aeb22-379">Bei der Verwendung von ist eine Leistungssteigerung zu `@key`erzielen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-379">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="aeb22-380">Die Leistungskosten sind nicht groß, sondern nur `@key` , wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-380">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="aeb22-381">Auch wenn `@key` nicht verwendet wird, behält blazor das untergeordnete Element und die Komponenten Instanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="aeb22-381">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="aeb22-382">Der einzige Vorteil bei der `@key` Verwendung von besteht darin, zu steuern, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt den diffor-Algorithmus, der die Zuordnung auswählt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-382">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="aeb22-383">Welche Werte für \@Key verwendet werden sollen</span><span class="sxs-lookup"><span data-stu-id="aeb22-383">What values to use for \@key</span></span>

<span data-ttu-id="aeb22-384">Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-384">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="aeb22-385">Modell Objektinstanzen (z. b. `Person` eine-Instanz, wie im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="aeb22-385">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="aeb22-386">Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-386">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="aeb22-387">Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom `int`Typ `string`, oder `Guid`).</span><span class="sxs-lookup"><span data-stu-id="aeb22-387">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="aeb22-388">Stellen Sie sicher, dass `@key` die für verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-388">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="aeb22-389">Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="aeb22-389">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="aeb22-390">Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.</span><span class="sxs-lookup"><span data-stu-id="aeb22-390">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="aeb22-391">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="aeb22-391">Lifecycle methods</span></span>

<span data-ttu-id="aeb22-392">`OnInitializedAsync`und `OnInitialized` führen Code aus, um die Komponente zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-392">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="aeb22-393">Verwenden `OnInitializedAsync` Sie zum Ausführen eines asynchronen Vorgangs das- `await` Schlüsselwort und das-Schlüsselwort für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="aeb22-393">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="aeb22-394">Verwenden `OnInitialized`Sie für einen synchronen Vorgang Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aeb22-394">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="aeb22-395">`OnParametersSetAsync`und `OnParametersSet` werden aufgerufen, wenn eine Komponente Parameter von ihrem übergeordneten Element empfangen hat und die Werte Eigenschaften zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-395">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="aeb22-396">Diese Methoden werden nach der Initialisierung der Komponente und jedes Mal, wenn die Komponente gerendert wird, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-396">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="aeb22-397">`OnAfterRenderAsync`und `OnAfterRender` werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="aeb22-397">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="aeb22-398">Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-398">Element and component references are populated at this point.</span></span> <span data-ttu-id="aeb22-399">Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom</span><span class="sxs-lookup"><span data-stu-id="aeb22-399">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="aeb22-400">`OnAfterRender`*wird nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*</span><span class="sxs-lookup"><span data-stu-id="aeb22-400">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="aeb22-401">Der `firstRender` -Parameter `OnAfterRenderAsync` für `OnAfterRender` und ist:</span><span class="sxs-lookup"><span data-stu-id="aeb22-401">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="aeb22-402">Wird zum `true` ersten Mal aufgerufen, wenn die Komponenteninstanz aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-402">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="aeb22-403">Stellt sicher, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-403">Ensures that initialization work is only performed once.</span></span>

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

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="aeb22-404">Unvollständige Async-Aktionen bei Rendering behandeln</span><span class="sxs-lookup"><span data-stu-id="aeb22-404">Handle incomplete async actions at render</span></span>

<span data-ttu-id="aeb22-405">Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert</span><span class="sxs-lookup"><span data-stu-id="aeb22-405">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="aeb22-406">Objekte können oder `null` nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-406">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="aeb22-407">Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert</span><span class="sxs-lookup"><span data-stu-id="aeb22-407">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="aeb22-408">Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht `null`) dar, während-Objekte sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-408">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="aeb22-409">In der `FetchData` -Komponente der blazor-Vorlagen `OnInitializedAsync` wird überschrieben, um Vorhersagedaten asynchron`forecasts`zu empfangen ().</span><span class="sxs-lookup"><span data-stu-id="aeb22-409">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="aeb22-410">Wenn `forecasts`den Wert hat,wirddemBenutzereineMeldungzumLadenangezeigt.`null`</span><span class="sxs-lookup"><span data-stu-id="aeb22-410">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="aeb22-411">Nachdem der `Task` von `OnInitializedAsync` zurückgegebene abgeschlossen wurde, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-411">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="aeb22-412">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-412">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="aeb22-413">Ausführen von Code vor dem Festlegen von Parametern</span><span class="sxs-lookup"><span data-stu-id="aeb22-413">Execute code before parameters are set</span></span>

<span data-ttu-id="aeb22-414">`SetParameters`kann überschrieben werden, um Code vor dem Festlegen von Parametern auszuführen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-414">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="aeb22-415">Wenn `base.SetParameters` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-415">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="aeb22-416">Beispielsweise müssen die eingehenden Parameter nicht den Eigenschaften der Klasse zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-416">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="aeb22-417">Aktualisierung der Benutzeroberfläche unterdrücken</span><span class="sxs-lookup"><span data-stu-id="aeb22-417">Suppress refreshing of the UI</span></span>

<span data-ttu-id="aeb22-418">`ShouldRender`kann überschrieben werden, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="aeb22-418">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="aeb22-419">Wenn die Implementierung zurück `true`gibt, wird die Benutzeroberfläche aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-419">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="aeb22-420">Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-420">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="aeb22-421">Komponenten Beseitigung mit iverwerf</span><span class="sxs-lookup"><span data-stu-id="aeb22-421">Component disposal with IDisposable</span></span>

<span data-ttu-id="aeb22-422">Wenn eine Komponente implementiert <xref:System.IDisposable>, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-422">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="aeb22-423">Die folgende Komponente verwendet `@implements IDisposable` die- `Dispose` Methode und die-Methode:</span><span class="sxs-lookup"><span data-stu-id="aeb22-423">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="aeb22-424">Routing</span><span class="sxs-lookup"><span data-stu-id="aeb22-424">Routing</span></span>

<span data-ttu-id="aeb22-425">Das Routing in blazor wird durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der APP erreicht.</span><span class="sxs-lookup"><span data-stu-id="aeb22-425">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="aeb22-426">Wenn eine Razor-Datei mit `@page` einer-Direktive kompiliert wird, erhält die generierte <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> -Klasse einen, der die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-426">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="aeb22-427">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-427">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="aeb22-428">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-428">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="aeb22-429">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="aeb22-429">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="aeb22-430">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-430">Route parameters</span></span>

<span data-ttu-id="aeb22-431">Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in `@page` der-Direktive bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-431">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="aeb22-432">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-432">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="aeb22-433">Weiterleitungs *Parameter Komponente*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-433">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="aeb22-434">Optionale Parameter werden nicht unterstützt, `@page` daher werden im obigen Beispiel zwei-Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-434">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="aeb22-435">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="aeb22-435">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="aeb22-436">Die zweite `@page` Anweisung übernimmt den `{text}` Routen Parameter und `Text` weist den Wert der-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="aeb22-436">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="aeb22-437">Basisklassen Vererbung für eine "Code Behind"-Funktion</span><span class="sxs-lookup"><span data-stu-id="aeb22-437">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="aeb22-438">Komponenten Dateien mischen HTML-Markup C# und Verarbeitungs Code in derselben Datei.</span><span class="sxs-lookup"><span data-stu-id="aeb22-438">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="aeb22-439">Die `@inherits` -Direktive kann verwendet werden, um blazor-apps eine "Code Behind"-Funktion bereitzustellen, die das Komponenten Markup von der Code Verarbeitung trennt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-439">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="aeb22-440">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) zeigt, wie eine Komponente eine Basisklasse erben `BlazorRocksBase`kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-440">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="aeb22-441">*Pages/blazorrocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-441">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="aeb22-442">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-442">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="aeb22-443">Die Basisklasse sollte von `ComponentBase`abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-443">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="aeb22-444">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="aeb22-444">Import components</span></span>

<span data-ttu-id="aeb22-445">Der Namespace einer mit Razor erstellten Komponente basiert auf:</span><span class="sxs-lookup"><span data-stu-id="aeb22-445">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="aeb22-446">Der des Projekts `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="aeb22-446">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="aeb22-447">Der Pfad vom Projektstamm zur Komponente.</span><span class="sxs-lookup"><span data-stu-id="aeb22-447">The path from the project root to the component.</span></span> <span data-ttu-id="aeb22-448">Beispielsweise `ComponentsSample/Pages/Index.razor` befindet sich im-Namespace `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="aeb22-448">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="aeb22-449">Komponenten folgen C# den namens Bindungs Regeln.</span><span class="sxs-lookup"><span data-stu-id="aeb22-449">Components follow C# name binding rules.</span></span> <span data-ttu-id="aeb22-450">Im Fall von " *Index. Razor*" befinden sich alle Komponenten im gleichen Ordner, auf den *Seiten*und im übergeordneten Ordner " *componentssample*" im Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="aeb22-450">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="aeb22-451">Komponenten, die in einem anderen Namespace definiert sind, können mit der [ \@using](xref:mvc/views/razor#using) -Direktive von Razor in den Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="aeb22-451">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="aeb22-452">Wenn eine andere Komponente `NavMenu.razor`,, im Ordner `ComponentsSample/Shared/`vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using` Anweisung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-452">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="aeb22-453">Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, sodass die [ using-Direktivenichtmehrbenötigtwird:\@](xref:mvc/views/razor#using)</span><span class="sxs-lookup"><span data-stu-id="aeb22-453">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="aeb22-454">Die `global::` Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-454">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="aeb22-455">Das Importieren von Komponenten mit `using` Alias Anweisungen ( `@using Foo = Bar`z. b.) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-455">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="aeb22-456">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-456">Partially qualified names aren't supported.</span></span> <span data-ttu-id="aeb22-457">Beispielsweise wird das `@using ComponentsSample` Hinzufügen von `NavMenu.razor` und `<Shared.NavMenu></Shared.NavMenu>` verweisen mit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-457">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="aeb22-458">Attribute für bedingtes HTML-Element</span><span class="sxs-lookup"><span data-stu-id="aeb22-458">Conditional HTML element attributes</span></span>

<span data-ttu-id="aeb22-459">HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-459">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="aeb22-460">Wenn der Wert oder `false` `null`ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-460">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="aeb22-461">Wenn der Wert ist `true`, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-461">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="aeb22-462">Im folgenden Beispiel wird von `IsCompleted` bestimmt, `checked` ob im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-462">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="aeb22-463">Wenn `IsCompleted`den Wert hat,wirddasKontrollkästchenwiefolgtgerendert:`true`</span><span class="sxs-lookup"><span data-stu-id="aeb22-463">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="aeb22-464">Wenn `IsCompleted`den Wert hat,wirddasKontrollkästchenwiefolgtgerendert:`false`</span><span class="sxs-lookup"><span data-stu-id="aeb22-464">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="aeb22-465">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="aeb22-465">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="aeb22-466">Unformatierte HTML</span><span class="sxs-lookup"><span data-stu-id="aeb22-466">Raw HTML</span></span>

<span data-ttu-id="aeb22-467">Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-467">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="aeb22-468">Um unformatierten HTML-Code zu erstellen, umschließen Sie den HTML-Inhalt in einen `MarkupString`</span><span class="sxs-lookup"><span data-stu-id="aeb22-468">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="aeb22-469">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-469">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="aeb22-470">Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-470">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="aeb22-471">Im folgenden Beispiel wird gezeigt, `MarkupString` wie der-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-471">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="aeb22-472">Komponenten mit Vorlagen</span><span class="sxs-lookup"><span data-stu-id="aeb22-472">Templated components</span></span>

<span data-ttu-id="aeb22-473">Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="aeb22-473">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="aeb22-474">Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-474">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="aeb22-475">Einige Beispiele sind:</span><span class="sxs-lookup"><span data-stu-id="aeb22-475">A couple of examples include:</span></span>

* <span data-ttu-id="aeb22-476">Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.</span><span class="sxs-lookup"><span data-stu-id="aeb22-476">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="aeb22-477">Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-477">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="aeb22-478">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-478">Template parameters</span></span>

<span data-ttu-id="aeb22-479">Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-479">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="aeb22-480">Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar.</span><span class="sxs-lookup"><span data-stu-id="aeb22-480">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="aeb22-481">`RenderFragment<T>`nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-481">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="aeb22-482">`TableTemplate`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-482">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="aeb22-483">Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können Sie die Vorlagen Parameter mit untergeordneten Elementen angeben, die mit den Namen`TableHeader` der `RowTemplate` Parameter (und im folgenden Beispiel) identisch sind:</span><span class="sxs-lookup"><span data-stu-id="aeb22-483">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="aeb22-484">Vorlagen Kontext Parameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-484">Template context parameters</span></span>

<span data-ttu-id="aeb22-485">Komponenten Argumente vom Typ `RenderFragment<T>` , `@context.PetId`die als-Elemente übergeben werden, `context` verfügen über einen impliziten Parameter mit dem Namen (z. b. aus dem vorangehenden Codebeispiel) `Context` . Sie können den Parameternamen jedoch mit dem-Attribut für das untergeordnete gewisses.</span><span class="sxs-lookup"><span data-stu-id="aeb22-485">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="aeb22-486">Im folgenden Beispiel gibt das `RowTemplate` -Attribut des-Elements den `pet` - `Context` Parameter an:</span><span class="sxs-lookup"><span data-stu-id="aeb22-486">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="aeb22-487">Alternativ können Sie das `Context` -Attribut für das Component-Element angeben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-487">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="aeb22-488">Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter.</span><span class="sxs-lookup"><span data-stu-id="aeb22-488">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="aeb22-489">Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element).</span><span class="sxs-lookup"><span data-stu-id="aeb22-489">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="aeb22-490">Im folgenden Beispiel wird das `Context` -Attribut `TableTemplate` im-Element angezeigt und gilt für alle Vorlagen Parameter:</span><span class="sxs-lookup"><span data-stu-id="aeb22-490">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="aeb22-491">Generisch typisierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="aeb22-491">Generic-typed components</span></span>

<span data-ttu-id="aeb22-492">Auf Vorlagen basierende Komponenten werden oft generisch typisiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-492">Templated components are often generically typed.</span></span> <span data-ttu-id="aeb22-493">Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` von Werten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-493">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="aeb22-494">Zum Definieren einer generischen Komponente verwenden Sie [@typeparam](xref:mvc/views/razor#typeparam) die-Direktive, um Typparameter anzugeben:</span><span class="sxs-lookup"><span data-stu-id="aeb22-494">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="aeb22-495">Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-495">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="aeb22-496">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-496">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="aeb22-497">Im folgenden Beispiel wird `TItem="Pet"` der-Typ angegeben:</span><span class="sxs-lookup"><span data-stu-id="aeb22-497">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="aeb22-498">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="aeb22-498">Cascading values and parameters</span></span>

<span data-ttu-id="aeb22-499">In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-499">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="aeb22-500">Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-500">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="aeb22-501">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-501">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="aeb22-502">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="aeb22-502">Theme example</span></span>

<span data-ttu-id="aeb22-503">Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo` -Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.</span><span class="sxs-lookup"><span data-stu-id="aeb22-503">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="aeb22-504">*Uithmeclasses/themerfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="aeb22-504">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="aeb22-505">Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-505">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="aeb22-506">Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="aeb22-506">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="aeb22-507">Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext `@Body` der Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-507">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="aeb22-508">`ButtonClass`wird `btn-success` in der Layoutkomponente der Wert zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-508">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="aeb22-509">Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierende Objekt nutzen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-509">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="aeb22-510">`CascadingValuesParametersLayout`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-510">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="aeb22-511">Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]` -Attributs.</span><span class="sxs-lookup"><span data-stu-id="aeb22-511">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="aeb22-512">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-512">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="aeb22-513">In der Beispiel-App bindet `CascadingValuesParametersTheme` die Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="aeb22-513">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="aeb22-514">Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-514">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="aeb22-515">`CascadingValuesParametersTheme`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-515">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="aeb22-516">Wenn Sie mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren möchten, stellen Sie `Name` eine eindeutige Zeichen `CascadingValue` Folge für jede Komponente `CascadingParameter`und ihre entsprechende bereit.</span><span class="sxs-lookup"><span data-stu-id="aeb22-516">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="aeb22-517">Im folgenden Beispiel werden unterschiedliche `CascadingValue` Instanzen von `MyCascadingType` anhand des Namens von zwei Komponenten kaskadiert:</span><span class="sxs-lookup"><span data-stu-id="aeb22-517">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="aeb22-518">In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-518">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="aeb22-519">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="aeb22-519">TabSet example</span></span>

<span data-ttu-id="aeb22-520">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="aeb22-520">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="aeb22-521">Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.</span><span class="sxs-lookup"><span data-stu-id="aeb22-521">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="aeb22-522">Die Beispiel-App verfügt `ITab` über eine Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="aeb22-522">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="aeb22-523">Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet` -Komponente, die mehrere `Tab` Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="aeb22-523">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="aeb22-524">Die untergeordneten `TabSet` KomponentenwerdennichtexplizitalsParameterandas-`Tab` Element übermittelt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-524">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="aeb22-525">Stattdessen sind die unter `Tab` geordneten Komponenten Teil des untergeordneten Inhalts `TabSet`von.</span><span class="sxs-lookup"><span data-stu-id="aeb22-525">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="aeb22-526">Allerdings muss `TabSet` immer noch über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code `TabSet` erforderlich ist, *kann sich die Komponente selbst als kaskadierenden Wert bereitstellen* , der dann `Tab` von den Nachfolger Komponenten abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-526">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="aeb22-527">`TabSet`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-527">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="aeb22-528">Die Nachfolger `TabSet` `Tab` `TabSet` Komponenten erfassen den enthaltenden als kaskadierenden Parameter, sodass sich die Komponenten der-und-Koordinate, auf der die Registerkarte aktiv ist, selbst hinzufügen. `Tab`</span><span class="sxs-lookup"><span data-stu-id="aeb22-528">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="aeb22-529">`Tab`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-529">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="aeb22-530">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="aeb22-530">Razor templates</span></span>

<span data-ttu-id="aeb22-531">Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-531">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="aeb22-532">Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:</span><span class="sxs-lookup"><span data-stu-id="aeb22-532">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="aeb22-533">Im folgenden Beispiel wird veranschaulicht, wie `RenderFragment` - `RenderFragment<T>` Werte und-Werte angegeben und Vorlagen direkt in einer-Komponente dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-533">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="aeb22-534">Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-534">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="aeb22-535">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="aeb22-535">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="aeb22-536">Manuelle rendertreebuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="aeb22-536">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="aeb22-537">`Microsoft.AspNetCore.Components.RenderTree`stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.</span><span class="sxs-lookup"><span data-stu-id="aeb22-537">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="aeb22-538">Die Verwendung `RenderTreeBuilder` von zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="aeb22-538">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="aeb22-539">Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-539">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="aeb22-540">Sehen Sie sich `PetDetails` die folgende Komponente an, die manuell in eine andere Komponente integriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="aeb22-540">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="aeb22-541">Im folgenden Beispiel generiert die-Schleife in der `CreateComponent` -Methode drei `PetDetails` -Komponenten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-541">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="aeb22-542">Beim Aufrufen `RenderTreeBuilder` von Methoden zum Erstellen der Komponenten`OpenComponent` ( `AddAttribute`und) sind Sequenznummern Quell Codezeilen Nummern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-542">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="aeb22-543">Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-543">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="aeb22-544">Wenn Sie eine Komponente mit `RenderTreeBuilder` Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-544">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="aeb22-545">**Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="aeb22-545">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="aeb22-546">Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="aeb22-546">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="aeb22-547">`BuiltContent`Zulieferern</span><span class="sxs-lookup"><span data-stu-id="aeb22-547">`BuiltContent` component:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="aeb22-548">Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="aeb22-548">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="aeb22-549">Blazor `.razor` -Dateien werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-549">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="aeb22-550">Dies ist möglicherweise ein großer Vorteil `.razor` für, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-550">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="aeb22-551">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="aeb22-551">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="aeb22-552">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-552">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="aeb22-553">Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.</span><span class="sxs-lookup"><span data-stu-id="aeb22-553">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="aeb22-554">Sehen Sie sich die `.razor` folgende einfache Datei an:</span><span class="sxs-lookup"><span data-stu-id="aeb22-554">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="aeb22-555">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="aeb22-555">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="aeb22-556">Wenn der Code zum ersten Mal ausgeführt wird, wenn `someFlag` `true`den Wert hat, empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aeb22-556">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="aeb22-557">Sequenz</span><span class="sxs-lookup"><span data-stu-id="aeb22-557">Sequence</span></span> | <span data-ttu-id="aeb22-558">Typ</span><span class="sxs-lookup"><span data-stu-id="aeb22-558">Type</span></span>      | <span data-ttu-id="aeb22-559">Daten</span><span class="sxs-lookup"><span data-stu-id="aeb22-559">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="aeb22-560">0</span><span class="sxs-lookup"><span data-stu-id="aeb22-560">0</span></span>        | <span data-ttu-id="aeb22-561">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-561">Text node</span></span> | <span data-ttu-id="aeb22-562">First</span><span class="sxs-lookup"><span data-stu-id="aeb22-562">First</span></span>  |
| <span data-ttu-id="aeb22-563">1</span><span class="sxs-lookup"><span data-stu-id="aeb22-563">1</span></span>        | <span data-ttu-id="aeb22-564">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-564">Text node</span></span> | <span data-ttu-id="aeb22-565">Second</span><span class="sxs-lookup"><span data-stu-id="aeb22-565">Second</span></span> |

<span data-ttu-id="aeb22-566">Stellen Sie sich vor, und das Markup wird wieder gerendert. `false` `someFlag`</span><span class="sxs-lookup"><span data-stu-id="aeb22-566">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="aeb22-567">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aeb22-567">This time, the builder receives:</span></span>

| <span data-ttu-id="aeb22-568">Sequenz</span><span class="sxs-lookup"><span data-stu-id="aeb22-568">Sequence</span></span> | <span data-ttu-id="aeb22-569">Typ</span><span class="sxs-lookup"><span data-stu-id="aeb22-569">Type</span></span>       | <span data-ttu-id="aeb22-570">Daten</span><span class="sxs-lookup"><span data-stu-id="aeb22-570">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="aeb22-571">1</span><span class="sxs-lookup"><span data-stu-id="aeb22-571">1</span></span>        | <span data-ttu-id="aeb22-572">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-572">Text node</span></span>  | <span data-ttu-id="aeb22-573">Second</span><span class="sxs-lookup"><span data-stu-id="aeb22-573">Second</span></span> |

<span data-ttu-id="aeb22-574">Wenn die Laufzeit einen Diff-Vorgang ausführt, wird angezeigt, dass das `0` Element in der Sequenz entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:</span><span class="sxs-lookup"><span data-stu-id="aeb22-574">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="aeb22-575">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-575">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="aeb22-576">Was schief geht, wenn Sie Programm gesteuert Sequenznummern generieren</span><span class="sxs-lookup"><span data-stu-id="aeb22-576">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="aeb22-577">Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:</span><span class="sxs-lookup"><span data-stu-id="aeb22-577">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="aeb22-578">Nun lautet die erste Ausgabe wie folgt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-578">Now, the first output is:</span></span>

| <span data-ttu-id="aeb22-579">Sequenz</span><span class="sxs-lookup"><span data-stu-id="aeb22-579">Sequence</span></span> | <span data-ttu-id="aeb22-580">Typ</span><span class="sxs-lookup"><span data-stu-id="aeb22-580">Type</span></span>      | <span data-ttu-id="aeb22-581">Daten</span><span class="sxs-lookup"><span data-stu-id="aeb22-581">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="aeb22-582">0</span><span class="sxs-lookup"><span data-stu-id="aeb22-582">0</span></span>        | <span data-ttu-id="aeb22-583">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-583">Text node</span></span> | <span data-ttu-id="aeb22-584">First</span><span class="sxs-lookup"><span data-stu-id="aeb22-584">First</span></span>  |
| <span data-ttu-id="aeb22-585">1</span><span class="sxs-lookup"><span data-stu-id="aeb22-585">1</span></span>        | <span data-ttu-id="aeb22-586">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-586">Text node</span></span> | <span data-ttu-id="aeb22-587">Second</span><span class="sxs-lookup"><span data-stu-id="aeb22-587">Second</span></span> |

<span data-ttu-id="aeb22-588">Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-588">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="aeb22-589">`someFlag`befindet `false` sich auf dem zweiten Rendering, und die Ausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="aeb22-589">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="aeb22-590">Sequenz</span><span class="sxs-lookup"><span data-stu-id="aeb22-590">Sequence</span></span> | <span data-ttu-id="aeb22-591">Typ</span><span class="sxs-lookup"><span data-stu-id="aeb22-591">Type</span></span>      | <span data-ttu-id="aeb22-592">Daten</span><span class="sxs-lookup"><span data-stu-id="aeb22-592">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="aeb22-593">0</span><span class="sxs-lookup"><span data-stu-id="aeb22-593">0</span></span>        | <span data-ttu-id="aeb22-594">Textknoten</span><span class="sxs-lookup"><span data-stu-id="aeb22-594">Text node</span></span> | <span data-ttu-id="aeb22-595">Second</span><span class="sxs-lookup"><span data-stu-id="aeb22-595">Second</span></span> |

<span data-ttu-id="aeb22-596">Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:</span><span class="sxs-lookup"><span data-stu-id="aeb22-596">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="aeb22-597">Ändern Sie den Wert für den ersten Textknoten `Second`in.</span><span class="sxs-lookup"><span data-stu-id="aeb22-597">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="aeb22-598">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-598">Remove the second text node.</span></span>

<span data-ttu-id="aeb22-599">Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo `if/else` die Verzweigungen und Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="aeb22-599">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="aeb22-600">Dies führt **doppelt so lange** wie zuvor zu einem diff.</span><span class="sxs-lookup"><span data-stu-id="aeb22-600">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="aeb22-601">Dies ist ein sehr einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="aeb22-601">This is a trivial example.</span></span> <span data-ttu-id="aeb22-602">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer.</span><span class="sxs-lookup"><span data-stu-id="aeb22-602">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="aeb22-603">Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="aeb22-603">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="aeb22-604">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="aeb22-604">Guidance and conclusions</span></span>

* <span data-ttu-id="aeb22-605">Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-605">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="aeb22-606">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aeb22-606">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="aeb22-607">Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik.</span><span class="sxs-lookup"><span data-stu-id="aeb22-607">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="aeb22-608">Bevorzugen `.razor` von Dateien und zulassen, dass der Compiler die Sequenznummern behandelt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-608">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="aeb22-609">Wenn Sie keine `RenderTreeBuilder` manuelle Logik vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die in `OpenRegion` / `CloseRegion` -aufrufen verpackt sind.</span><span class="sxs-lookup"><span data-stu-id="aeb22-609">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="aeb22-610">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.</span><span class="sxs-lookup"><span data-stu-id="aeb22-610">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="aeb22-611">Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-611">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="aeb22-612">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="aeb22-612">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="aeb22-613">Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-613">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="aeb22-614">Blazor verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-614">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="aeb22-615">Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und blazor bietet den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler `.razor` erstellt, die Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-615">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="aeb22-616">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="aeb22-616">Localization</span></span>

<span data-ttu-id="aeb22-617">Blazor-Server-apps werden mithilfe der [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="aeb22-617">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="aeb22-618">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-618">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="aeb22-619">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="aeb22-619">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="aeb22-620">Cookies</span><span class="sxs-lookup"><span data-stu-id="aeb22-620">Cookies</span></span>](#cookies)
* [<span data-ttu-id="aeb22-621">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="aeb22-621">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="aeb22-622">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="aeb22-622">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="aeb22-623">Cookies</span><span class="sxs-lookup"><span data-stu-id="aeb22-623">Cookies</span></span>

<span data-ttu-id="aeb22-624">Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="aeb22-624">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="aeb22-625">Das Cookie wird von der `OnGet` -Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-625">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="aeb22-626">Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-626">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="aeb22-627">Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="aeb22-627">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="aeb22-628">Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-628">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="aeb22-629">Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="aeb22-629">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="aeb22-630">Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-630">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="aeb22-631">Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.</span><span class="sxs-lookup"><span data-stu-id="aeb22-631">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="aeb22-632">Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="aeb22-632">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="aeb22-633">Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der blazor-Server-App:</span><span class="sxs-lookup"><span data-stu-id="aeb22-633">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="aeb22-634">Die Lokalisierung wird in der APP behandelt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-634">Localization is handled in the app:</span></span>

1. <span data-ttu-id="aeb22-635">Der Browser sendet eine anfängliche http-Anforderung an die app.</span><span class="sxs-lookup"><span data-stu-id="aeb22-635">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="aeb22-636">Die Kultur wird von der Lokalisierungs Middleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-636">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="aeb22-637">Die `OnGet` -Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.</span><span class="sxs-lookup"><span data-stu-id="aeb22-637">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="aeb22-638">Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive blazor-Server Sitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-638">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="aeb22-639">Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="aeb22-639">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="aeb22-640">Die blazor-Server Sitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="aeb22-640">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="aeb22-641">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="aeb22-641">Provide UI to choose the culture</span></span>

<span data-ttu-id="aeb22-642">Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-642">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="aeb22-643">Der Prozess ähnelt der Vorgehensweise in einer Web-App, wenn ein Benutzer versucht, auf eine sichere&mdash;Ressource zuzugreifen, auf die der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück an die ursprüngliche Ressource umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="aeb22-643">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="aeb22-644">Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller.</span><span class="sxs-lookup"><span data-stu-id="aeb22-644">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="aeb22-645">Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.</span><span class="sxs-lookup"><span data-stu-id="aeb22-645">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="aeb22-646">Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:</span><span class="sxs-lookup"><span data-stu-id="aeb22-646">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="aeb22-647">Verwenden Sie `LocalRedirect` das Aktions Ergebnis, um offene Umleitungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="aeb22-647">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="aeb22-648">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="aeb22-648">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="aeb22-649">Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-649">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="aeb22-650">Verwenden von .net-Lokalisierungs Szenarien in blazor-apps</span><span class="sxs-lookup"><span data-stu-id="aeb22-650">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="aeb22-651">Innerhalb von blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:</span><span class="sxs-lookup"><span data-stu-id="aeb22-651">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="aeb22-652">. NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="aeb22-652">.NET's resources system</span></span>
* <span data-ttu-id="aeb22-653">Kulturspezifische Zahlen-und Datums Formatierung</span><span class="sxs-lookup"><span data-stu-id="aeb22-653">Culture-specific number and date formatting</span></span>

<span data-ttu-id="aeb22-654">Die Funktion von `@bind` blazor führt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch.</span><span class="sxs-lookup"><span data-stu-id="aeb22-654">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="aeb22-655">Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="aeb22-655">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="aeb22-656">Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-656">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="aeb22-657">`IStringLocalizer<>`wird in blazor-apps *unterstützt* .</span><span class="sxs-lookup"><span data-stu-id="aeb22-657">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="aeb22-658">`IHtmlLocalizer<>`die `IViewLocalizer<>`Lokalisierung von-,-und-Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in blazor-apps **nicht unterstützt** .</span><span class="sxs-lookup"><span data-stu-id="aeb22-658">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="aeb22-659">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="aeb22-659">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="aeb22-660">SVG-Bilder (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="aeb22-660">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="aeb22-661">Da blazor HTML rendert, werden vom Browser unterstützte Bilder, einschließlich der SVG-Bilder (Scalable Vector Graphics *) (SVG*), `<img>` über das-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-661">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="aeb22-662">Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="aeb22-662">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="aeb22-663">Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-663">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="aeb22-664">Wenn Sie ein `<svg>` Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb22-664">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="aeb22-665">Beispielsweise `<use>` werden Tags derzeit nicht beachtet und können `@bind` nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-665">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="aeb22-666">Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="aeb22-666">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aeb22-667">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aeb22-667">Additional resources</span></span>

* <span data-ttu-id="aeb22-668"><xref:security/blazor/server>&ndash; Enthält Anleitungen zum Entwickeln von blazor-Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="aeb22-668"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
