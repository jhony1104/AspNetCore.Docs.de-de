---
title: Erstellen und Verwenden von Razor-Komponenten
author: guardrex
description: Informationen Sie zum Erstellen und Verwenden von Razor-Komponenten, einschließlich Informationen zum Binden an Daten, Verarbeiten von Ereignissen und Lebenszyklen Komponente verwalten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/components
ms.openlocfilehash: f8ac7f3844b94a162e8d1c45f80ae153d89536ee
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515562"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="05560-103">Erstellen und Verwenden von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="05560-103">Create and use Razor Components</span></span>

<span data-ttu-id="05560-104">Durch [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), und [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="05560-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="05560-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="05560-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="05560-106">Informieren Sie sich in dem Thema [Erste Schritte](xref:razor-components/get-started) über die Voraussetzungen.</span><span class="sxs-lookup"><span data-stu-id="05560-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="05560-107">Apps für Razor-Komponenten basieren *Komponenten*.</span><span class="sxs-lookup"><span data-stu-id="05560-107">Razor Components apps are built using *components*.</span></span> <span data-ttu-id="05560-108">Eine Komponente ist als eigenständigen Teil der Benutzeroberfläche (UI), z. B. eine Seite, Dialogfeld oder Formular.</span><span class="sxs-lookup"><span data-stu-id="05560-108">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="05560-109">Eine Komponente enthält die HTML-Markup und die Verarbeitungslogik zum Einfügen von Daten oder zum Reagieren auf Ereignisse der Benutzeroberfläche erforderlich.</span><span class="sxs-lookup"><span data-stu-id="05560-109">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="05560-110">Komponenten sind flexibler und einfacher.</span><span class="sxs-lookup"><span data-stu-id="05560-110">Components are flexible and lightweight.</span></span> <span data-ttu-id="05560-111">Sie können geschachtelte, wiederverwendet und von den Projekten gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="05560-111">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="05560-112">Komponentenklassen</span><span class="sxs-lookup"><span data-stu-id="05560-112">Component classes</span></span>

<span data-ttu-id="05560-113">Komponenten werden in der Regel in Razor-Komponentendateien implementiert (*.razor*) mit einer Kombination aus C# und HTML-Markup (*.cshtml* Dateien in Blazor-apps verwendet werden).</span><span class="sxs-lookup"><span data-stu-id="05560-113">Components are typically implemented in Razor Component files (*.razor*) using a combination of C# and HTML markup (*.cshtml* files are used in Blazor apps).</span></span>

<span data-ttu-id="05560-114">Komponenten erstellt werden können, wird in Razor-Komponenten-apps, die mit der *.cshtml* Dateierweiterung aus, solange die Dateien identifiziert werden, als Komponente der Razor-Dateien, die mit der `_RazorComponentInclude` MSBuild-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="05560-114">Components can be authored in Razor Components apps using the *.cshtml* file extension as long as the files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="05560-115">Eine app, die mit der Komponente der Razor-Vorlage erstellt z. B. gibt an, dass alle *.cshtml* Dateien unter den *Komponenten* Ordner als Komponenten der Razor-Dateien behandelt werden soll:</span><span class="sxs-lookup"><span data-stu-id="05560-115">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components files:</span></span>

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="05560-116">Die Benutzeroberfläche für eine Komponente ist HTML-Format definiert.</span><span class="sxs-lookup"><span data-stu-id="05560-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="05560-117">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="05560-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="05560-118">Wenn eine app für Razor-Komponenten kompiliert wird, das HTML-Markup und C# Renderinglogik in eine Komponentenklasse konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="05560-118">When a Razor Components app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="05560-119">Der Name der generierten Klasse entspricht der Name der Datei.</span><span class="sxs-lookup"><span data-stu-id="05560-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="05560-120">Mitglieder der Komponentenklasse werden definiert, einem `@functions` Block (mehr als eine `@functions` Block ist zulässig).</span><span class="sxs-lookup"><span data-stu-id="05560-120">Members of the component class are defined in a `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="05560-121">In der `@functions` Block Komponentenstatus (Eigenschaften, Felder) wird angegeben, mit Methoden für die Behandlung von Ereignissen oder um andere Logik für die Komponente zu definieren.</span><span class="sxs-lookup"><span data-stu-id="05560-121">In the `@functions` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="05560-122">Mitglieder der Komponente können dann verwendet werden, als Teil der Komponente mit der Logik des Rendern C# Ausdrücke, die mit beginnen `@`.</span><span class="sxs-lookup"><span data-stu-id="05560-122">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="05560-123">Z. B. eine C# Feld gerendert wird, werden `@` auf den Namen des Felds.</span><span class="sxs-lookup"><span data-stu-id="05560-123">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="05560-124">Im folgenden Beispiel ergibt und rendert:</span><span class="sxs-lookup"><span data-stu-id="05560-124">The following example evaluates and renders:</span></span>

* `_headingFontStyle` <span data-ttu-id="05560-125">in den CSS-Eigenschaft-Wert für `font-style`.</span><span class="sxs-lookup"><span data-stu-id="05560-125">to the CSS property value for `font-style`.</span></span>
* `_headingText` <span data-ttu-id="05560-126">um den Inhalt der `<h1>` Element.</span><span class="sxs-lookup"><span data-stu-id="05560-126">to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="05560-127">Nachdem die Komponente ursprünglich gerendert wurde, wird die Komponente der Render-Struktur als Reaktion auf Ereignisse erneut generiert.</span><span class="sxs-lookup"><span data-stu-id="05560-127">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="05560-128">Razor-Komponenten die neue Rendering-Struktur mit dem vorherigen Beispiel vergleicht und wendet alle Änderungen, Model (DOM) des Browsers.</span><span class="sxs-lookup"><span data-stu-id="05560-128">Razor Components then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="05560-129">Integrieren von Komponenten in Razor-Seiten und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="05560-129">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="05560-130">Verwenden Sie Komponenten mit vorhandenen Razor-Seiten und MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="05560-130">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="05560-131">Besteht keine Notwendigkeit, vorhandene Seiten oder Ansichten mit Razor-Komponenten neu zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="05560-131">There's no need to rewrite existing pages or views to use Razor Components.</span></span> <span data-ttu-id="05560-132">Beim Rendern der Seite oder Ansicht Komponenten sind vorab gerenderte&dagger; zur gleichen Zeit.</span><span class="sxs-lookup"><span data-stu-id="05560-132">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="05560-133">&dagger;Serverseitige Prerendering ist für apps mit Razor-Komponenten standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="05560-133">&dagger;Server-side prerendering is enabled for Razor Components apps by default.</span></span> <span data-ttu-id="05560-134">Die clientseitige Blazor apps werden Prerendering in der bevorstehenden Version von Preview 4 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="05560-134">Client-side Blazor apps will support prerendering in the upcoming Preview 4 release.</span></span> <span data-ttu-id="05560-135">Weitere Informationen finden Sie unter [Aktualisieren von Vorlagen/Middleware für die Datei "/ MapFallbackToPage" Verwendung](https://github.com/aspnet/AspNetCore/issues/8852).</span><span class="sxs-lookup"><span data-stu-id="05560-135">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="05560-136">Um eine Komponente von einer Seite oder Ansicht zu rendern, verwenden Sie die `RenderComponentAsync<TComponent>` HTML-Hilfsmethode:</span><span class="sxs-lookup"><span data-stu-id="05560-136">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="05560-137">Komponenten von Seiten und Ansichten gerendert sind noch nicht interaktiv in die Version Preview 3.</span><span class="sxs-lookup"><span data-stu-id="05560-137">Components rendered from pages and views aren't yet interactive in the Preview 3 release.</span></span> <span data-ttu-id="05560-138">Auswählen einer Schaltfläche nicht z. B. Aufruf einer Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="05560-138">For example, selecting a button doesn't trigger a method call.</span></span> <span data-ttu-id="05560-139">Eine kommenden Vorschau wird diese Einschränkung zu beheben und Hinzufügen von Unterstützung für Rendering-Komponenten, die mit der normalen Syntax von Element- und Attributnamen.</span><span class="sxs-lookup"><span data-stu-id="05560-139">A future preview will address this limitation and add support for rendering components using the normal element and attribute syntax.</span></span>

<span data-ttu-id="05560-140">Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil "true" nicht.</span><span class="sxs-lookup"><span data-stu-id="05560-140">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="05560-141">Komponenten können keine Sicht und seitenspezifische-Szenarien, z. B. Teilansichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="05560-141">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="05560-142">Um Logik aus Teilansicht in einer Komponente, auszuklammern die Teilansicht Logik in eine Komponente zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="05560-142">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

## <a name="using-components"></a><span data-ttu-id="05560-143">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="05560-143">Using components</span></span>

<span data-ttu-id="05560-144">Komponenten können andere Komponenten einbinden, indem sie deklarieren mithilfe der Syntax von HTML-Element.</span><span class="sxs-lookup"><span data-stu-id="05560-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="05560-145">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="05560-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="05560-146">Das folgende Markup rendert eine `HeadingComponent` (*HeadingComponent.cshtml*) Instanz:</span><span class="sxs-lookup"><span data-stu-id="05560-146">The following markup renders a `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a><span data-ttu-id="05560-147">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="05560-147">Component parameters</span></span>

<span data-ttu-id="05560-148">Komponenten haben *Parameter der Komponente*, die definiert werden, mithilfe von *nicht öffentlichen* Eigenschaften für die Component-Klasse ergänzt `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="05560-148">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="05560-149">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="05560-149">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="05560-150">Im folgenden Beispiel die `ParentComponent` legt den Wert für die `Title` Eigenschaft der `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="05560-150">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="05560-151">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-151">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

<span data-ttu-id="05560-152">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-152">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a><span data-ttu-id="05560-153">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="05560-153">Child content</span></span>

<span data-ttu-id="05560-154">Komponenten können den Inhalt einer anderen Komponente festlegen.</span><span class="sxs-lookup"><span data-stu-id="05560-154">Components can set the content of another component.</span></span> <span data-ttu-id="05560-155">Zuweisen von Komponente enthält den Inhalt zwischen die Tags, die die empfangende Komponente angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-155">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="05560-156">Z. B. eine `ParentComponent` bieten Inhalte für das Rendering durch eine untergeordnete Komponente durch die Platzierung des Inhalts in `<ChildComponent>` Tags.</span><span class="sxs-lookup"><span data-stu-id="05560-156">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="05560-157">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-157">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

<span data-ttu-id="05560-158">Die untergeordnete Komponente verfügt über eine `ChildContent` dargestellten Eigenschaft eine `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="05560-158">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="05560-159">Der Wert des `ChildContent` befindet sich im Markup für die untergeordnete Komponente, in dem der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="05560-159">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="05560-160">Im folgenden Beispiel den Wert der `ChildContent` von der übergeordneten Komponente empfangen wird und innerhalb des Bereichs der Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="05560-160">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="05560-161">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-161">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> <span data-ttu-id="05560-162">Die Eigenschaft empfangen die `RenderFragment` Inhalt muss den Namen `ChildContent` gemäß der Konvention.</span><span class="sxs-lookup"><span data-stu-id="05560-162">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="05560-163">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="05560-163">Data binding</span></span>

<span data-ttu-id="05560-164">Datenbindung an Komponenten und DOM-Elemente sowohl erfolgt mithilfe der `bind` Attribut.</span><span class="sxs-lookup"><span data-stu-id="05560-164">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="05560-165">Im folgenden Beispiel wird die `ItalicsCheck` -Eigenschaft auf des Kontrollkästchens aktiviert, Status:</span><span class="sxs-lookup"><span data-stu-id="05560-165">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

<span data-ttu-id="05560-166">Wenn Sie dieses Kontrollkästchen aktiviert und deaktiviert ist, wird der Wert der Eigenschaft auf aktualisiert `true` und `false`bzw.</span><span class="sxs-lookup"><span data-stu-id="05560-166">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="05560-167">Nur, wenn die Komponente gerendert wird, nicht als Reaktion auf den Wert der Eigenschaft ändern, wird das Kontrollkästchen in der Benutzeroberfläche aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="05560-167">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="05560-168">Da Komponenten selbst rendern nach der Ausführung von Code im Ereignishandler, werden die Updates der Eigenschaft in der Regel sofort in der Benutzeroberfläche dargestellt.</span><span class="sxs-lookup"><span data-stu-id="05560-168">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="05560-169">Mithilfe von `bind` mit einem `CurrentValue` Eigenschaft (`<input bind="@CurrentValue">`) entspricht im Wesentlichen die folgenden:</span><span class="sxs-lookup"><span data-stu-id="05560-169">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue">`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

<span data-ttu-id="05560-170">Wenn die Komponente gerendert wird, die `value` des Eingabeelements stammt aus der `CurrentValue` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="05560-170">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="05560-171">Wenn der Benutzer, in das Textfeld eingibt der `onchange` Ereignis wird ausgelöst, und die `CurrentValue` -Eigenschaft auf den geänderten Wert festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="05560-171">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="05560-172">In Wirklichkeit die codegenerierung ist ein wenig komplexer, da `bind` behandelt einige Fälle, in denen typkonvertierungen werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="05560-172">In reality, the code generation is a little more complex because `bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="05560-173">Im Prinzip `bind` ordnet den aktuellen Wert eines Ausdrucks mit einem `value` -Attribut und die Handles-Änderungen, die mit den registrierten Handler.</span><span class="sxs-lookup"><span data-stu-id="05560-173">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="05560-174">Zusätzlich zu `onchange`, die Eigenschaft kann mit anderen Ereignissen wie gebunden werden `oninput` als expliziter darüber, was zum Binden an:</span><span class="sxs-lookup"><span data-stu-id="05560-174">In addition to `onchange`, the property can be bound using other events like `oninput` by being more explicit about what to bind to:</span></span>

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

<span data-ttu-id="05560-175">Im Gegensatz zu `onchange`, `oninput` ausgelöst wird, für jedes Zeichen, die in das Textfeld eingegeben wird.</span><span class="sxs-lookup"><span data-stu-id="05560-175">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

**<span data-ttu-id="05560-176">Formatzeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="05560-176">Format strings</span></span>**

<span data-ttu-id="05560-177">Die Datenbindung funktioniert mit <xref:System.DateTime> Formatzeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="05560-177">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="05560-178">Andere Formatausdrücke, z. B. Währungsdaten oder Zahlenformate, sind zu diesem Zeitpunkt nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="05560-178">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="05560-179">Die `format-value` Attribut gibt an, das Datumsformat zuweisen der `value` von der `input` Element.</span><span class="sxs-lookup"><span data-stu-id="05560-179">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="05560-180">Das Format wird auch beim Analysieren des Werts verwendet bei der ein `onchange` Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="05560-180">The format is also used to parse the value when an `onchange` event occurs.</span></span>

**<span data-ttu-id="05560-181">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="05560-181">Component parameters</span></span>**

<span data-ttu-id="05560-182">Bindung erkennt außerdem die Parameter der Komponente, wobei `bind-{property}` können einen Eigenschaftswert für Komponenten binden.</span><span class="sxs-lookup"><span data-stu-id="05560-182">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="05560-183">Die folgende Komponente verwendet `ChildComponent` und bindet die `ParentYear` Parameter aus dem übergeordneten Element der `Year` Parameter in der untergeordneten Komponente:</span><span class="sxs-lookup"><span data-stu-id="05560-183">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="05560-184">Übergeordnete Komponente:</span><span class="sxs-lookup"><span data-stu-id="05560-184">Parent component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="05560-185">Untergeordnete Komponente:</span><span class="sxs-lookup"><span data-stu-id="05560-185">Child component:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

<span data-ttu-id="05560-186">Beim Laden der `ParentComponent` erzeugt das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="05560-186">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="05560-187">Wenn der Wert des der `ParentYear` -Eigenschaft geändert wird, durch Auswählen der Schaltfläche in der `ParentComponent`, wird die `Year` Eigenschaft der `ChildComponent` wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="05560-187">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="05560-188">Der neue Wert des `Year` in der Benutzeroberfläche gerendert wird bei der `ParentComponent` gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="05560-188">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="05560-189">Die `Year` Parameter bindbar ist, da sie eine begleitende hat `YearChanged` -Ereignis, das den Typ des entspricht der `Year` Parameter.</span><span class="sxs-lookup"><span data-stu-id="05560-189">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="05560-190">Gemäß der Konvention `<ChildComponent bind-Year="@ParentYear" />` ist im Wesentlichen äquivalent zum Schreiben von Code,</span><span class="sxs-lookup"><span data-stu-id="05560-190">By convention, `<ChildComponent bind-Year="@ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

<span data-ttu-id="05560-191">Im Allgemeinen für die eine Eigenschaft gebunden werden kann, in eine entsprechende Event Handler `bind-property-event` Attribut.</span><span class="sxs-lookup"><span data-stu-id="05560-191">In general, a property can be bound to a corresponding event handler using `bind-property-event` attribute.</span></span>

## <a name="event-handling"></a><span data-ttu-id="05560-192">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="05560-192">Event handling</span></span>

<span data-ttu-id="05560-193">Razor-Komponenten bieten Funktionen für die Ereignisbehandlung.</span><span class="sxs-lookup"><span data-stu-id="05560-193">Razor Components provide event handling features.</span></span> <span data-ttu-id="05560-194">Für ein HTML-Element-Attribut mit dem Namen `on<event>` (z. B. `onclick`, `onsubmit`) mit einem Wert für typisierte Delegaten-Razor-Komponenten, der Wert des Attributs als Ereignishandler behandelt.</span><span class="sxs-lookup"><span data-stu-id="05560-194">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor Components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="05560-195">Den Namen des Attributs beginnt immer mit `on`.</span><span class="sxs-lookup"><span data-stu-id="05560-195">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="05560-196">Der folgende code Ruft die `UpdateHeading` Methode, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="05560-196">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="05560-197">Der folgende code Ruft die `CheckboxChanged` Methode, wenn Sie das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="05560-197">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="05560-198">-Ereignishandler können auch sein, asynchrone und Zurückgeben einer <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="05560-198">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="05560-199">Besteht keine Notwendigkeit, manuell aufzurufen `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="05560-199">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="05560-200">Ausnahmen werden protokolliert, wenn sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="05560-200">Exceptions are logged when they occur.</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="05560-201">Für einige Ereignisse sind ereignisspezifische Ereignisargumenttypen zulässig.</span><span class="sxs-lookup"><span data-stu-id="05560-201">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="05560-202">Wenn Sie den Zugriff auf diese Art von Ereignis nicht erforderlich ist, muss es nicht im Aufruf Methode.</span><span class="sxs-lookup"><span data-stu-id="05560-202">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="05560-203">Die Liste der unterstützten Ereignisargumente ist:</span><span class="sxs-lookup"><span data-stu-id="05560-203">The list of supported event arguments is:</span></span>

* <span data-ttu-id="05560-204">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="05560-204">UIEventArgs</span></span>
* <span data-ttu-id="05560-205">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="05560-205">UIChangeEventArgs</span></span>
* <span data-ttu-id="05560-206">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="05560-206">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="05560-207">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="05560-207">UIMouseEventArgs</span></span>

<span data-ttu-id="05560-208">Lambda-Ausdrücke können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="05560-208">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="05560-209">Häufig ist es sinnvoll, um über zusätzlichen Werten, z. B. schließen, wenn eine Gruppe von Elementen zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="05560-209">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="05560-210">Das folgende Beispiel erstellt drei Schaltflächen, von denen jedes Aufrufen `UpdateHeading` ein Ereignisargument übergeben (`UIMouseEventArgs`) und die Schaltfläche "-Nummer (`buttonNumber`) Wenn auf der Benutzeroberfläche ausgewählt:</span><span class="sxs-lookup"><span data-stu-id="05560-210">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="05560-211">Führen Sie **nicht** verwenden die Schleifenvariable (`i`) in einem `for` Schleife direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="05560-211">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="05560-212">Andernfalls wird dieselbe Variable verwendet, von allen Lambdaausdrücken verursacht `i`Wert, der in alle Lambdas identisch sein.</span><span class="sxs-lookup"><span data-stu-id="05560-212">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="05560-213">Immer der Wert in einer lokalen Variable erfasst (`buttonNumber` im vorherigen Beispiel) und anschließend.</span><span class="sxs-lookup"><span data-stu-id="05560-213">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="05560-214">Erfassen Sie Verweise auf Komponenten</span><span class="sxs-lookup"><span data-stu-id="05560-214">Capture references to components</span></span>

<span data-ttu-id="05560-215">Komponentenverweise bieten einem Möglichkeit Get einen Verweis auf eine Instanz der Komponente, damit Sie Befehle an diese Instanz ist, z. B. ausgeben können `Show` oder `Reset`.</span><span class="sxs-lookup"><span data-stu-id="05560-215">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="05560-216">Wenn einen Komponentenverweis erfassen möchten, fügen einen `ref` -Attribut auf den untergeordneten-Komponente und anschließend definieren Sie ein Feld mit dem gleichen Namen und den gleichen Typ wie die untergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="05560-216">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="05560-217">Wenn die Komponente gerendert wird, die `loginDialog` Feld wird aufgefüllt, mit der `MyLoginDialog` untergeordneten Komponenteninstanz.</span><span class="sxs-lookup"><span data-stu-id="05560-217">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="05560-218">Sie können dann .NET Methoden auf die Instanz der Komponente aufrufen.</span><span class="sxs-lookup"><span data-stu-id="05560-218">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="05560-219">Die `loginDialog` Variable wird nur aufgefüllt, nachdem die Komponente gerendert wird und die Ausgabe enthält die `MyLoginDialog` Element.</span><span class="sxs-lookup"><span data-stu-id="05560-219">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="05560-220">Bis zu diesem Zeitpunkt ist es nichts zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="05560-220">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="05560-221">Verwenden, um Komponenten Verweise zu bearbeiten, nachdem die Komponente vollständig gerendert wurde die `OnAfterRenderAsync` oder `OnAfterRender` Methoden.</span><span class="sxs-lookup"><span data-stu-id="05560-221">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="05560-222">Während eine ähnliche Syntax zum Erfassen von komponentenverweisen verwendet werden. [erfassen Elementverweise](xref:razor-components/javascript-interop#capture-references-to-elements), ist es keine [JavaScript-Interop](xref:razor-components/javascript-interop) Feature.</span><span class="sxs-lookup"><span data-stu-id="05560-222">While capturing component references uses a similar syntax to [capturing element references](xref:razor-components/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:razor-components/javascript-interop) feature.</span></span> <span data-ttu-id="05560-223">Komponentenverweise sind nicht für JavaScript-Code übergeben. Sie werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="05560-223">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="05560-224">Führen Sie **nicht** Komponente verweist, geändert wird, den Status der untergeordneten Komponenten verwenden.</span><span class="sxs-lookup"><span data-stu-id="05560-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="05560-225">Verwenden Sie stattdessen normalen deklarative Parameter, um Daten an den untergeordneten Komponenten übergeben.</span><span class="sxs-lookup"><span data-stu-id="05560-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="05560-226">Dies bewirkt, dass untergeordneten Komponenten, die zum korrekten Zeitpunkt automatisch erneut rendern.</span><span class="sxs-lookup"><span data-stu-id="05560-226">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="05560-227">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="05560-227">Lifecycle methods</span></span>

`OnInitAsync` <span data-ttu-id="05560-228">und `OnInit` Ausführen von Code zum Initialisieren der Komponente.</span><span class="sxs-lookup"><span data-stu-id="05560-228">and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="05560-229">Verwenden Sie zum Ausführen eines asynchronen Vorgangs `OnInitAsync` und `await` Schlüsselwort für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="05560-229">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="05560-230">Verwenden Sie für einen synchronen Vorgang `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="05560-230">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` <span data-ttu-id="05560-231">und `OnParametersSet` werden aufgerufen, wenn eine Komponente Parameter, aus dem übergeordneten Element empfangen hat und die Werte an Eigenschaften zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="05560-231">and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="05560-232">Diese Methoden werden nach der komponenteninitialisierung ausgeführt, und klicken Sie dann jedes Mal die Komponente wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="05560-232">These methods are executed after component initialization and then each time the component is rendered:</span></span>

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

`OnAfterRenderAsync` <span data-ttu-id="05560-233">und `OnAfterRender` werden aufgerufen, nachdem eine Komponente vollständig gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="05560-233">and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="05560-234">Datenelement und Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="05560-234">Element and component references are populated at this point.</span></span> <span data-ttu-id="05560-235">Verwenden Sie diese Phase, um zusätzliche Initialisierung Schritte mit den gerenderten Inhalt, z. B. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die für die gerenderte DOM-Elemente verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="05560-235">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` <span data-ttu-id="05560-236">kann überschrieben werden, um Code auszuführen, bevor die Parameter festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="05560-236">can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="05560-237">Wenn `base.SetParameters` wird nicht aufgerufen, der benutzerdefinierte Code kann zu interpretieren eingehende Parameter in einer Weise, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="05560-237">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="05560-238">Die eingehende Parameter sind beispielsweise nicht erforderlich, um die Eigenschaften der Klasse zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="05560-238">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

`ShouldRender` <span data-ttu-id="05560-239">kann überschrieben werden, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="05560-239">can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="05560-240">Wenn die Implementierung gibt `true`, die Benutzeroberfläche aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="05560-240">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="05560-241">Auch wenn `ShouldRender` wird außer Kraft gesetzt, wird immer zuerst die Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="05560-241">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="05560-242">Freigabe von Komponenten mit "IDisposable"</span><span class="sxs-lookup"><span data-stu-id="05560-242">Component disposal with IDisposable</span></span>

<span data-ttu-id="05560-243">Wenn eine Komponente implementiert <xref:System.IDisposable>, [Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose) wird aufgerufen, wenn die Komponente, die von der Benutzeroberfläche entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="05560-243">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="05560-244">Die folgende Komponente verwendet `@implements IDisposable` und `Dispose` Methode:</span><span class="sxs-lookup"><span data-stu-id="05560-244">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="05560-245">Routing</span><span class="sxs-lookup"><span data-stu-id="05560-245">Routing</span></span>

<span data-ttu-id="05560-246">Routing in der Razor-Komponenten erfolgt durch die Bereitstellung einer routenvorlage jeder Komponente zugegriffen werden kann, in der app.</span><span class="sxs-lookup"><span data-stu-id="05560-246">Routing in Razor Components is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="05560-247">Wenn eine *cshtml* -Datei mit ein `@page` Richtlinie wird kompiliert, wird die generierte Klasse wird eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> die routenvorlage angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-247">When a *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="05560-248">Zur Laufzeit sucht der Router Komponentenklassen mit einem `RouteAttribute` und rendert, welche Komponente eine routenvorlage verfügt, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="05560-248">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="05560-249">Mehrere routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="05560-249">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="05560-250">Die folgende Komponente antwortet auf Anfragen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="05560-250">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="05560-251">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="05560-251">Route parameters</span></span>

<span data-ttu-id="05560-252">Komponenten können Routenparameter erhalten, von der routenvorlage angegeben werden, der `@page` Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="05560-252">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="05560-253">Der Router verwendet Routenparameter, um die entsprechende Komponentenparameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="05560-253">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="05560-254">*RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-254">*RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

<span data-ttu-id="05560-255">Optionale Parameter werden nicht unterstützt, daher werden zwei `@page` Anweisungen sind im obigen Beispiel angewendet.</span><span class="sxs-lookup"><span data-stu-id="05560-255">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="05560-256">Die erste ermöglicht die Navigation zu der Komponente ohne Parameter.</span><span class="sxs-lookup"><span data-stu-id="05560-256">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="05560-257">Die zweite `@page` Direktive nimmt die `{text}` Routenparameter und weist den Wert der `Text` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="05560-257">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="05560-258">Vererbung der Basisklasse für eine Umgebung "Code-Behind"</span><span class="sxs-lookup"><span data-stu-id="05560-258">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="05560-259">Komponentendateien (*.cshtml*) Mischen von HTML-Markup und C# Verarbeitung von Code in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="05560-259">Component files (*.cshtml*) mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="05560-260">Die `@inherits` Richtlinie kann verwendet werden, um apps mit Razor-Komponenten "Code-Behind" nutzen können, die Komponente Markup von Verarbeitungscode trennt.</span><span class="sxs-lookup"><span data-stu-id="05560-260">The `@inherits` directive can be used to provide Razor Components apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="05560-261">Die [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) zeigt, wie eine Komponente eine Basisklasse erben kann `BlazorRocksBase`, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="05560-261">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="05560-262">*BlazorRocks.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-262">*BlazorRocks.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

<span data-ttu-id="05560-263">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="05560-263">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="05560-264">Die Basisklasse ableiten sollten `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="05560-264">The base class should derive from `ComponentBase`.</span></span>

## <a name="razor-support"></a><span data-ttu-id="05560-265">Razor-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="05560-265">Razor support</span></span>

**<span data-ttu-id="05560-266">Razor-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="05560-266">Razor directives</span></span>**

<span data-ttu-id="05560-267">Razor-Anweisungen werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="05560-267">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="05560-268">Direktive</span><span class="sxs-lookup"><span data-stu-id="05560-268">Directive</span></span> | <span data-ttu-id="05560-269">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="05560-269">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="05560-270">\@Funktionen</span><span class="sxs-lookup"><span data-stu-id="05560-270">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="05560-271">Fügt eine C# Codeblock, um eine Komponente.</span><span class="sxs-lookup"><span data-stu-id="05560-271">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="05560-272">Implementiert eine Schnittstelle für die generierte Component-Klasse.</span><span class="sxs-lookup"><span data-stu-id="05560-272">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="05560-273">\@Erbt</span><span class="sxs-lookup"><span data-stu-id="05560-273">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="05560-274">Bietet vollständige Kontrolle über die Klasse, die Komponente erbt.</span><span class="sxs-lookup"><span data-stu-id="05560-274">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="05560-275">\@inject</span><span class="sxs-lookup"><span data-stu-id="05560-275">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="05560-276">Ermöglicht service Injection aus der [Dienstcontainer](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="05560-276">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="05560-277">Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="05560-277">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="05560-278">Gibt eine Layoutkomponente.</span><span class="sxs-lookup"><span data-stu-id="05560-278">Specifies a layout component.</span></span> <span data-ttu-id="05560-279">Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="05560-279">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="05560-280">\@Seite "</span><span class="sxs-lookup"><span data-stu-id="05560-280">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="05560-281">Gibt an, dass die Komponente Anforderungen direkt verarbeiten soll.</span><span class="sxs-lookup"><span data-stu-id="05560-281">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="05560-282">Die `@page` Richtlinie kann mit einer Route und optionale Parameter angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="05560-282">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="05560-283">Im Gegensatz zu Razor Pages die `@page` Richtlinie muss nicht die erste Anweisung am Anfang der Datei sein.</span><span class="sxs-lookup"><span data-stu-id="05560-283">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="05560-284">Weitere Informationen finden Sie unter [Routing](xref:razor-components/routing).</span><span class="sxs-lookup"><span data-stu-id="05560-284">For more information, see [Routing](xref:razor-components/routing).</span></span> |
| [<span data-ttu-id="05560-285">\@Mithilfe von</span><span class="sxs-lookup"><span data-stu-id="05560-285">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="05560-286">Fügt der C# `using` Anweisung der generierten Component-Klasse.</span><span class="sxs-lookup"><span data-stu-id="05560-286">Adds the C# `using` directive to the generated component class.</span></span> |
| [<span data-ttu-id="05560-287">\@addTagHelper</span><span class="sxs-lookup"><span data-stu-id="05560-287">\@addTagHelper</span></span>](xref:mvc/views/razor#tag-helpers) | <span data-ttu-id="05560-288">Verwenden Sie `@addTagHelper` Komponente in einer anderen Assembly als die Assembly der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="05560-288">Use `@addTagHelper` to use a component in a different assembly than the app's assembly.</span></span> |

**<span data-ttu-id="05560-289">Bedingte Attribute</span><span class="sxs-lookup"><span data-stu-id="05560-289">Conditional attributes</span></span>**

<span data-ttu-id="05560-290">Attribute werden bedingt gerendert basierend auf dem .NET-Wert.</span><span class="sxs-lookup"><span data-stu-id="05560-290">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="05560-291">Wenn der Wert ist `false` oder `null`, das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="05560-291">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="05560-292">Wenn der Wert ist `true`, das Attribut gerendert wird minimiert.</span><span class="sxs-lookup"><span data-stu-id="05560-292">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="05560-293">Im folgenden Beispiel `IsCompleted` bestimmt, ob `checked` wird in das Markup des Steuerelements gerendert:</span><span class="sxs-lookup"><span data-stu-id="05560-293">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="05560-294">Wenn `IsCompleted` ist `true`, als Sie das Kontrollkästchen gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="05560-294">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked>
```

<span data-ttu-id="05560-295">Wenn `IsCompleted` ist `false`, als Sie das Kontrollkästchen gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="05560-295">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox">
```

**<span data-ttu-id="05560-296">Weitere Informationen zu Razor</span><span class="sxs-lookup"><span data-stu-id="05560-296">Additional information on Razor</span></span>**

<span data-ttu-id="05560-297">Weitere Informationen zu Razor, finden Sie unter den [Razor-syntaxverweis](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="05560-297">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="05560-298">Unformatiertes HTML</span><span class="sxs-lookup"><span data-stu-id="05560-298">Raw HTML</span></span>

<span data-ttu-id="05560-299">Zeichenfolgen werden normal gerendert mithilfe von DOM-Knoten, was bedeutet, dass alle darin enthaltenen möglicherweise Markup wird ignoriert und als Literaltext behandelt.</span><span class="sxs-lookup"><span data-stu-id="05560-299">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="05560-300">Um unformatiertes HTML zu rendern, umschließen Sie den HTML-Inhalt in einem `MarkupString` Wert.</span><span class="sxs-lookup"><span data-stu-id="05560-300">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="05560-301">Der Wert wird als HTML- oder SVG analysiert und in das DOM eingefügt</span><span class="sxs-lookup"><span data-stu-id="05560-301">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="05560-302">Rendern von unformatierten HTML-erstellt aus jeder von nicht vertrauenswürdigen Quelle ist eine **Sicherheitsrisiko** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="05560-302">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="05560-303">Das folgende Beispiel zeigt die Verwendung der `MarkupString` Typ um einen Block mit statischem HTML-Inhalt in die gerenderte Ausgabe einer Komponente hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="05560-303">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="05560-304">Auf Vorlagen basierende Komponenten</span><span class="sxs-lookup"><span data-stu-id="05560-304">Templated components</span></span>

<span data-ttu-id="05560-305">Auf Vorlagen basierende Komponenten sind Komponenten, die eine oder mehrere UI-Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="05560-305">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="05560-306">Auf Vorlagen basierende Komponenten können Sie Komponenten höherer Ebene zu erstellen, die als reguläre Komponenten besser wiederverwendbar sind.</span><span class="sxs-lookup"><span data-stu-id="05560-306">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="05560-307">Einige Beispiele enthalten:</span><span class="sxs-lookup"><span data-stu-id="05560-307">A couple of examples include:</span></span>

* <span data-ttu-id="05560-308">Eine Tabelle-Komponente, die einem Benutzer ermöglicht, Vorlagen für Header, Zeilen und Fußzeile der Tabelle angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-308">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="05560-309">Eine Listenkomponente, die einem Benutzer ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-309">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="05560-310">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="05560-310">Template parameters</span></span>

<span data-ttu-id="05560-311">Eine auf Vorlagen basierende Komponente wird definiert, indem Sie eine oder mehrere Parameter für die Komponente vom Typ `RenderFragment` oder `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="05560-311">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="05560-312">Ein Rendering-Fragment stellt ein Segment der Benutzeroberfläche, die von der Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="05560-312">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="05560-313">Ein Fragment gerendert hat optional einen Parameter, der aufgerufen wird, der Rendering-Fragment angegeben werden kann.</span><span class="sxs-lookup"><span data-stu-id="05560-313">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="05560-314">*Components/TableTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-314">*Components/TableTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

<span data-ttu-id="05560-315">Wenn Sie eine auf Vorlagen basierende Komponente zu verwenden, die Parameter der Vorlage können angegeben werden von untergeordneten Elementen, die den Namen der Parameter übereinstimmen (`TableHeader` und `RowTemplate` im folgenden Beispiel):</span><span class="sxs-lookup"><span data-stu-id="05560-315">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="05560-316">Kontext-Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="05560-316">Template context parameters</span></span>

<span data-ttu-id="05560-317">Komponente-Argumenten des Typs `RenderFragment<T>` übergeben, da Elemente einen impliziten Parameter mit dem Namen `context` (z. B. aus dem vorherigen Codebeispiel `@context.PetId`), aber Sie können ändern, den Parameter mit der `Context` Attribut für das untergeordnete Element Element.</span><span class="sxs-lookup"><span data-stu-id="05560-317">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="05560-318">Im folgenden Beispiel die `RowTemplate` des Elements `Context` -Attribut gibt an, die `pet` Parameter:</span><span class="sxs-lookup"><span data-stu-id="05560-318">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
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

<span data-ttu-id="05560-319">Alternativ können Sie angeben der `Context` Attributs im Komponentenelement.</span><span class="sxs-lookup"><span data-stu-id="05560-319">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="05560-320">Das angegebene `Context` Attribut gilt für alle Parameter der angegebenen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="05560-320">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="05560-321">Dies kann nützlich sein, wenn der Inhalt Parametername für implizite untergeordneten Inhalt angeben möchten (nur mit jedem Zeilenumbruch untergeordnetes Element).</span><span class="sxs-lookup"><span data-stu-id="05560-321">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="05560-322">Im folgenden Beispiel die `Context` Attribut angezeigt wird, auf die `TableTemplate` Element und gilt für alle Parameter der Vorlage:</span><span class="sxs-lookup"><span data-stu-id="05560-322">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="05560-323">Typisierte generische Komponenten</span><span class="sxs-lookup"><span data-stu-id="05560-323">Generic-typed components</span></span>

<span data-ttu-id="05560-324">Auf Vorlagen basierende Komponenten sind häufig generisch typisiert.</span><span class="sxs-lookup"><span data-stu-id="05560-324">Templated components are often generically typed.</span></span> <span data-ttu-id="05560-325">Beispielsweise kann eine generische Liste Ansichtsvorlage-Komponente zum Rendern verwendet `IEnumerable<T>` Werte.</span><span class="sxs-lookup"><span data-stu-id="05560-325">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="05560-326">Verwenden Sie zum Definieren einer generischen Komponente der `@typeparam` Direktive, um die Typparameter angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-326">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="05560-327">*Components/ListViewTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-327">*Components/ListViewTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

<span data-ttu-id="05560-328">Wenn Sie typisierte generische Komponenten zu verwenden, wird der Typparameter möglichst abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="05560-328">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="05560-329">Andernfalls muss der Typparameter explizit angegeben werden unter Verwendung eines Attributs, das mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="05560-329">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="05560-330">Im folgenden Beispiel `TItem="Pet"` gibt den Typ an:</span><span class="sxs-lookup"><span data-stu-id="05560-330">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="05560-331">Kaskadierende Rückgabewerte und Parameter</span><span class="sxs-lookup"><span data-stu-id="05560-331">Cascading values and parameters</span></span>

<span data-ttu-id="05560-332">In einigen Szenarien ist es unpraktisch für den Datenfluss aus einer übergeordneten Komponente in eine untergeordnete Komponente [Parameter der Komponente](#component-parameters), besonders wenn es mehrere Ebenen der Komponente.</span><span class="sxs-lookup"><span data-stu-id="05560-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="05560-333">Lösen dieses Problem durch die Bereitstellung einer praktische Methode für eine Vorgänger-Komponente, um einen Wert für alle seine untergeordneten Komponenten bereitzustellen, Cascading Rückgabewerte und Parameter.</span><span class="sxs-lookup"><span data-stu-id="05560-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="05560-334">Kaskadierende Rückgabewerte und Parameter bieten auch einen Ansatz für Komponenten zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="05560-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="05560-335">Design-Beispiel</span><span class="sxs-lookup"><span data-stu-id="05560-335">Theme example</span></span>

<span data-ttu-id="05560-336">In der folgenden *Design* Beispiel aus der Beispiel-app die `ThemeInfo` Klasse gibt an, das Designinformationen zur Übertragung in der Komponentenhierarchie, sodass alle Schaltflächen innerhalb eines bestimmten Teils der app im gleichen Stil freigeben.</span><span class="sxs-lookup"><span data-stu-id="05560-336">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="05560-337">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="05560-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="05560-338">Eine Vorgänger-Komponente kann es sich um einen kaskadierende Wert mithilfe der Komponente der kaskadierende Wert angeben.</span><span class="sxs-lookup"><span data-stu-id="05560-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="05560-339">Die Komponente der kaskadierende Wert dient als Wrapper für eine Unterstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur.</span><span class="sxs-lookup"><span data-stu-id="05560-339">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="05560-340">Die Beispiel-app gibt z. B. Designinformationen (`ThemeInfo`) in einem von der app-Layouts als kaskadierenden Parameter für alle Komponenten, aus denen der Layout-Text, der die `@Body` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="05560-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> `ButtonClass` <span data-ttu-id="05560-341">einen Wert zugewiesen `btn-success` in der Layoutkomponente.</span><span class="sxs-lookup"><span data-stu-id="05560-341">is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="05560-342">Eine untergeordnete Komponente kann diese Eigenschaft durch Nutzen der `ThemeInfo` cascading-Objekt.</span><span class="sxs-lookup"><span data-stu-id="05560-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="05560-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="05560-344">Vornehmen von kaskadierenden Werten verwenden, die Komponenten deklarieren kaskadierende Parameter mithilfe der `[CascadingParameter]` Attribut oder basierend auf einem Zeichenfolge-Name-Wert:</span><span class="sxs-lookup"><span data-stu-id="05560-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="05560-345">Bindung mit einem Namen Zeichenfolgenwert ist relevant, wenn Sie mehrere kaskadierende Werte vom gleichen Typ aufweisen und innerhalb der gleichen Teilstruktur unterscheiden müssen.</span><span class="sxs-lookup"><span data-stu-id="05560-345">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="05560-346">Kaskadierender Werte werden an kaskadierende Parameter vom Typ gebunden.</span><span class="sxs-lookup"><span data-stu-id="05560-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="05560-347">In der Beispiel-app, bindet die kaskadierenden Werten Parameter Design-Komponente an die `ThemeInfo` kaskadierende Wert, der einem kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="05560-347">In the sample app, the Cascading Values Parameters Theme component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="05560-348">Der Parameter wird verwendet, die CSS-Klasse für eine der Schaltflächen angezeigt, die von der Komponente festlegen.</span><span class="sxs-lookup"><span data-stu-id="05560-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="05560-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="05560-350">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="05560-350">TabSet example</span></span>

<span data-ttu-id="05560-351">Kaskadierende Parameter ermöglichen auch die Komponenten für die Zusammenarbeit in der Komponentenhierarchie.</span><span class="sxs-lookup"><span data-stu-id="05560-351">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="05560-352">Betrachten Sie beispielsweise die folgenden *TabSet* Beispiel in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="05560-352">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="05560-353">Die Beispielapp verfügt über eine `ITab` -Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="05560-353">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="05560-354">Die kaskadierenden Werten Parameter TabSet-Komponente verwendet die legen Sie die Registerkarte "-Komponente, die mehrere Registerkarte Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="05560-354">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

<span data-ttu-id="05560-355">Der untergeordneten Registerkarte Komponenten werden nicht explizit als Parameter übergeben, auf der Registerkarte "festgelegt.</span><span class="sxs-lookup"><span data-stu-id="05560-355">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="05560-356">Stattdessen werden die Komponenten der untergeordneten Registerkarte Teil des untergeordneten Inhalts des legen Sie die Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="05560-356">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="05560-357">Allerdings muss legen Sie die Registerkarte noch jede Registerkarte Komponente kennen, damit er die Header und der aktiven Registerkarte rendern kann. Zu diesem Zweck zu aktivieren, ohne zusätzlichen Code, der Komponente legen Sie die Registerkarte *bieten sich selbst als kaskadierenden Wert* , wird dann von der untergeordneten Registerkarte Komponenten übernommen.</span><span class="sxs-lookup"><span data-stu-id="05560-357">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="05560-358">*Components/TabSet.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-358">*Components/TabSet.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

<span data-ttu-id="05560-359">Die Erfassung der untergeordneten Registerkarte Komponenten festlegen als kaskadierenden Parameter, die mit Registerkarte die Registerkarte "-Komponenten selbst auf die Registerkarte" festgelegt und die Koordinate hinzufügen auf der Registerkarte ist aktiv.</span><span class="sxs-lookup"><span data-stu-id="05560-359">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="05560-360">*Components/Tab.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-360">*Components/Tab.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a><span data-ttu-id="05560-361">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="05560-361">Razor templates</span></span>

<span data-ttu-id="05560-362">Rendern von Fragmenten können mithilfe der Syntax für Razor-Vorlage definiert werden.</span><span class="sxs-lookup"><span data-stu-id="05560-362">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="05560-363">Razor-Vorlagen sind eine Möglichkeit, einen UI-Codeausschnitt definiert und das folgende Format angenommen:</span><span class="sxs-lookup"><span data-stu-id="05560-363">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="05560-364">Im folgende Beispiel wird veranschaulicht, wie an `RenderFragment` und `RenderFragment<T>` Werte.</span><span class="sxs-lookup"><span data-stu-id="05560-364">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="05560-365">*RazorTemplates.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="05560-365">*RazorTemplates.cshtml*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="05560-366">Rendern Sie Fragmente mit Razor-Vorlagen, die auf Vorlagen basierende Komponenten als Argumente übergeben oder direkt gerendert werden können, definiert.</span><span class="sxs-lookup"><span data-stu-id="05560-366">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="05560-367">Beispielsweise werden die vorherigen Vorlagen direkt mit dem folgenden Razor-Markup gerendert:</span><span class="sxs-lookup"><span data-stu-id="05560-367">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="05560-368">Gerenderte Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="05560-368">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="05560-369">Manuelle RenderTreeBuilder Logik</span><span class="sxs-lookup"><span data-stu-id="05560-369">Manual RenderTreeBuilder logic</span></span>

`Microsoft.AspNetCore.Components.RenderTree` <span data-ttu-id="05560-370">Stellt Methoden zum Bearbeiten von Komponenten und Elemente, einschließlich Erstellen von Komponenten manuell in C# Code.</span><span class="sxs-lookup"><span data-stu-id="05560-370">provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="05560-371">Verwenden von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="05560-371">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="05560-372">Eine fehlerhafte Komponente (z. B. eine nicht geschlossene Markuptag) kann zu nicht definiertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="05560-372">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="05560-373">Betrachten Sie die folgende Pet-Details-Komponente (*PetDetails.razor* in Razor-Komponenten; *PetDetails.cshtml* in Blazor), die manuell in eine andere Komponente erstellt werden kann:</span><span class="sxs-lookup"><span data-stu-id="05560-373">Consider the following Pet Details component (*PetDetails.razor* in Razor Components; *PetDetails.cshtml* in Blazor), which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="05560-374">Im folgenden Beispiel der Schleife in der `CreateComponent` Methode generiert drei Pet-Details-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="05560-374">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="05560-375">Beim Aufrufen von `RenderTreeBuilder` Methoden zum Erstellen der Komponenten (`OpenComponent` und `AddAttribute`), sind die Sequenznummern Source Code Zeilennummern.</span><span class="sxs-lookup"><span data-stu-id="05560-375">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="05560-376">Die Razor-Komponenten, die die Sequenznummern, die für unterschiedliche Codezeilen nicht unterschiedliche Unterschied-Algorithmus basiert aufrufen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="05560-376">The Razor Components difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="05560-377">Beim Erstellen einer Komponente mit `RenderTreeBuilder` Methoden, die hartcodiert die Argumente für Sequenznummern.</span><span class="sxs-lookup"><span data-stu-id="05560-377">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> **<span data-ttu-id="05560-378">Verwenden eine Berechnung oder einen Indikator, um die Sequenznummer zu generieren, kann zu Leistungseinbußen führen.</span><span class="sxs-lookup"><span data-stu-id="05560-378">Using a calculation or counter to generate the sequence number can lead to poor performance.</span></span>**

<span data-ttu-id="05560-379">Erstellt die Komponente (*BuiltContent.razor* in Razor-Komponenten; *BuiltContent.cshtml* in Blazor):</span><span class="sxs-lookup"><span data-stu-id="05560-379">Built component (*BuiltContent.razor* in Razor Components; *BuiltContent.cshtml* in Blazor):</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
