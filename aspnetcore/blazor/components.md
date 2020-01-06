---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/28/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: 87f21d84c17e5bbd1247bb955acee81384b890e7
ms.sourcegitcommit: 47d453f34b6fd0179119c572cb8be64c5365cbb6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75597901"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="26bb0-103">Erstellen und Verwenden von ASP.net Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="26bb0-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="26bb0-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="26bb0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="26bb0-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26bb0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="26bb0-106">-apps werden mithilfe von- *Komponenten*erstellt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-106"> apps are built using *components*.</span></span> <span data-ttu-id="26bb0-107">Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="26bb0-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="26bb0-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="26bb0-109">Komponenten sind flexibel und leicht zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="26bb0-110">Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="26bb0-111">Komponenten Klassen</span><span class="sxs-lookup"><span data-stu-id="26bb0-111">Component classes</span></span>

<span data-ttu-id="26bb0-112">Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="26bb0-113">Eine Komponente in Blazor wird formal als *Razor-Komponente*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="26bb0-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="26bb0-115">Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="26bb0-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="26bb0-116">Die Benutzeroberfläche für eine Komponente wird mit HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="26bb0-117">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="26bb0-118">Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="26bb0-119">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="26bb0-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="26bb0-120">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="26bb0-121">Im `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="26bb0-122">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="26bb0-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="26bb0-123">In früheren Vorschauen von ASP.net Core 3,0 wurden `@functions` Blöcke für denselben Zweck verwendet wie `@code` Blöcke in Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="26bb0-124">`@functions` Blöcke funktionieren weiterhin in Razor-Komponenten, aber es wird empfohlen, den `@code`-Block in ASP.net Core 3,0 Preview 6 oder höher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="26bb0-125">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="26bb0-126">Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="26bb0-127">Im folgenden Beispiel wird ausgewertet und gerendert:</span><span class="sxs-lookup"><span data-stu-id="26bb0-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="26bb0-128">`_headingFontStyle` zum CSS-Eigenschafts Wert für `font-style`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="26bb0-129">`_headingText` auf den Inhalt des `<h1>` Elements.</span><span class="sxs-lookup"><span data-stu-id="26bb0-129">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="26bb0-130">Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="26bb0-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="26bb0-131"> vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen auf die Dokumentobjektmodell des Browsers (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="26bb0-131"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="26bb0-132">Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="26bb0-133">Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="26bb0-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="26bb0-134">Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="26bb0-135">Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports Razor* -Datei der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="26bb0-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="26bb0-136">Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `WebApplication`ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```razor
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="26bb0-137">Integrieren von Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="26bb0-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="26bb0-138">Verwenden Sie Komponenten mit vorhandenen Razor Pages und MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="26bb0-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="26bb0-139">Es ist nicht erforderlich, vorhandene Seiten oder Sichten für die Verwendung von Razor-Komponenten neu zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="26bb0-140">Wenn die Seite oder die Ansicht gerendert wird, werden die Komponenten gleichzeitig vorab in eine Vorabversion übernommen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="26bb0-141">Verwenden Sie das `Component`-taghilfsprogramm, um eine Komponente aus einer Seite oder Sicht zu Rendering:</span><span class="sxs-lookup"><span data-stu-id="26bb0-141">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="26bb0-142">Das übergeben von Parametern (z. b. `IncrementAmount` im vorangehenden Beispiel) wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-142">Passing parameters (for example, `IncrementAmount` in the preceding example) is supported.</span></span>

<span data-ttu-id="26bb0-143">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="26bb0-143">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="26bb0-144">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-144">Is prerendered into the page.</span></span>
* <span data-ttu-id="26bb0-145">Wird auf der Seite als statischer HTML-Code gerendert, oder, wenn er die erforderlichen Informationen zum Bootstrapping einer Blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="26bb0-145">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="26bb0-146">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="26bb0-146">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="26bb0-147">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-147">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="26bb0-148">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-148">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="26bb0-149">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-149">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="26bb0-150">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-150">Output from the component isn't included.</span></span> <span data-ttu-id="26bb0-151">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-151">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="26bb0-152">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="26bb0-152">Renders the component into static HTML.</span></span> |

<span data-ttu-id="26bb0-153">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="26bb0-153">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="26bb0-154">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="26bb0-154">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="26bb0-155">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-155">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="26bb0-156">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-156">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="26bb0-157">Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `Component`-taghilfsprogramm finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-157">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

<span data-ttu-id="26bb0-158">Verwenden Sie die `RenderComponentAsync<TComponent>` HTML-Hilfsmethode, um eine Komponente aus einer Seite oder Sicht zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-158">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

<span data-ttu-id="26bb0-159">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="26bb0-159">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="26bb0-160">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-160">Is prerendered into the page.</span></span>
* <span data-ttu-id="26bb0-161">Wird auf der Seite als statischer HTML-Code gerendert, oder, wenn er die erforderlichen Informationen zum Bootstrapping einer Blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="26bb0-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="26bb0-162">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="26bb0-162">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="26bb0-163">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-163">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="26bb0-164">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-164">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="26bb0-165">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-165">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="26bb0-166">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="26bb0-167">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-167">Output from the component isn't included.</span></span> <span data-ttu-id="26bb0-168">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="26bb0-169">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-169">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="26bb0-170">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="26bb0-170">Renders the component into static HTML.</span></span> <span data-ttu-id="26bb0-171">Parameter werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-171">Parameters are supported.</span></span> |

<span data-ttu-id="26bb0-172">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="26bb0-172">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="26bb0-173">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="26bb0-173">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="26bb0-174">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-174">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="26bb0-175">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-175">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="26bb0-176">Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `RenderComponentAsync` HTML-Hilfsobjekt finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-176">For more information on how components are rendered, component state, and the `RenderComponentAsync` HTML Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

## <a name="use-components"></a><span data-ttu-id="26bb0-177">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="26bb0-177">Use components</span></span>

<span data-ttu-id="26bb0-178">Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-178">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="26bb0-179">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-179">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="26bb0-180">Bei der Attribut Bindung wird Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="26bb0-180">Attribute binding is case sensitive.</span></span> <span data-ttu-id="26bb0-181">Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="26bb0-181">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="26bb0-182">Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent` Instanz:</span><span class="sxs-lookup"><span data-stu-id="26bb0-182">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="26bb0-183">*Komponenten/headingcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-183">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="26bb0-184">Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-184">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="26bb0-185">Durch das Hinzufügen einer `@using`-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-185">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="26bb0-186">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-186">Component parameters</span></span>

<span data-ttu-id="26bb0-187">Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-187">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="26bb0-188">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-188">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="26bb0-189">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-189">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="26bb0-190">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft der `ChildComponent`fest.</span><span class="sxs-lookup"><span data-stu-id="26bb0-190">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="26bb0-191">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-191">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="26bb0-192">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="26bb0-192">Child content</span></span>

<span data-ttu-id="26bb0-193">Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-193">Components can set the content of another component.</span></span> <span data-ttu-id="26bb0-194">Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-194">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="26bb0-195">Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-195">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="26bb0-196">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="26bb0-196">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="26bb0-197">Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`des Bootstrap-Panels gerendert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-197">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="26bb0-198">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-198">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="26bb0-199">Die Eigenschaft, die den `RenderFragment` Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-199">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="26bb0-200">Der `ParentComponent` in der Beispiel-App kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>` Tags platziert wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-200">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="26bb0-201">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-201">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="26bb0-202">Attribut-Verteilung und beliebige Parameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-202">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="26bb0-203">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering.</span><span class="sxs-lookup"><span data-stu-id="26bb0-203">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="26bb0-204">Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`](xref:mvc/views/razor#attributes) Razor-Direktive gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-204">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="26bb0-205">Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-205">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="26bb0-206">Beispielsweise kann es mühsam sein, Attribute für eine `<input>`, die viele Parameter unterstützt, separat zu definieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-206">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="26bb0-207">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attribut-splatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-207">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="26bb0-208">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichen folgen Schlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-208">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="26bb0-209">Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch eine Option.</span><span class="sxs-lookup"><span data-stu-id="26bb0-209">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="26bb0-210">Die gerenderten `<input>` Elemente mit beiden Ansätzen sind identisch:</span><span class="sxs-lookup"><span data-stu-id="26bb0-210">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="26bb0-211">Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter mithilfe des `[Parameter]`-Attributs, wobei die `CaptureUnmatchedValues`-Eigenschaft auf `true`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-211">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="26bb0-212">Die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` ermöglicht dem Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-212">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="26bb0-213">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-213">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="26bb0-214">Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichen folgen Schlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="26bb0-214">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="26bb0-215">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.</span><span class="sxs-lookup"><span data-stu-id="26bb0-215">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="26bb0-216">Die Position `@attributes` relativ zur Position von Element Attributen ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="26bb0-216">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="26bb0-217">Wenn `@attributes` auf dem Element splatted sind, werden die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-217">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="26bb0-218">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child` Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-218">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="26bb0-219">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="26bb0-219">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="26bb0-220">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-220">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="26bb0-221">Das `extra`-Attribut der `Child` Komponente ist rechts von `@attributes`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-221">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="26bb0-222">Die gerenderte `<div>` der `Parent` Komponente enthält `extra="5"`, wenn Sie über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-222">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="26bb0-223">Im folgenden Beispiel wird die Reihenfolge der `extra` und `@attributes` im `<div>`der `Child` Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-223">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="26bb0-224">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="26bb0-224">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="26bb0-225">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-225">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="26bb0-226">Der gerenderte `<div>` in der `Parent` Komponente enthält beim Durchlaufen des zusätzlichen Attributs `extra="10"`:</span><span class="sxs-lookup"><span data-stu-id="26bb0-226">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="26bb0-227">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="26bb0-227">Data binding</span></span>

<span data-ttu-id="26bb0-228">Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [`@bind`](xref:mvc/views/razor#bind) -Attribut.</span><span class="sxs-lookup"><span data-stu-id="26bb0-228">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="26bb0-229">Im folgenden Beispiel wird eine `CurrentValue`-Eigenschaft an den Wert des Textfelds gebunden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-229">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="26bb0-230">Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-230">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="26bb0-231">Das Textfeld wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="26bb0-231">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="26bb0-232">Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen in der Benutzeroberfläche *normalerweise* sofort nach dem Auslösen eines Ereignis Handlers</span><span class="sxs-lookup"><span data-stu-id="26bb0-232">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="26bb0-233">Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:</span><span class="sxs-lookup"><span data-stu-id="26bb0-233">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="26bb0-234">Wenn die Komponente gerendert wird, stammt der `value` des Eingabe Elements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="26bb0-234">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="26bb0-235">Wenn der Benutzer das Textfeld eingibt und den Element Fokus ändert, wird das `onchange`-Ereignis ausgelöst, und die `CurrentValue`-Eigenschaft wird auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-235">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="26bb0-236">In der Realität ist die Codegenerierung komplexer, da `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-236">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="26bb0-237">Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.</span><span class="sxs-lookup"><span data-stu-id="26bb0-237">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="26bb0-238">Zusätzlich zur Behandlung von `onchange` Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [`@bind-value`](xref:mvc/views/razor#bind) Attribut mit einem `event` Parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-238">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="26bb0-239">Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-239">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="26bb0-240">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-240">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="26bb0-241">**Nicht zu erteilbare Werte**</span><span class="sxs-lookup"><span data-stu-id="26bb0-241">**Unparsable values**</span></span>

<span data-ttu-id="26bb0-242">Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-242">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="26bb0-243">Als Beispiel dient das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="26bb0-243">Consider the following scenario:</span></span>

* <span data-ttu-id="26bb0-244">Ein `<input>`-Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-244">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="26bb0-245">Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="26bb0-245">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="26bb0-246">Im vorangehenden Szenario wird der Wert des Elements auf `123`zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-246">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="26bb0-247">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="26bb0-247">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="26bb0-248">Standardmäßig gilt die Bindung für das `onchange` Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="26bb0-248">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="26bb0-249">Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-249">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="26bb0-250">Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-250">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="26bb0-251">Wenn das `oninput` Ereignis mit einem `int`gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.` Zeichen eingibt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-251">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="26bb0-252">Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="26bb0-252">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="26bb0-253">Es gibt Szenarios, in denen das Wiederherstellen des Werts für das `oninput` Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht zu testbaren `<input>` Wert löschen darf.</span><span class="sxs-lookup"><span data-stu-id="26bb0-253">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="26bb0-254">Zu Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="26bb0-254">Alternatives include:</span></span>

* <span data-ttu-id="26bb0-255">Verwenden Sie das `oninput`-Ereignis nicht.</span><span class="sxs-lookup"><span data-stu-id="26bb0-255">Don't use the `oninput` event.</span></span> <span data-ttu-id="26bb0-256">Verwenden Sie das Standard `onchange` Ereignis (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-256">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="26bb0-257">Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik bereit,</span><span class="sxs-lookup"><span data-stu-id="26bb0-257">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="26bb0-258">Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-258">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="26bb0-259">Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-259">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="26bb0-260">Komponenten Validierungs Komponenten:</span><span class="sxs-lookup"><span data-stu-id="26bb0-260">Form validation components:</span></span>
  * <span data-ttu-id="26bb0-261">Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler auf dem zugeordneten `EditContext`zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-261">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="26bb0-262">Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-262">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="26bb0-263">**Globalisierung**</span><span class="sxs-lookup"><span data-stu-id="26bb0-263">**Globalization**</span></span>

<span data-ttu-id="26bb0-264">`@bind` Werte für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-264">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="26bb0-265">Der Zugriff auf die aktuelle Kultur ist über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft möglich.</span><span class="sxs-lookup"><span data-stu-id="26bb0-265">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="26bb0-266">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-266">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="26bb0-267">Die vorangehenden Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-267">The preceding field types:</span></span>

* <span data-ttu-id="26bb0-268">Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-268">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="26bb0-269">Darf keinen frei Form Text enthalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-269">Can't contain free-form text.</span></span>
* <span data-ttu-id="26bb0-270">Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.</span><span class="sxs-lookup"><span data-stu-id="26bb0-270">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="26bb0-271">Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von Blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-271">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="26bb0-272">`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName> zum Auswerten und Formatieren eines Werts bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-272">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="26bb0-273">Die Angabe einer Kultur ist nicht empfehlenswert, wenn die Feldtypen `date` und `number` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-273">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="26bb0-274">`date` und `number` verfügen über integrierte Blazor Unterstützung, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-274">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="26bb0-275">Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .</span><span class="sxs-lookup"><span data-stu-id="26bb0-275">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="26bb0-276">**Format Zeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="26bb0-276">**Format strings**</span></span>

<span data-ttu-id="26bb0-277">Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="26bb0-277">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="26bb0-278">Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="26bb0-278">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="26bb0-279">Im vorangehenden Code ist der Feldtyp (`type`) des `<input>` Elements standardmäßig `text`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-279">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="26bb0-280">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-280">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="26bb0-281"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="26bb0-281"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="26bb0-282"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="26bb0-282"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="26bb0-283">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf die `value` des `<input>` Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="26bb0-283">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="26bb0-284">Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange` Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-284">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="26bb0-285">Es wird nicht empfohlen, ein Format für den `date` Feldtyp anzugeben, da Blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-285">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="26bb0-286">Verwenden Sie trotz der Empfehlung nur das `yyyy-MM-dd` Datumsformat für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem `date` Feldtyp bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-286">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="26bb0-287">**Komponenten Parameter**</span><span class="sxs-lookup"><span data-stu-id="26bb0-287">**Component parameters**</span></span>

<span data-ttu-id="26bb0-288">Die Bindung erkennt Komponenten Parameter, bei denen `@bind-{property}` einen Eigenschafts Wert auf Komponenten binden kann.</span><span class="sxs-lookup"><span data-stu-id="26bb0-288">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="26bb0-289">Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year` Component-Parameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="26bb0-289">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="26bb0-290">`EventCallback<T>` wird im Abschnitt [EventCallback](#eventcallback) erläutert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-290">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="26bb0-291">Die folgende übergeordnete Komponente verwendet `ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten an den `Year`-Parameter in der untergeordneten Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-291">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```razor
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

<span data-ttu-id="26bb0-292">Beim Laden des `ParentComponent` wird das folgende Markup erzeugt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-292">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="26bb0-293">Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent`geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-293">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="26bb0-294">Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-294">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="26bb0-295">Der `Year`-Parameter ist bindbar, da er über ein Begleit `YearChanged` Ereignis verfügt, das mit dem Typ des `Year` Parameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-295">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="26bb0-296">Gemäß der Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im wesentlichen Äquivalent zum Schreiben:</span><span class="sxs-lookup"><span data-stu-id="26bb0-296">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="26bb0-297">Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` Attributs an einen entsprechenden Ereignishandler gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-297">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="26bb0-298">Beispielsweise kann die-Eigenschaft `MyProp` an `MyEventHandler` mithilfe der folgenden beiden Attribute gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-298">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="26bb0-299">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="26bb0-299">Event handling</span></span>

<span data-ttu-id="26bb0-300">Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit.</span><span class="sxs-lookup"><span data-stu-id="26bb0-300">Razor components provide event handling features.</span></span> <span data-ttu-id="26bb0-301">Für ein HTML-Element Attribut mit dem Namen `on{EVENT}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="26bb0-301">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="26bb0-302">Der Name des Attributs wird immer [`@on{EVENT}`](xref:mvc/views/razor#onevent)formatiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-302">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="26bb0-303">Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-303">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
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

<span data-ttu-id="26bb0-304">Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-304">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="26bb0-305">Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-305">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="26bb0-306">[Statehaschte](xref:blazor/lifecycle#state-changes)muss nicht manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-306">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="26bb0-307">Ausnahmen werden protokolliert, wenn Sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-307">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="26bb0-308">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-308">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
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

### <a name="event-argument-types"></a><span data-ttu-id="26bb0-309">Ereignis Argument Typen</span><span class="sxs-lookup"><span data-stu-id="26bb0-309">Event argument types</span></span>

<span data-ttu-id="26bb0-310">Für einige Ereignisse sind Ereignis Argument Typen zulässig.</span><span class="sxs-lookup"><span data-stu-id="26bb0-310">For some events, event argument types are permitted.</span></span> <span data-ttu-id="26bb0-311">Wenn der Zugriff auf einen dieser Ereignis Typen nicht erforderlich ist, ist er im Methoden Aufrufvorgang nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="26bb0-311">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="26bb0-312">Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-312">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="26bb0-313">Event</span><span class="sxs-lookup"><span data-stu-id="26bb0-313">Event</span></span>            | <span data-ttu-id="26bb0-314">Klasse</span><span class="sxs-lookup"><span data-stu-id="26bb0-314">Class</span></span>                | <span data-ttu-id="26bb0-315">DOM-Ereignisse und-Notizen</span><span class="sxs-lookup"><span data-stu-id="26bb0-315">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="26bb0-316">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="26bb0-316">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="26bb0-317">`oncut`ist `oncopy`ist `onpaste`</span><span class="sxs-lookup"><span data-stu-id="26bb0-317">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="26bb0-318">Ziehen</span><span class="sxs-lookup"><span data-stu-id="26bb0-318">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="26bb0-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="26bb0-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="26bb0-320">`DataTransfer` und `DataTransferItem` die gezogenen Elementdaten speichern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-320">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="26bb0-321">Fehler</span><span class="sxs-lookup"><span data-stu-id="26bb0-321">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="26bb0-322">Event</span><span class="sxs-lookup"><span data-stu-id="26bb0-322">Event</span></span>            | `EventArgs`          | <span data-ttu-id="26bb0-323">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="26bb0-323">*General*</span></span><br><span data-ttu-id="26bb0-324">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="26bb0-324">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="26bb0-325">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="26bb0-325">*Clipboard*</span></span><br><span data-ttu-id="26bb0-326">`onbeforecut`ist `onbeforecopy`ist `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="26bb0-326">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="26bb0-327">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="26bb0-327">*Input*</span></span><br><span data-ttu-id="26bb0-328">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="26bb0-328">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="26bb0-329">*Medien*</span><span class="sxs-lookup"><span data-stu-id="26bb0-329">*Media*</span></span><br><span data-ttu-id="26bb0-330">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="26bb0-330">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="26bb0-331">Fokus</span><span class="sxs-lookup"><span data-stu-id="26bb0-331">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="26bb0-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="26bb0-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="26bb0-333">Beinhaltet keine Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-333">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="26bb0-334">Input</span><span class="sxs-lookup"><span data-stu-id="26bb0-334">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="26bb0-335">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="26bb0-335">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="26bb0-336">Tastatur</span><span class="sxs-lookup"><span data-stu-id="26bb0-336">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="26bb0-337">`onkeydown`ist `onkeypress`ist `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="26bb0-337">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="26bb0-338">Maus</span><span class="sxs-lookup"><span data-stu-id="26bb0-338">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="26bb0-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="26bb0-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="26bb0-340">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="26bb0-340">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="26bb0-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="26bb0-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="26bb0-342">Mausrad</span><span class="sxs-lookup"><span data-stu-id="26bb0-342">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="26bb0-343">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="26bb0-343">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="26bb0-344">Fortschritt</span><span class="sxs-lookup"><span data-stu-id="26bb0-344">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="26bb0-345">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="26bb0-345">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="26bb0-346">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="26bb0-346">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="26bb0-347">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="26bb0-347">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="26bb0-348">`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar.</span><span class="sxs-lookup"><span data-stu-id="26bb0-348">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="26bb0-349">Informationen zu den Eigenschaften und dem Ereignis Behandlungs Verhalten der Ereignisse in der vorangehenden Tabelle finden Sie unter [EventArgs classes in the Reference Source (ASPNET/aspnetcore Release/3.0 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="26bb0-349">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="26bb0-350">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="26bb0-350">Lambda expressions</span></span>

<span data-ttu-id="26bb0-351">Lambda-Ausdrücke können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-351">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="26bb0-352">Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-352">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="26bb0-353">Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufgerufen wird, um ein Ereignis Argument (`MouseEventArgs`) und seine Schaltflächen Nummer (`buttonNumber`) zu übergeben, wenn es in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-353">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
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
> <span data-ttu-id="26bb0-354">Verwenden Sie die Schleifenvariable (`i`) in einer `for`-Schleife **nicht** direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="26bb0-354">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="26bb0-355">Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch `i`Wert in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-355">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="26bb0-356">Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="26bb0-356">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="26bb0-357">EventCallback</span><span class="sxs-lookup"><span data-stu-id="26bb0-357">EventCallback</span></span>

<span data-ttu-id="26bb0-358">Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt&mdash;z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-358">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="26bb0-359">Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie eine `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-359">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="26bb0-360">Eine übergeordnete Komponente kann dem `EventCallback`einer untergeordneten Komponente eine Rückruf Methode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-360">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="26bb0-361">Der `ChildComponent` in der Beispiel-app (*Components/childcomponent. Razor*) veranschaulicht, wie der `onclick` Handler einer Schaltfläche für den Empfang eines `EventCallback` Delegaten aus dem `ParentComponent`des Beispiels eingerichtet ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-361">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="26bb0-362">Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-362">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="26bb0-363">Der `ParentComponent` legt die `EventCallback<T>` des untergeordneten Elements auf seine `ShowMessage`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="26bb0-363">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method.</span></span>

<span data-ttu-id="26bb0-364">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-364">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="26bb0-365">Wenn die Schaltfläche im `ChildComponent`ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-365">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="26bb0-366">Die `ShowMessage`-Methode des `ParentComponent`wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-366">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="26bb0-367">`messageText` wird aktualisiert und in der `ParentComponent`angezeigt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-367">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="26bb0-368">In der-Methode des Rückrufs (`ShowMessage`) ist kein aufzurufende [statehasch-](xref:blazor/lifecycle#state-changes) Methode erforderlich.</span><span class="sxs-lookup"><span data-stu-id="26bb0-368">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="26bb0-369">`StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent`zu übertragen, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-369">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="26bb0-370">`EventCallback` und `EventCallback<T>` die asynchrone Delegaten zulassen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-370">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="26bb0-371">`EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="26bb0-371">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="26bb0-372">`EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="26bb0-372">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="26bb0-373">Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf die <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="26bb0-373">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="26bb0-374">Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.</span><span class="sxs-lookup"><span data-stu-id="26bb0-374">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="26bb0-375">Bevorzugen der stark typisierten `EventCallback<T>` über `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-375">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="26bb0-376">`EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehler Feedback.</span><span class="sxs-lookup"><span data-stu-id="26bb0-376">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="26bb0-377">Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional.</span><span class="sxs-lookup"><span data-stu-id="26bb0-377">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="26bb0-378">Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-378">Use `EventCallback` when there's no value passed to the callback.</span></span>

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a><span data-ttu-id="26bb0-379">Standard Aktionen verhindern</span><span class="sxs-lookup"><span data-stu-id="26bb0-379">Prevent default actions</span></span>

<span data-ttu-id="26bb0-380">Verwenden Sie das [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) direktivenattribut, um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-380">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="26bb0-381">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und sich der Element Fokus auf einem Textfeld befindet, zeigt ein Browser normalerweise das Schlüsselzeichen im Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="26bb0-381">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="26bb0-382">Im folgenden Beispiel wird das Standardverhalten verhindert, indem das `@onkeypress:preventDefault` direktivenattribut angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-382">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="26bb0-383">Der Wert des Leistungs Zählers wird erhöht, und der **+** Schlüssel wird nicht in den Wert des `<input>` Elements aufgezeichnet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-383">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="26bb0-384">Das Angeben des `@on{EVENT}:preventDefault` Attributs ohne Wert entspricht `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-384">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="26bb0-385">Der Wert des-Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="26bb0-385">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="26bb0-386">Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool` Feld, das entweder auf `true` oder `false`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-386">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="26bb0-387">Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-387">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="26bb0-388">Der Ereignishandler und die Vermeidung von Standard Aktions Szenarien können unabhängig voneinander verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-388">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="26bb0-389">Ereignis Weitergabe Abbrechen</span><span class="sxs-lookup"><span data-stu-id="26bb0-389">Stop event propagation</span></span>

<span data-ttu-id="26bb0-390">Verwenden Sie das Attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) Direktive, um die Ereignis Weitergabe zu verhindern</span><span class="sxs-lookup"><span data-stu-id="26bb0-390">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="26bb0-391">Im folgenden Beispiel wird durch Aktivieren des Kontrollkästchens verhindert, dass Click-Ereignisse aus dem zweiten untergeordneten `<div>` an die übergeordnete `<div>`weitergegeben werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-391">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker-end

## <a name="chained-bind"></a><span data-ttu-id="26bb0-392">Verkettete Bindung</span><span class="sxs-lookup"><span data-stu-id="26bb0-392">Chained bind</span></span>

<span data-ttu-id="26bb0-393">Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="26bb0-393">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="26bb0-394">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-394">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="26bb0-395">Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-395">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="26bb0-396">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-396">The event handler and value must be specified separately.</span></span> <span data-ttu-id="26bb0-397">Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-397">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="26bb0-398">Die folgende `PasswordField` Komponente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="26bb0-398">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="26bb0-399">Legt den Wert eines `<input>` Elements auf eine `Password` Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="26bb0-399">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="26bb0-400">Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="26bb0-400">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
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

<span data-ttu-id="26bb0-401">Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-401">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="26bb0-402">Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="26bb0-402">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="26bb0-403">Erstellen Sie ein Unterstützungs Feld für `Password` (`password` im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="26bb0-403">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="26bb0-404">Führen Sie die Überprüfungen oder Trap Fehler im `Password` Setter aus.</span><span class="sxs-lookup"><span data-stu-id="26bb0-404">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="26bb0-405">Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-405">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
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

## <a name="capture-references-to-components"></a><span data-ttu-id="26bb0-406">Verweise auf Komponenten erfassen</span><span class="sxs-lookup"><span data-stu-id="26bb0-406">Capture references to components</span></span>

<span data-ttu-id="26bb0-407">Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, z. b. `Show` oder `Reset`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-407">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="26bb0-408">So erfassen Sie einen Komponenten Verweis:</span><span class="sxs-lookup"><span data-stu-id="26bb0-408">To capture a component reference:</span></span>

* <span data-ttu-id="26bb0-409">Fügen Sie der untergeordneten Komponente ein [`@ref`](xref:mvc/views/razor#ref) Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="26bb0-409">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="26bb0-410">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="26bb0-410">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="26bb0-411">Wenn die Komponente gerendert wird, wird das `loginDialog` Feld mit der `MyLoginDialog` untergeordneten Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-411">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="26bb0-412">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-412">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26bb0-413">Die `loginDialog` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="26bb0-413">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="26bb0-414">Bis zu diesem Punkt sind keine Verweise mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-414">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="26bb0-415">Um Komponenten Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, verwenden Sie die [onafterrenderasync-Methode oder die onafterrendering-Methode](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="26bb0-415">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="26bb0-416">Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion</span><span class="sxs-lookup"><span data-stu-id="26bb0-416">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="26bb0-417">Komponenten Verweise werden nicht an JavaScript-Code übermittelt,&mdash;Sie nur in .NET-Code verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-417">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="26bb0-418">Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-418">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="26bb0-419">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-419">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="26bb0-420">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-420">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="26bb0-421">Komponenten Methoden extern aufrufen, um den Status zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-421">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="26bb0-422"> verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-422"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="26bb0-423">Die [Lebenszyklus Methoden](xref:blazor/lifecycle) einer Komponente und alle Ereignis Rückrufe, die von Blazor ausgelöst werden, werden für diese `SynchronizationContext`ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-423">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="26bb0-424">Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an Blazor-`SynchronizationContext`weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-424">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="26bb0-425">Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="26bb0-425">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="26bb0-426">Verwendung des `NotifierService` zum Aktualisieren einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-426">Usage of the `NotifierService` to update a component:</span></span>

```razor
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

<span data-ttu-id="26bb0-427">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify` Methode der Komponente außerhalb des `SynchronizationContext`der Blazorauf.</span><span class="sxs-lookup"><span data-stu-id="26bb0-427">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="26bb0-428">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-428">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="26bb0-429">Verwenden Sie \@Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-429">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="26bb0-430">Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss Blazorder diffingalgorithmus entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-430">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="26bb0-431">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-431">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="26bb0-432">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="26bb0-432">Consider the following example:</span></span>

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

<span data-ttu-id="26bb0-433">Der Inhalt der `People` Sammlung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-433">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="26bb0-434">Wenn die Komponente erneut ausgeführt wird, kann sich die `<DetailsEditor>` Komponente ändern, um unterschiedliche `Details` Parameterwerte zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-434">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="26bb0-435">Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-435">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="26bb0-436">In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="26bb0-436">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="26bb0-437">Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-437">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="26bb0-438">`@key` bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-438">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="26bb0-439">Wenn sich die `People`-Auflistung ändert, behält der diff-Algorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="26bb0-439">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="26bb0-440">Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>` Instanz aus der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-440">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="26bb0-441">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-441">Other instances are left unchanged.</span></span>
* <span data-ttu-id="26bb0-442">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>` Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-442">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="26bb0-443">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-443">Other instances are left unchanged.</span></span>
* <span data-ttu-id="26bb0-444">Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-444">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="26bb0-445">In einigen Szenarios minimiert die Verwendung von `@key` die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="26bb0-445">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26bb0-446">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="26bb0-446">Keys are local to each container element or component.</span></span> <span data-ttu-id="26bb0-447">Schlüssel werden nicht global über das Dokument hinweg verglichen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-447">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="26bb0-448">Verwendung \@Schlüssels</span><span class="sxs-lookup"><span data-stu-id="26bb0-448">When to use \@key</span></span>

<span data-ttu-id="26bb0-449">In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. b. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um die `@key`zu definieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-449">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="26bb0-450">Sie können `@key` auch verwenden, um zu verhindern, dass Blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="26bb0-450">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="26bb0-451">Wenn `@currentPerson` geändert wird, erzwingt die `@key` Attribute-Direktive Blazor, den gesamten `<div>` und seine Nachfolger zu verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-451">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="26bb0-452">Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-452">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="26bb0-453">Verwendung \@Schlüssels nicht</span><span class="sxs-lookup"><span data-stu-id="26bb0-453">When not to use \@key</span></span>

<span data-ttu-id="26bb0-454">Beim Durchlaufen der `@key`werden Kosten für die Leistung anfallen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-454">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="26bb0-455">Die Leistungskosten sind nicht groß, sondern geben nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-455">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="26bb0-456">Auch wenn `@key` nicht verwendet wird, behält Blazor die untergeordneten Elemente und Komponenten Instanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="26bb0-456">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="26bb0-457">Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-457">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="26bb0-458">Welche Werte müssen für \@Schlüssel verwendet werden?</span><span class="sxs-lookup"><span data-stu-id="26bb0-458">What values to use for \@key</span></span>

<span data-ttu-id="26bb0-459">Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-459">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="26bb0-460">Modell Objektinstanzen (z. b. eine `Person`-Instanz, wie im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="26bb0-460">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="26bb0-461">Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-461">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="26bb0-462">Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string`oder `Guid`).</span><span class="sxs-lookup"><span data-stu-id="26bb0-462">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="26bb0-463">Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-463">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="26bb0-464">Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst Blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="26bb0-464">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="26bb0-465">Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.</span><span class="sxs-lookup"><span data-stu-id="26bb0-465">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="26bb0-466">Routing</span><span class="sxs-lookup"><span data-stu-id="26bb0-466">Routing</span></span>

<span data-ttu-id="26bb0-467">Das Routing in Blazor erfolgt durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-467">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="26bb0-468">Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-468">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="26bb0-469">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-469">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="26bb0-470">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-470">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="26bb0-471">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-471">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="26bb0-472">*Pages/blazorroute. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-472">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="26bb0-473">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-473">Route parameters</span></span>

<span data-ttu-id="26bb0-474">Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-474">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="26bb0-475">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-475">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="26bb0-476">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-476">*Pages/RouteParameter.razor*:</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="26bb0-477">Optionale Parameter werden nicht unterstützt. Daher werden im obigen Beispiel zwei `@page` Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-477">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="26bb0-478">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="26bb0-478">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="26bb0-479">Die zweite `@page`-Direktive übernimmt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="26bb0-479">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="26bb0-480">Die *catch-all-* Parameter Syntax (`*`/`**`), die den Pfad über mehrere Ordner Grenzen hinweg erfasst, wird in Razor-*Komponenten (Razor*-Komponenten) **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-480">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a><span data-ttu-id="26bb0-481">Unterstützung für Teil Klassen</span><span class="sxs-lookup"><span data-stu-id="26bb0-481">Partial class support</span></span>

<span data-ttu-id="26bb0-482">Razor-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-482">Razor components are generated as partial classes.</span></span> <span data-ttu-id="26bb0-483">Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-483">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="26bb0-484">C#Code wird in einem [`@code`](xref:mvc/views/razor#code) -Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-484">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="26bb0-485">Mithilfe dieses Ansatzes definieren Blazor Vorlagen ihre Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-485">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="26bb0-486">C#der Code wird in einer Code Behind-Datei platziert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-486">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="26bb0-487">Das folgende Beispiel zeigt die Standard `Counter` Komponente mit einem `@code`-Block in einer APP, die aus einer Blazor Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="26bb0-487">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="26bb0-488">HTML-Markup, Razor-Code C# und Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="26bb0-488">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="26bb0-489">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-489">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="26bb0-490">Die `Counter` Komponente kann auch mit einer Code Behind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-490">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="26bb0-491">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-491">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="26bb0-492">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-492">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a><span data-ttu-id="26bb0-493">Angeben einer Komponentenbasis Klasse</span><span class="sxs-lookup"><span data-stu-id="26bb0-493">Specify a component base class</span></span>

<span data-ttu-id="26bb0-494">Die `@inherits`-Direktive kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-494">The `@inherits` directive can be used to specify a base class for a component.</span></span>

<span data-ttu-id="26bb0-495">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-495">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="26bb0-496">*Pages/blazorrocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-496">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="26bb0-497">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-497">*BlazorRocksBase.cs*:</span></span>

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

<span data-ttu-id="26bb0-498">Die Basisklasse sollte von `ComponentBase`abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-498">The base class should derive from `ComponentBase`.</span></span>

::: moniker-end

## <a name="import-components"></a><span data-ttu-id="26bb0-499">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="26bb0-499">Import components</span></span>

<span data-ttu-id="26bb0-500">Der Namespace einer mit Razor erstellten Komponente basiert auf (in Prioritäts Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="26bb0-500">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="26bb0-501">[`@namespace`](xref:mvc/views/razor#namespace) Bezeichnung in Razor-Datei ( *. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="26bb0-501">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="26bb0-502">Das Projekt `RootNamespace` in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="26bb0-502">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="26bb0-503">Der Projektname, der aus dem Dateinamen der Projektdatei ( *. csproj*) und dem Pfad des Projekt Stamms zur Komponente stammt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-503">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="26bb0-504">Das Framework löst z. b. *{Project root}/pages/index.Razor* (*blazorsample. csproj*) in den Namespace `BlazorSample.Pages`auf.</span><span class="sxs-lookup"><span data-stu-id="26bb0-504">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="26bb0-505">Komponenten folgen C# den namens Bindungs Regeln.</span><span class="sxs-lookup"><span data-stu-id="26bb0-505">Components follow C# name binding rules.</span></span> <span data-ttu-id="26bb0-506">Für die `Index` Komponente in diesem Beispiel sind die Komponenten im Bereich alle Komponenten:</span><span class="sxs-lookup"><span data-stu-id="26bb0-506">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="26bb0-507">Im gleichen Ordner, *Seiten*.</span><span class="sxs-lookup"><span data-stu-id="26bb0-507">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="26bb0-508">Die Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-508">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="26bb0-509">Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der [`@using`](xref:mvc/views/razor#using) Direktive von Razor in den Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="26bb0-509">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="26bb0-510">Wenn eine andere Komponente, `NavMenu.razor`im Ordner " *blazorsample/Shared/* " vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using`-Anweisung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-510">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="26bb0-511">Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, die die [`@using`](xref:mvc/views/razor#using) -Direktive nicht benötigen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-511">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="26bb0-512">Die `global::` Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-512">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="26bb0-513">Das Importieren von Komponenten mit Alias `using` Anweisungen (z. b. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-513">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="26bb0-514">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-514">Partially qualified names aren't supported.</span></span> <span data-ttu-id="26bb0-515">Beispielsweise wird das Hinzufügen von `@using BlazorSample` und verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-515">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="26bb0-516">Attribute für bedingtes HTML-Element</span><span class="sxs-lookup"><span data-stu-id="26bb0-516">Conditional HTML element attributes</span></span>

<span data-ttu-id="26bb0-517">HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-517">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="26bb0-518">Wenn der Wert `false` oder `null`ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-518">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="26bb0-519">Wenn der Wert `true`ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-519">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="26bb0-520">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-520">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="26bb0-521">Wenn `IsCompleted` `true`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="26bb0-521">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="26bb0-522">Wenn `IsCompleted` `false`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="26bb0-522">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="26bb0-523">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-523">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="26bb0-524">Einige HTML-Attribute, wie z. b. [Aria-gedrückt](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), funktionieren nicht ordnungsgemäß, wenn der .NET-Typ ein `bool`ist.</span><span class="sxs-lookup"><span data-stu-id="26bb0-524">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="26bb0-525">Verwenden Sie in diesen Fällen einen `string` Typ anstelle eines `bool`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-525">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="26bb0-526">Unformatierte HTML</span><span class="sxs-lookup"><span data-stu-id="26bb0-526">Raw HTML</span></span>

<span data-ttu-id="26bb0-527">Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-527">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="26bb0-528">Um unformatierten HTML-Code zu erstellen, packen Sie den HTML-Inhalt in einen `MarkupString` Wert</span><span class="sxs-lookup"><span data-stu-id="26bb0-528">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="26bb0-529">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-529">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="26bb0-530">Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-530">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="26bb0-531">Im folgenden Beispiel wird gezeigt, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-531">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="26bb0-532">Komponenten mit Vorlagen</span><span class="sxs-lookup"><span data-stu-id="26bb0-532">Templated components</span></span>

<span data-ttu-id="26bb0-533">Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="26bb0-533">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="26bb0-534">Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="26bb0-534">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="26bb0-535">Einige Beispiele sind:</span><span class="sxs-lookup"><span data-stu-id="26bb0-535">A couple of examples include:</span></span>

* <span data-ttu-id="26bb0-536">Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.</span><span class="sxs-lookup"><span data-stu-id="26bb0-536">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="26bb0-537">Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-537">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="26bb0-538">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-538">Template parameters</span></span>

<span data-ttu-id="26bb0-539">Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-539">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="26bb0-540">Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar.</span><span class="sxs-lookup"><span data-stu-id="26bb0-540">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="26bb0-541">`RenderFragment<T>` nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-541">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="26bb0-542">`TableTemplate` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-542">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="26bb0-543">Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können die Vorlagen Parameter mit untergeordneten Elementen angegeben werden, die den Namen der Parameter (`TableHeader` und `RowTemplate` im folgenden Beispiel) entsprechen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-543">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
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

### <a name="template-context-parameters"></a><span data-ttu-id="26bb0-544">Vorlagen Kontext Parameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-544">Template context parameters</span></span>

<span data-ttu-id="26bb0-545">Komponenten Argumente vom Typ `RenderFragment<T>` als Elemente übergeben, haben einen impliziten Parameter mit dem Namen `context` (z. b. aus dem vorangehenden Codebeispiel `@context.PetId`), Sie können jedoch den Parameternamen mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-545">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="26bb0-546">Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate` Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="26bb0-546">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
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

<span data-ttu-id="26bb0-547">Alternativ können Sie das `Context`-Attribut für das Component-Element angeben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-547">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="26bb0-548">Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter.</span><span class="sxs-lookup"><span data-stu-id="26bb0-548">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="26bb0-549">Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element).</span><span class="sxs-lookup"><span data-stu-id="26bb0-549">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="26bb0-550">Im folgenden Beispiel wird das `Context`-Attribut im `TableTemplate`-Element angezeigt und gilt für alle Vorlagen Parameter:</span><span class="sxs-lookup"><span data-stu-id="26bb0-550">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
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

### <a name="generic-typed-components"></a><span data-ttu-id="26bb0-551">Generisch typisierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="26bb0-551">Generic-typed components</span></span>

<span data-ttu-id="26bb0-552">Auf Vorlagen basierende Komponenten werden oft generisch typisiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-552">Templated components are often generically typed.</span></span> <span data-ttu-id="26bb0-553">Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` Werte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-553">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="26bb0-554">Verwenden Sie zum Definieren einer generischen Komponente die [`@typeparam`](xref:mvc/views/razor#typeparam) -Direktive, um Typparameter anzugeben:</span><span class="sxs-lookup"><span data-stu-id="26bb0-554">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="26bb0-555">Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-555">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="26bb0-556">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-556">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="26bb0-557">Im folgenden Beispiel gibt `TItem="Pet"` den Typ an:</span><span class="sxs-lookup"><span data-stu-id="26bb0-557">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="26bb0-558">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="26bb0-558">Cascading values and parameters</span></span>

<span data-ttu-id="26bb0-559">In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="26bb0-559">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="26bb0-560">Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-560">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="26bb0-561">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-561">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="26bb0-562">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="26bb0-562">Theme example</span></span>

<span data-ttu-id="26bb0-563">Im folgenden Beispiel der Beispiel-App gibt die `ThemeInfo`-Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.</span><span class="sxs-lookup"><span data-stu-id="26bb0-563">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="26bb0-564">*Uithmeclasses/themerfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="26bb0-564">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="26bb0-565">Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-565">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="26bb0-566">Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="26bb0-566">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="26bb0-567">Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-567">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="26bb0-568">`ButtonClass` wird der Wert `btn-success` in der Layoutkomponente zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-568">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="26bb0-569">Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierenden Objekts nutzen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-569">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="26bb0-570">`CascadingValuesParametersLayout` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-570">`CascadingValuesParametersLayout` component:</span></span>

```razor
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

<span data-ttu-id="26bb0-571">Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="26bb0-571">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="26bb0-572">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-572">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="26bb0-573">In der Beispiel-App bindet die `CascadingValuesParametersTheme` Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="26bb0-573">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="26bb0-574">Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-574">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="26bb0-575">`CascadingValuesParametersTheme` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-575">`CascadingValuesParametersTheme` component:</span></span>

```razor
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

<span data-ttu-id="26bb0-576">Um mehrere Werte desselben Typs innerhalb derselben Unterstruktur zu kaskadieren, stellen Sie jeder `CascadingValue` Komponente und der entsprechenden `CascadingParameter`eine eindeutige `Name` Zeichenfolge bereit.</span><span class="sxs-lookup"><span data-stu-id="26bb0-576">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="26bb0-577">Im folgenden Beispiel werden zwei `CascadingValue` Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen kaskadieren:</span><span class="sxs-lookup"><span data-stu-id="26bb0-577">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
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

<span data-ttu-id="26bb0-578">In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-578">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="26bb0-579">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="26bb0-579">TabSet example</span></span>

<span data-ttu-id="26bb0-580">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="26bb0-580">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="26bb0-581">Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.</span><span class="sxs-lookup"><span data-stu-id="26bb0-581">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="26bb0-582">Die Beispiel-App verfügt über eine `ITab` Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="26bb0-582">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="26bb0-583">Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab` Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="26bb0-583">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="26bb0-584">Die untergeordneten `Tab` Komponenten werden nicht explizit als Parameter an die `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-584">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="26bb0-585">Stattdessen sind die untergeordneten `Tab` Komponenten Teil des untergeordneten Inhalts des `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-585">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="26bb0-586">Allerdings müssen die `TabSet` weiterhin über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code erforderlich ist, kann die `TabSet` Komponente *sich selbst als kaskadierenden Wert bereitstellen* , der dann von den Nachfolger `Tab` Komponenten abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-586">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="26bb0-587">`TabSet` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-587">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="26bb0-588">Die Nachfolger `Tab` Komponenten erfassen den enthaltenden `TabSet` als kaskadierenden Parameter, sodass sich die `Tab` Komponenten dem `TabSet` und der Koordinate, auf der die Registerkarte aktiv ist, hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-588">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="26bb0-589">`Tab` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-589">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="26bb0-590">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="26bb0-590">Razor templates</span></span>

<span data-ttu-id="26bb0-591">Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-591">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="26bb0-592">Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:</span><span class="sxs-lookup"><span data-stu-id="26bb0-592">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="26bb0-593">Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`-und `RenderFragment<T>` Werte angeben und Vorlagen direkt in einer-Komponente darstellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-593">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="26bb0-594">Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-594">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```razor
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

<span data-ttu-id="26bb0-595">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="26bb0-595">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="26bb0-596">Manuelle rendertreebuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="26bb0-596">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="26bb0-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.</span><span class="sxs-lookup"><span data-stu-id="26bb0-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="26bb0-598">Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="26bb0-598">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="26bb0-599">Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-599">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="26bb0-600">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere-Komponente integriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="26bb0-600">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="26bb0-601">Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-601">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="26bb0-602">Wenn Sie `RenderTreeBuilder` Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-602">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="26bb0-603">Der Algorithmus für die Blazor Differenz basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-603">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="26bb0-604">Wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-604">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="26bb0-605">**Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="26bb0-605">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="26bb0-606">Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="26bb0-606">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="26bb0-607">`BuiltContent` Komponente:</span><span class="sxs-lookup"><span data-stu-id="26bb0-607">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="26bb0-608">Die Typen in `Microsoft.AspNetCore.Components.RenderTree` die Verarbeitung der *Ergebnisse* von Renderingvorgängen zulassen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-608">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="26bb0-609">Hierbei handelt es sich um interne Details der Implementierung des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="26bb0-609">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="26bb0-610">Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="26bb0-610">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="26bb0-611">Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="26bb0-611">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="26bb0-612"> `.razor` Dateien werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-612"> `.razor` files are always compiled.</span></span> <span data-ttu-id="26bb0-613">Dies ist möglicherweise ein großer Vorteil für `.razor`, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-613">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="26bb0-614">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="26bb0-614">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="26bb0-615">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-615">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="26bb0-616">Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.</span><span class="sxs-lookup"><span data-stu-id="26bb0-616">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="26bb0-617">Sehen Sie sich die folgende Razor-Komponenten Datei ( *. Razor*) an:</span><span class="sxs-lookup"><span data-stu-id="26bb0-617">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="26bb0-618">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="26bb0-618">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="26bb0-619">Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true`ist:</span><span class="sxs-lookup"><span data-stu-id="26bb0-619">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="26bb0-620">Sequenz</span><span class="sxs-lookup"><span data-stu-id="26bb0-620">Sequence</span></span> | <span data-ttu-id="26bb0-621">Typ</span><span class="sxs-lookup"><span data-stu-id="26bb0-621">Type</span></span>      | <span data-ttu-id="26bb0-622">importieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-622">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="26bb0-623">0</span><span class="sxs-lookup"><span data-stu-id="26bb0-623">0</span></span>        | <span data-ttu-id="26bb0-624">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-624">Text node</span></span> | <span data-ttu-id="26bb0-625">Erster</span><span class="sxs-lookup"><span data-stu-id="26bb0-625">First</span></span>  |
| <span data-ttu-id="26bb0-626">1</span><span class="sxs-lookup"><span data-stu-id="26bb0-626">1</span></span>        | <span data-ttu-id="26bb0-627">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-627">Text node</span></span> | <span data-ttu-id="26bb0-628">Second</span><span class="sxs-lookup"><span data-stu-id="26bb0-628">Second</span></span> |

<span data-ttu-id="26bb0-629">Stellen Sie sich vor, dass `someFlag` `false`wird und das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-629">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="26bb0-630">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="26bb0-630">This time, the builder receives:</span></span>

| <span data-ttu-id="26bb0-631">Sequenz</span><span class="sxs-lookup"><span data-stu-id="26bb0-631">Sequence</span></span> | <span data-ttu-id="26bb0-632">Typ</span><span class="sxs-lookup"><span data-stu-id="26bb0-632">Type</span></span>       | <span data-ttu-id="26bb0-633">importieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-633">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="26bb0-634">1</span><span class="sxs-lookup"><span data-stu-id="26bb0-634">1</span></span>        | <span data-ttu-id="26bb0-635">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-635">Text node</span></span>  | <span data-ttu-id="26bb0-636">Second</span><span class="sxs-lookup"><span data-stu-id="26bb0-636">Second</span></span> |

<span data-ttu-id="26bb0-637">Wenn die Laufzeit einen diff ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:</span><span class="sxs-lookup"><span data-stu-id="26bb0-637">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="26bb0-638">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-638">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="26bb0-639">Was schief geht, wenn Sie Programm gesteuert Sequenznummern generieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-639">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="26bb0-640">Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:</span><span class="sxs-lookup"><span data-stu-id="26bb0-640">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="26bb0-641">Nun lautet die erste Ausgabe wie folgt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-641">Now, the first output is:</span></span>

| <span data-ttu-id="26bb0-642">Sequenz</span><span class="sxs-lookup"><span data-stu-id="26bb0-642">Sequence</span></span> | <span data-ttu-id="26bb0-643">Typ</span><span class="sxs-lookup"><span data-stu-id="26bb0-643">Type</span></span>      | <span data-ttu-id="26bb0-644">importieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-644">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="26bb0-645">0</span><span class="sxs-lookup"><span data-stu-id="26bb0-645">0</span></span>        | <span data-ttu-id="26bb0-646">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-646">Text node</span></span> | <span data-ttu-id="26bb0-647">Erster</span><span class="sxs-lookup"><span data-stu-id="26bb0-647">First</span></span>  |
| <span data-ttu-id="26bb0-648">1</span><span class="sxs-lookup"><span data-stu-id="26bb0-648">1</span></span>        | <span data-ttu-id="26bb0-649">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-649">Text node</span></span> | <span data-ttu-id="26bb0-650">Second</span><span class="sxs-lookup"><span data-stu-id="26bb0-650">Second</span></span> |

<span data-ttu-id="26bb0-651">Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="26bb0-651">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="26bb0-652">`someFlag` wird im zweiten Rendering `false`, und die Ausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="26bb0-652">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="26bb0-653">Sequenz</span><span class="sxs-lookup"><span data-stu-id="26bb0-653">Sequence</span></span> | <span data-ttu-id="26bb0-654">Typ</span><span class="sxs-lookup"><span data-stu-id="26bb0-654">Type</span></span>      | <span data-ttu-id="26bb0-655">importieren</span><span class="sxs-lookup"><span data-stu-id="26bb0-655">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="26bb0-656">0</span><span class="sxs-lookup"><span data-stu-id="26bb0-656">0</span></span>        | <span data-ttu-id="26bb0-657">Textknoten</span><span class="sxs-lookup"><span data-stu-id="26bb0-657">Text node</span></span> | <span data-ttu-id="26bb0-658">Second</span><span class="sxs-lookup"><span data-stu-id="26bb0-658">Second</span></span> |

<span data-ttu-id="26bb0-659">Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:</span><span class="sxs-lookup"><span data-stu-id="26bb0-659">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="26bb0-660">Ändern Sie den Wert für den ersten Textknoten in `Second`.</span><span class="sxs-lookup"><span data-stu-id="26bb0-660">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="26bb0-661">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-661">Remove the second text node.</span></span>

<span data-ttu-id="26bb0-662">Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo die `if/else` branches und Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="26bb0-662">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="26bb0-663">Dies führt **doppelt so lange** wie zuvor zu einem diff.</span><span class="sxs-lookup"><span data-stu-id="26bb0-663">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="26bb0-664">Dies ist ein sehr einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="26bb0-664">This is a trivial example.</span></span> <span data-ttu-id="26bb0-665">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer.</span><span class="sxs-lookup"><span data-stu-id="26bb0-665">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="26bb0-666">Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="26bb0-666">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="26bb0-667">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="26bb0-667">Guidance and conclusions</span></span>

* <span data-ttu-id="26bb0-668">Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-668">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="26bb0-669">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="26bb0-669">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="26bb0-670">Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik.</span><span class="sxs-lookup"><span data-stu-id="26bb0-670">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="26bb0-671">Bevorzugen Sie `.razor` Dateien, und ermöglichen Sie dem Compiler, die Sequenznummern zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="26bb0-671">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="26bb0-672">Wenn Sie manuelle `RenderTreeBuilder` Logik nicht vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die `OpenRegion`/`CloseRegion` aufrufen umschließen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-672">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="26bb0-673">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.</span><span class="sxs-lookup"><span data-stu-id="26bb0-673">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="26bb0-674">Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-674">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="26bb0-675">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="26bb0-675">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="26bb0-676">Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-676">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="26bb0-677"> verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-677"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="26bb0-678">Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und Blazor hat den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die *Razor* -Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-678">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="26bb0-679">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="26bb0-679">Localization</span></span>

Blazor<span data-ttu-id="26bb0-680"> Server-apps werden mithilfe von [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="26bb0-680"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="26bb0-681">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-681">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="26bb0-682">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="26bb0-682">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="26bb0-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="26bb0-683">Cookies</span></span>](#cookies)
* [<span data-ttu-id="26bb0-684">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="26bb0-684">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="26bb0-685">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-685">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="26bb0-686">Konfigurieren des Linkers für die Internationalisierung (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="26bb0-686">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="26bb0-687">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="26bb0-687">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="26bb0-688">Weitere Informationen und Anleitungen zum Steuern des linkerverhaltens finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-688">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="26bb0-689">Cookies</span><span class="sxs-lookup"><span data-stu-id="26bb0-689">Cookies</span></span>

<span data-ttu-id="26bb0-690">Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="26bb0-690">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="26bb0-691">Das Cookie wird durch die `OnGet`-Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-691">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="26bb0-692">Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-692">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="26bb0-693">Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="26bb0-693">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="26bb0-694">Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-694">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="26bb0-695">Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="26bb0-695">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="26bb0-696">Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-696">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="26bb0-697">Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.</span><span class="sxs-lookup"><span data-stu-id="26bb0-697">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="26bb0-698">Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="26bb0-698">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="26bb0-699">Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="26bb0-699">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="26bb0-700">Die Lokalisierung wird in der APP behandelt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-700">Localization is handled in the app:</span></span>

1. <span data-ttu-id="26bb0-701">Der Browser sendet eine anfängliche http-Anforderung an die app.</span><span class="sxs-lookup"><span data-stu-id="26bb0-701">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="26bb0-702">Die Kultur wird von der Lokalisierungs Middleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-702">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="26bb0-703">Die `OnGet`-Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.</span><span class="sxs-lookup"><span data-stu-id="26bb0-703">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="26bb0-704">Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive Blazor Server-Sitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-704">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="26bb0-705">Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="26bb0-705">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="26bb0-706">Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="26bb0-706">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="26bb0-707">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="26bb0-707">Provide UI to choose the culture</span></span>

<span data-ttu-id="26bb0-708">Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-708">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="26bb0-709">Der Prozess ähnelt dem, was in einer Web-App geschieht, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen,&mdash;der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="26bb0-709">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="26bb0-710">Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller.</span><span class="sxs-lookup"><span data-stu-id="26bb0-710">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="26bb0-711">Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.</span><span class="sxs-lookup"><span data-stu-id="26bb0-711">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="26bb0-712">Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:</span><span class="sxs-lookup"><span data-stu-id="26bb0-712">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="26bb0-713">Verwenden Sie das Ergebnis der `LocalRedirect` Aktion, um Open Redirect-Angriffe zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="26bb0-713">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="26bb0-714">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-714">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="26bb0-715">Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-715">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="26bb0-716">Verwenden von .net-Lokalisierungs Szenarien in Blazor-apps</span><span class="sxs-lookup"><span data-stu-id="26bb0-716">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="26bb0-717">In Blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:</span><span class="sxs-lookup"><span data-stu-id="26bb0-717">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="26bb0-718">. NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="26bb0-718">.NET's resources system</span></span>
* <span data-ttu-id="26bb0-719">Kulturspezifische Zahlen-und Datums Formatierung</span><span class="sxs-lookup"><span data-stu-id="26bb0-719">Culture-specific number and date formatting</span></span>

<span data-ttu-id="26bb0-720">die `@bind` Funktionalität von Blazorführt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch.</span><span class="sxs-lookup"><span data-stu-id="26bb0-720">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="26bb0-721">Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="26bb0-721">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="26bb0-722">Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-722">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="26bb0-723">`IStringLocalizer<>` wird in Blazor-apps *unterstützt* .</span><span class="sxs-lookup"><span data-stu-id="26bb0-723">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="26bb0-724">die Lokalisierung von `IHtmlLocalizer<>`, `IViewLocalizer<>`und Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in Blazor-apps **nicht unterstützt** .</span><span class="sxs-lookup"><span data-stu-id="26bb0-724">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="26bb0-725">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="26bb0-725">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="26bb0-726">SVG-Bilder (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="26bb0-726">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="26bb0-727">Seit Blazor rendert HTML-Bilder, die vom Browser unterstützt werden, einschließlich der SVG (Scalable Vector Graphics)-*Bilder (SVG),* werden über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-727">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="26bb0-728">Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="26bb0-728">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="26bb0-729">Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-729">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="26bb0-730">Wenn Sie ein `<svg>`-Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="26bb0-730">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="26bb0-731">Beispielsweise werden `<use>` Tags derzeit nicht beachtet, und `@bind` können nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-731">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="26bb0-732">Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="26bb0-732">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26bb0-733">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="26bb0-733">Additional resources</span></span>

* <span data-ttu-id="26bb0-734"><xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von Blazor Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="26bb0-734"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
