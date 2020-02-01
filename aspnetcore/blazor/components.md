---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: d6ba60b20d21636c7f780a80d8fbdb152505a3a3
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928262"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c53e2-103">Erstellen und Verwenden von ASP.net Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="c53e2-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c53e2-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c53e2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c53e2-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c53e2-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c53e2-106">Blazor-apps werden mithilfe von *Komponenten*erstellt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="c53e2-107">Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="c53e2-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c53e2-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c53e2-109">Komponenten sind flexibel und leicht zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="c53e2-110">Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c53e2-111">Komponenten Klassen</span><span class="sxs-lookup"><span data-stu-id="c53e2-111">Component classes</span></span>

<span data-ttu-id="c53e2-112">Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c53e2-113">Eine Komponente in blazor wird formal als *Razor-Komponente*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="c53e2-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="c53e2-115">Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="c53e2-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="c53e2-116">Die Benutzeroberfläche für eine Komponente wird mit HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c53e2-117">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c53e2-118">Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c53e2-119">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="c53e2-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c53e2-120">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c53e2-121">Im `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c53e2-122">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="c53e2-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="c53e2-123">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c53e2-124">Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c53e2-125">Im folgenden Beispiel wird ausgewertet und gerendert:</span><span class="sxs-lookup"><span data-stu-id="c53e2-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c53e2-126">`_headingFontStyle` zum CSS-Eigenschafts Wert für `font-style`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c53e2-127">`_headingText` auf den Inhalt des `<h1>` Elements.</span><span class="sxs-lookup"><span data-stu-id="c53e2-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c53e2-128">Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="c53e2-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="c53e2-129">Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen am Dokumentobjektmodell des Browsers (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="c53e2-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="c53e2-130">Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c53e2-131">Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="c53e2-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="c53e2-132">Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="c53e2-133">In der Regel wird der Namespace einer Komponente aus dem Stamm Namespace der APP und dem Speicherort (Ordner) der Komponente in der APP abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="c53e2-134">Wenn der Stamm Namespace der APP `BlazorApp` ist und sich die `Counter` Komponente im Ordner " *pages* " befindet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="c53e2-135">Der Namespace der `Counter` Komponente ist `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="c53e2-136">Der voll qualifizierte Typname der Komponente ist `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="c53e2-137">Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="c53e2-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="c53e2-138">Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports Razor* -Datei der APP hinzu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="c53e2-139">Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `BlazorApp`ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c53e2-140">Integrieren von Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="c53e2-140">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c53e2-141">Razor-Komponenten können in Razor Pages-und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-141">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="c53e2-142">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab vorgerendert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-142">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="c53e2-143">Wenn Sie eine Razor Pages-oder MVC-App zum Hosten von Razor-Komponenten vorbereiten möchten, befolgen Sie die Anweisungen im Abschnitt *integrieren von Razor-Komponenten in Razor Pages und MVC-apps* des <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> Artikels.</span><span class="sxs-lookup"><span data-stu-id="c53e2-143">To prepare a Razor Pages or MVC app to host Razor components, follow the guidance in the *Integrate Razor components into Razor Pages and MVC apps* section of the <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> article.</span></span>

<span data-ttu-id="c53e2-144">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace, der den Ordner darstellt, entweder der Seite/Ansicht oder der Datei " *_ViewImports. cshtml* " hinzu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-144">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c53e2-145">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c53e2-145">In the following example:</span></span>

* <span data-ttu-id="c53e2-146">Ändern Sie `MyAppNamespace` in den Namespace der app.</span><span class="sxs-lookup"><span data-stu-id="c53e2-146">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="c53e2-147">Wenn ein Ordner mit dem Namen *Components* nicht zum Speichern der Komponenten verwendet wird, ändern Sie `Components` in den Ordner, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-147">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```csharp
@using MyAppNamespace.Components
```

<span data-ttu-id="c53e2-148">Die Datei " *_ViewImports. cshtml* " befindet sich im Ordner " *pages* " einer Razor Pages-APP oder im Ordner " *views* " einer MVC-app.</span><span class="sxs-lookup"><span data-stu-id="c53e2-148">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="c53e2-149">Verwenden Sie das `Component`-taghilfsprogramm, um eine Komponente aus einer Seite oder Sicht zu Rendering:</span><span class="sxs-lookup"><span data-stu-id="c53e2-149">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="c53e2-150">Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="c53e2-150">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="c53e2-151">Beispielsweise können Sie einen Wert für `IncrementAmount` angeben, da der Typ der `IncrementAmount` ein `int`ist, bei dem es sich um einen primitiven Typ handelt, der vom JSON-Serialisierungsprogramm unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-151">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="c53e2-152">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="c53e2-152">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c53e2-153">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-153">Is prerendered into the page.</span></span>
* <span data-ttu-id="c53e2-154">Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="c53e2-154">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c53e2-155">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="c53e2-155">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="c53e2-156">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine blazor-Server-app.</span><span class="sxs-lookup"><span data-stu-id="c53e2-156">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c53e2-157">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-157">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="c53e2-158">Rendert einen Marker für eine blazor-Server-app.</span><span class="sxs-lookup"><span data-stu-id="c53e2-158">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c53e2-159">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-159">Output from the component isn't included.</span></span> <span data-ttu-id="c53e2-160">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-160">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="c53e2-161">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="c53e2-161">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c53e2-162">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="c53e2-162">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c53e2-163">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="c53e2-163">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="c53e2-164">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-164">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c53e2-165">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-165">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="c53e2-166">Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `Component`-taghilfsprogramm finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-166">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="c53e2-167">Taghilfsprogramme werden in Komponenten nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-167">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="c53e2-168">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden*in Razor-Komponenten (Razor* -Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-168">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="c53e2-169">Um taghilfsobjekte in blazor bereitzustellen, erstellen Sie eine-Komponente mit der gleichen Funktionalität wie das taghilfsprogramm, und verwenden Sie stattdessen die-Komponente.</span><span class="sxs-lookup"><span data-stu-id="c53e2-169">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="c53e2-170">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="c53e2-170">Use components</span></span>

<span data-ttu-id="c53e2-171">Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-171">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c53e2-172">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-172">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c53e2-173">Bei der Attribut Bindung wird Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="c53e2-173">Attribute binding is case sensitive.</span></span> <span data-ttu-id="c53e2-174">Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="c53e2-174">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="c53e2-175">Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent` Instanz:</span><span class="sxs-lookup"><span data-stu-id="c53e2-175">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="c53e2-176">*Komponenten/headingcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-176">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="c53e2-177">Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-177">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="c53e2-178">Durch das Hinzufügen einer `@using`-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-178">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="c53e2-179">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-179">Component parameters</span></span>

<span data-ttu-id="c53e2-180">Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-180">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c53e2-181">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-181">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c53e2-182">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="c53e2-183">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft der `ChildComponent`fest.</span><span class="sxs-lookup"><span data-stu-id="c53e2-183">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c53e2-184">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-184">*Pages/ParentComponent.razor*:</span></span>

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

## <a name="child-content"></a><span data-ttu-id="c53e2-185">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="c53e2-185">Child content</span></span>

<span data-ttu-id="c53e2-186">Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-186">Components can set the content of another component.</span></span> <span data-ttu-id="c53e2-187">Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-187">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c53e2-188">Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-188">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="c53e2-189">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="c53e2-189">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c53e2-190">Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`des Bootstrap-Panels gerendert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-190">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c53e2-191">*Komponenten/childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-191">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c53e2-192">Die Eigenschaft, die den `RenderFragment` Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-192">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c53e2-193">Der `ParentComponent` in der Beispiel-App kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>` Tags platziert wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-193">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c53e2-194">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-194">*Pages/ParentComponent.razor*:</span></span>

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

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c53e2-195">Attribut-Verteilung und beliebige Parameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-195">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c53e2-196">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering.</span><span class="sxs-lookup"><span data-stu-id="c53e2-196">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c53e2-197">Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`](xref:mvc/views/razor#attributes) Razor-Direktive gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-197">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="c53e2-198">Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-198">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c53e2-199">Beispielsweise kann es mühsam sein, Attribute für eine `<input>`, die viele Parameter unterstützt, separat zu definieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-199">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c53e2-200">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attribut-splatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-200">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="c53e2-201">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichen folgen Schlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-201">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="c53e2-202">Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch eine Option.</span><span class="sxs-lookup"><span data-stu-id="c53e2-202">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="c53e2-203">Die gerenderten `<input>` Elemente mit beiden Ansätzen sind identisch:</span><span class="sxs-lookup"><span data-stu-id="c53e2-203">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="c53e2-204">Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter mithilfe des `[Parameter]`-Attributs, wobei die `CaptureUnmatchedValues`-Eigenschaft auf `true`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-204">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c53e2-205">Die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` ermöglicht dem Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-205">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c53e2-206">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-206">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="c53e2-207">Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichen folgen Schlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="c53e2-207">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c53e2-208">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.</span><span class="sxs-lookup"><span data-stu-id="c53e2-208">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="c53e2-209">Die Position `@attributes` relativ zur Position von Element Attributen ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="c53e2-209">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="c53e2-210">Wenn `@attributes` auf dem Element splatted sind, werden die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-210">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="c53e2-211">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child` Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-211">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="c53e2-212">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="c53e2-212">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c53e2-213">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-213">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c53e2-214">Das `extra`-Attribut der `Child` Komponente ist rechts von `@attributes`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-214">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="c53e2-215">Die gerenderte `<div>` der `Parent` Komponente enthält `extra="5"`, wenn Sie über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-215">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="c53e2-216">Im folgenden Beispiel wird die Reihenfolge der `extra` und `@attributes` im `<div>`der `Child` Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-216">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="c53e2-217">" *Parser Component. Razor*":</span><span class="sxs-lookup"><span data-stu-id="c53e2-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c53e2-218">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-218">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c53e2-219">Der gerenderte `<div>` in der `Parent` Komponente enthält beim Durchlaufen des zusätzlichen Attributs `extra="10"`:</span><span class="sxs-lookup"><span data-stu-id="c53e2-219">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="c53e2-220">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="c53e2-220">Data binding</span></span>

<span data-ttu-id="c53e2-221">Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [`@bind`](xref:mvc/views/razor#bind) -Attribut.</span><span class="sxs-lookup"><span data-stu-id="c53e2-221">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="c53e2-222">Im folgenden Beispiel wird eine `CurrentValue`-Eigenschaft an den Wert des Textfelds gebunden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-222">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c53e2-223">Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-223">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="c53e2-224">Das Textfeld wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="c53e2-224">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c53e2-225">Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen in der Benutzeroberfläche *normalerweise* sofort nach dem Auslösen eines Ereignis Handlers</span><span class="sxs-lookup"><span data-stu-id="c53e2-225">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c53e2-226">Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:</span><span class="sxs-lookup"><span data-stu-id="c53e2-226">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c53e2-227">Wenn die Komponente gerendert wird, stammt der `value` des Eingabe Elements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c53e2-227">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c53e2-228">Wenn der Benutzer das Textfeld eingibt und den Element Fokus ändert, wird das `onchange`-Ereignis ausgelöst, und die `CurrentValue`-Eigenschaft wird auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-228">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c53e2-229">In der Realität ist die Codegenerierung komplexer, da `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-229">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="c53e2-230">Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.</span><span class="sxs-lookup"><span data-stu-id="c53e2-230">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c53e2-231">Zusätzlich zur Behandlung von `onchange` Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [`@bind-value`](xref:mvc/views/razor#bind) Attribut mit einem `event` Parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-231">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="c53e2-232">Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-232">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c53e2-233">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-233">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c53e2-234">im vorherigen Beispiel wird `@bind-value` gebunden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-234">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="c53e2-235">Der angegebene Ausdruck (`CurrentValue`) zum `value` Attribut des Elements.</span><span class="sxs-lookup"><span data-stu-id="c53e2-235">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="c53e2-236">Ein Änderungs Ereignis Delegat für das Ereignis, das durch `@bind-value:event`angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-236">A change event delegate to the event specified by `@bind-value:event`.</span></span>

<span data-ttu-id="c53e2-237">**Nicht zu erteilbare Werte**</span><span class="sxs-lookup"><span data-stu-id="c53e2-237">**Unparsable values**</span></span>

<span data-ttu-id="c53e2-238">Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-238">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c53e2-239">Als Beispiel dient das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="c53e2-239">Consider the following scenario:</span></span>

* <span data-ttu-id="c53e2-240">Ein `<input>`-Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-240">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="c53e2-241">Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="c53e2-241">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="c53e2-242">Im vorangehenden Szenario wird der Wert des Elements auf `123`zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-242">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c53e2-243">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c53e2-243">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="c53e2-244">Standardmäßig gilt die Bindung für das `onchange` Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="c53e2-244">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c53e2-245">Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-245">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="c53e2-246">Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-246">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c53e2-247">Wenn das `oninput` Ereignis mit einem `int`gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.` Zeichen eingibt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-247">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="c53e2-248">Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c53e2-248">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="c53e2-249">Es gibt Szenarios, in denen das Wiederherstellen des Werts für das `oninput` Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht zu testbaren `<input>` Wert löschen darf.</span><span class="sxs-lookup"><span data-stu-id="c53e2-249">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c53e2-250">Zu Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="c53e2-250">Alternatives include:</span></span>

* <span data-ttu-id="c53e2-251">Verwenden Sie das `oninput`-Ereignis nicht.</span><span class="sxs-lookup"><span data-stu-id="c53e2-251">Don't use the `oninput` event.</span></span> <span data-ttu-id="c53e2-252">Verwenden Sie das Standard `onchange` Ereignis (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-252">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="c53e2-253">Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik bereit,</span><span class="sxs-lookup"><span data-stu-id="c53e2-253">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="c53e2-254">Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-254">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="c53e2-255">Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-255">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c53e2-256">Komponenten Validierungs Komponenten:</span><span class="sxs-lookup"><span data-stu-id="c53e2-256">Form validation components:</span></span>
  * <span data-ttu-id="c53e2-257">Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler auf dem zugeordneten `EditContext`zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-257">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="c53e2-258">Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-258">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="c53e2-259">**Globalisierung**</span><span class="sxs-lookup"><span data-stu-id="c53e2-259">**Globalization**</span></span>

<span data-ttu-id="c53e2-260">`@bind` Werte für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-260">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="c53e2-261">Der Zugriff auf die aktuelle Kultur ist über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft möglich.</span><span class="sxs-lookup"><span data-stu-id="c53e2-261">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="c53e2-262">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-262">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="c53e2-263">Die vorangehenden Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-263">The preceding field types:</span></span>

* <span data-ttu-id="c53e2-264">Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-264">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="c53e2-265">Darf keinen frei Form Text enthalten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-265">Can't contain free-form text.</span></span>
* <span data-ttu-id="c53e2-266">Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.</span><span class="sxs-lookup"><span data-stu-id="c53e2-266">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="c53e2-267">Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-267">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="c53e2-268">`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName> zum Auswerten und Formatieren eines Werts bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-268">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="c53e2-269">Die Angabe einer Kultur ist nicht empfehlenswert, wenn die Feldtypen `date` und `number` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-269">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="c53e2-270">`date` und `number` verfügen über eine integrierte Unterstützung für blazor, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-270">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="c53e2-271">Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .</span><span class="sxs-lookup"><span data-stu-id="c53e2-271">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="c53e2-272">**Format Zeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="c53e2-272">**Format strings**</span></span>

<span data-ttu-id="c53e2-273">Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="c53e2-273">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="c53e2-274">Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c53e2-274">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c53e2-275">Im vorangehenden Code ist der Feldtyp (`type`) des `<input>` Elements standardmäßig `text`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-275">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c53e2-276">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-276">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c53e2-277"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c53e2-277"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c53e2-278"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c53e2-278"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c53e2-279">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf die `value` des `<input>` Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c53e2-279">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c53e2-280">Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange` Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-280">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c53e2-281">Es wird nicht empfohlen, ein Format für den `date` Feldtyp anzugeben, da blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-281">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="c53e2-282">Verwenden Sie trotz der Empfehlung nur das `yyyy-MM-dd` Datumsformat für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem `date` Feldtyp bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-282">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="c53e2-283">**Komponenten Parameter**</span><span class="sxs-lookup"><span data-stu-id="c53e2-283">**Component parameters**</span></span>

<span data-ttu-id="c53e2-284">Die Bindung erkennt Komponenten Parameter, bei denen `@bind-{property}` einen Eigenschafts Wert auf Komponenten binden kann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-284">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="c53e2-285">Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year` Component-Parameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="c53e2-285">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="c53e2-286">`EventCallback<T>` wird im Abschnitt [EventCallback](#eventcallback) erläutert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-286">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="c53e2-287">Die folgende übergeordnete Komponente verwendet `ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten an den `Year`-Parameter in der untergeordneten Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-287">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="c53e2-288">Beim Laden des `ParentComponent` wird das folgende Markup erzeugt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-288">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c53e2-289">Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent`geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-289">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c53e2-290">Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-290">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c53e2-291">Der `Year`-Parameter ist bindbar, da er über ein Begleit `YearChanged` Ereignis verfügt, das mit dem Typ des `Year` Parameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-291">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c53e2-292">Gemäß der Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im wesentlichen Äquivalent zum Schreiben:</span><span class="sxs-lookup"><span data-stu-id="c53e2-292">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c53e2-293">Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` Attributs an einen entsprechenden Ereignishandler gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-293">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="c53e2-294">Beispielsweise kann die-Eigenschaft `MyProp` an `MyEventHandler` mithilfe der folgenden beiden Attribute gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-294">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

<span data-ttu-id="c53e2-295">**Options Felder**</span><span class="sxs-lookup"><span data-stu-id="c53e2-295">**Radio buttons**</span></span>

<span data-ttu-id="c53e2-296">Informationen zum Binden an Options Felder in einem Formular finden Sie unter <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-296">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

## <a name="event-handling"></a><span data-ttu-id="c53e2-297">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="c53e2-297">Event handling</span></span>

<span data-ttu-id="c53e2-298">Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit.</span><span class="sxs-lookup"><span data-stu-id="c53e2-298">Razor components provide event handling features.</span></span> <span data-ttu-id="c53e2-299">Für ein HTML-Element Attribut mit dem Namen `on{EVENT}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="c53e2-299">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="c53e2-300">Der Name des Attributs wird immer [`@on{EVENT}`](xref:mvc/views/razor#onevent)formatiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-300">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="c53e2-301">Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-301">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c53e2-302">Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-302">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c53e2-303">Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-303">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c53e2-304">[Statehaschte](xref:blazor/lifecycle#state-changes)muss nicht manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-304">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="c53e2-305">Ausnahmen werden protokolliert, wenn Sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-305">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c53e2-306">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-306">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

### <a name="event-argument-types"></a><span data-ttu-id="c53e2-307">Ereignis Argument Typen</span><span class="sxs-lookup"><span data-stu-id="c53e2-307">Event argument types</span></span>

<span data-ttu-id="c53e2-308">Für einige Ereignisse sind Ereignis Argument Typen zulässig.</span><span class="sxs-lookup"><span data-stu-id="c53e2-308">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c53e2-309">Wenn der Zugriff auf einen dieser Ereignis Typen nicht erforderlich ist, ist er im Methoden Aufrufvorgang nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c53e2-309">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="c53e2-310">Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-310">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="c53e2-311">Event</span><span class="sxs-lookup"><span data-stu-id="c53e2-311">Event</span></span>            | <span data-ttu-id="c53e2-312">Klasse</span><span class="sxs-lookup"><span data-stu-id="c53e2-312">Class</span></span>                | <span data-ttu-id="c53e2-313">DOM-Ereignisse und-Notizen</span><span class="sxs-lookup"><span data-stu-id="c53e2-313">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="c53e2-314">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="c53e2-314">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="c53e2-315">`oncut`ist `oncopy`ist `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c53e2-315">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c53e2-316">Hinein</span><span class="sxs-lookup"><span data-stu-id="c53e2-316">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="c53e2-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c53e2-317">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c53e2-318">`DataTransfer` und `DataTransferItem` die gezogenen Elementdaten speichern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-318">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="c53e2-319">Fehler</span><span class="sxs-lookup"><span data-stu-id="c53e2-319">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="c53e2-320">Event</span><span class="sxs-lookup"><span data-stu-id="c53e2-320">Event</span></span>            | `EventArgs`          | <span data-ttu-id="c53e2-321">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="c53e2-321">*General*</span></span><br><span data-ttu-id="c53e2-322">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c53e2-322">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c53e2-323">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="c53e2-323">*Clipboard*</span></span><br><span data-ttu-id="c53e2-324">`onbeforecut`ist `onbeforecopy`ist `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c53e2-324">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c53e2-325">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="c53e2-325">*Input*</span></span><br><span data-ttu-id="c53e2-326">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c53e2-326">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c53e2-327">*Medien*</span><span class="sxs-lookup"><span data-stu-id="c53e2-327">*Media*</span></span><br><span data-ttu-id="c53e2-328">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c53e2-328">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="c53e2-329">Fokus</span><span class="sxs-lookup"><span data-stu-id="c53e2-329">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="c53e2-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c53e2-330">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c53e2-331">Beinhaltet keine Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-331">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c53e2-332">Input</span><span class="sxs-lookup"><span data-stu-id="c53e2-332">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="c53e2-333">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c53e2-333">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c53e2-334">Tastatur</span><span class="sxs-lookup"><span data-stu-id="c53e2-334">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="c53e2-335">`onkeydown`ist `onkeypress`ist `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c53e2-335">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c53e2-336">Maus</span><span class="sxs-lookup"><span data-stu-id="c53e2-336">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="c53e2-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c53e2-337">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c53e2-338">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="c53e2-338">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="c53e2-339">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c53e2-339">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c53e2-340">Mausrad</span><span class="sxs-lookup"><span data-stu-id="c53e2-340">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="c53e2-341">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c53e2-341">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c53e2-342">Fortschritt</span><span class="sxs-lookup"><span data-stu-id="c53e2-342">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="c53e2-343">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c53e2-343">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c53e2-344">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="c53e2-344">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="c53e2-345">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c53e2-345">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c53e2-346">`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar.</span><span class="sxs-lookup"><span data-stu-id="c53e2-346">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c53e2-347">Informationen zu den Eigenschaften und dem Ereignis Behandlungs Verhalten der Ereignisse in der vorangehenden Tabelle finden Sie unter [EventArgs classes in the Reference Source (dotnet/aspnetcore Release/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c53e2-347">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="c53e2-348">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="c53e2-348">Lambda expressions</span></span>

<span data-ttu-id="c53e2-349">Lambda-Ausdrücke können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-349">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c53e2-350">Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-350">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c53e2-351">Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufgerufen wird, um ein Ereignis Argument (`MouseEventArgs`) und seine Schaltflächen Nummer (`buttonNumber`) zu übergeben, wenn es in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-351">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="c53e2-352">Verwenden Sie die Schleifenvariable (`i`) in einer `for`-Schleife **nicht** direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="c53e2-352">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c53e2-353">Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch `i`Wert in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-353">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c53e2-354">Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-354">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="c53e2-355">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c53e2-355">EventCallback</span></span>

<span data-ttu-id="c53e2-356">Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt&mdash;z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-356">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c53e2-357">Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie eine `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-357">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c53e2-358">Eine übergeordnete Komponente kann dem `EventCallback`einer untergeordneten Komponente eine Rückruf Methode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-358">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c53e2-359">Der `ChildComponent` in der Beispiel-app (*Components/childcomponent. Razor*) veranschaulicht, wie der `onclick` Handler einer Schaltfläche für den Empfang eines `EventCallback` Delegaten aus dem `ParentComponent`des Beispiels eingerichtet ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-359">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c53e2-360">Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-360">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c53e2-361">Der `ParentComponent` legt die `EventCallback<T>` (`OnClick`) des untergeordneten Elements auf die `ShowMessage` Methode fest.</span><span class="sxs-lookup"><span data-stu-id="c53e2-361">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClick`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c53e2-362">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-362">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="c53e2-363">Wenn die Schaltfläche im `ChildComponent`ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-363">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c53e2-364">Die `ShowMessage`-Methode des `ParentComponent`wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-364">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c53e2-365">`_messageText` wird aktualisiert und in der `ParentComponent`angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-365">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c53e2-366">In der-Methode des Rückrufs (`ShowMessage`) ist kein aufzurufende [statehasch-](xref:blazor/lifecycle#state-changes) Methode erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c53e2-366">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c53e2-367">`StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent`zu übertragen, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-367">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c53e2-368">`EventCallback` und `EventCallback<T>` die asynchrone Delegaten zulassen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-368">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c53e2-369">`EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="c53e2-369">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c53e2-370">`EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="c53e2-370">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c53e2-371">Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf die <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="c53e2-371">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c53e2-372">Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.</span><span class="sxs-lookup"><span data-stu-id="c53e2-372">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c53e2-373">Bevorzugen der stark typisierten `EventCallback<T>` über `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-373">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c53e2-374">`EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehler Feedback.</span><span class="sxs-lookup"><span data-stu-id="c53e2-374">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c53e2-375">Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional.</span><span class="sxs-lookup"><span data-stu-id="c53e2-375">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c53e2-376">Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-376">Use `EventCallback` when there's no value passed to the callback.</span></span>

### <a name="prevent-default-actions"></a><span data-ttu-id="c53e2-377">Standard Aktionen verhindern</span><span class="sxs-lookup"><span data-stu-id="c53e2-377">Prevent default actions</span></span>

<span data-ttu-id="c53e2-378">Verwenden Sie das [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) direktivenattribut, um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-378">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c53e2-379">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und sich der Element Fokus auf einem Textfeld befindet, zeigt ein Browser normalerweise das Schlüsselzeichen im Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="c53e2-379">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c53e2-380">Im folgenden Beispiel wird das Standardverhalten verhindert, indem das `@onkeypress:preventDefault` direktivenattribut angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-380">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c53e2-381">Der Wert des Leistungs Zählers wird erhöht, und der **+** Schlüssel wird nicht in den Wert des `<input>` Elements aufgezeichnet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-381">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c53e2-382">Das Angeben des `@on{EVENT}:preventDefault` Attributs ohne Wert entspricht `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-382">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c53e2-383">Der Wert des-Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="c53e2-383">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c53e2-384">Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool` Feld, das entweder auf `true` oder `false`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-384">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="c53e2-385">Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-385">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="c53e2-386">Der Ereignishandler und die Vermeidung von Standard Aktions Szenarien können unabhängig voneinander verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-386">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="c53e2-387">Ereignis Weitergabe Abbrechen</span><span class="sxs-lookup"><span data-stu-id="c53e2-387">Stop event propagation</span></span>

<span data-ttu-id="c53e2-388">Verwenden Sie das Attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) Direktive, um die Ereignis Weitergabe zu verhindern</span><span class="sxs-lookup"><span data-stu-id="c53e2-388">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c53e2-389">Im folgenden Beispiel wird durch Aktivieren des Kontrollkästchens verhindert, dass Click-Ereignisse aus dem zweiten untergeordneten `<div>` an die übergeordnete `<div>`weitergegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-389">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="chained-bind"></a><span data-ttu-id="c53e2-390">Verkettete Bindung</span><span class="sxs-lookup"><span data-stu-id="c53e2-390">Chained bind</span></span>

<span data-ttu-id="c53e2-391">Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="c53e2-391">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="c53e2-392">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-392">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="c53e2-393">Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-393">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="c53e2-394">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-394">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c53e2-395">Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-395">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="c53e2-396">Die folgende `PasswordField` Komponente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c53e2-396">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="c53e2-397">Legt den Wert eines `<input>` Elements auf eine `Password` Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="c53e2-397">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="c53e2-398">Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c53e2-398">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="c53e2-399">Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-399">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="c53e2-400">Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c53e2-400">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="c53e2-401">Erstellen Sie ein Unterstützungs Feld für `Password` (`_password` im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="c53e2-401">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="c53e2-402">Führen Sie die Überprüfungen oder Trap Fehler im `Password` Setter aus.</span><span class="sxs-lookup"><span data-stu-id="c53e2-402">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="c53e2-403">Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-403">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="c53e2-404">Verweise auf Komponenten erfassen</span><span class="sxs-lookup"><span data-stu-id="c53e2-404">Capture references to components</span></span>

<span data-ttu-id="c53e2-405">Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, z. b. `Show` oder `Reset`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-405">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c53e2-406">So erfassen Sie einen Komponenten Verweis:</span><span class="sxs-lookup"><span data-stu-id="c53e2-406">To capture a component reference:</span></span>

* <span data-ttu-id="c53e2-407">Fügen Sie der untergeordneten Komponente ein [`@ref`](xref:mvc/views/razor#ref) Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-407">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="c53e2-408">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="c53e2-408">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="c53e2-409">Wenn die Komponente gerendert wird, wird das `_loginDialog` Feld mit der `MyLoginDialog` untergeordneten Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-409">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="c53e2-410">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-410">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c53e2-411">Die `_loginDialog` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="c53e2-411">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c53e2-412">Bis zu diesem Punkt sind keine Verweise mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-412">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="c53e2-413">Um Komponenten Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, verwenden Sie die [onafterrenderasync-Methode oder die onafterrendering-Methode](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="c53e2-413">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="c53e2-414">Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion</span><span class="sxs-lookup"><span data-stu-id="c53e2-414">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="c53e2-415">Komponenten Verweise werden nicht an JavaScript-Code übermittelt,&mdash;Sie nur in .NET-Code verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-415">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c53e2-416">Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-416">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c53e2-417">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-417">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c53e2-418">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-418">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="c53e2-419">Komponenten Methoden extern aufrufen, um den Status zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-419">Invoke component methods externally to update state</span></span>

<span data-ttu-id="c53e2-420">Blazor verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-420">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="c53e2-421">Die [Lebenszyklus Methoden](xref:blazor/lifecycle) einer Komponente und alle Ereignis Rückrufe, die von blazor ausgelöst werden, werden in dieser `SynchronizationContext`ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-421">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="c53e2-422">Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an die `SynchronizationContext`von blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-422">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="c53e2-423">Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="c53e2-423">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="c53e2-424">Verwendung des `NotifierService` zum Aktualisieren einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-424">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="c53e2-425">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify` Methode der Komponente außerhalb der `SynchronizationContext`von blazor auf.</span><span class="sxs-lookup"><span data-stu-id="c53e2-425">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="c53e2-426">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-426">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c53e2-427">Verwenden Sie \@Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-427">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c53e2-428">Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss der diffalling-Algorithmus von blazor entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-428">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c53e2-429">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-429">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c53e2-430">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c53e2-430">Consider the following example:</span></span>

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

<span data-ttu-id="c53e2-431">Der Inhalt der `People` Sammlung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-431">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c53e2-432">Wenn die Komponente erneut ausgeführt wird, kann sich die `<DetailsEditor>` Komponente ändern, um unterschiedliche `Details` Parameterwerte zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-432">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c53e2-433">Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-433">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c53e2-434">In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="c53e2-434">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c53e2-435">Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-435">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="c53e2-436">`@key` bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-436">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="c53e2-437">Wenn sich die `People`-Auflistung ändert, behält der diff-Algorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="c53e2-437">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c53e2-438">Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>` Instanz aus der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-438">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c53e2-439">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-439">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c53e2-440">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>` Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-440">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c53e2-441">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-441">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c53e2-442">Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-442">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c53e2-443">In einigen Szenarios minimiert die Verwendung von `@key` die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="c53e2-443">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c53e2-444">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="c53e2-444">Keys are local to each container element or component.</span></span> <span data-ttu-id="c53e2-445">Schlüssel werden nicht global über das Dokument hinweg verglichen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-445">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c53e2-446">Verwendung \@Schlüssels</span><span class="sxs-lookup"><span data-stu-id="c53e2-446">When to use \@key</span></span>

<span data-ttu-id="c53e2-447">In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. b. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um die `@key`zu definieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-447">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="c53e2-448">Sie können auch `@key` verwenden, um zu verhindern, dass blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="c53e2-448">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="c53e2-449">Wenn `@currentPerson` geändert wird, erzwingt die `@key` Attribute-Direktive, dass blazor den gesamten `<div>` und seine Nachfolger verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-449">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c53e2-450">Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-450">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c53e2-451">Verwendung \@Schlüssels nicht</span><span class="sxs-lookup"><span data-stu-id="c53e2-451">When not to use \@key</span></span>

<span data-ttu-id="c53e2-452">Beim Durchlaufen der `@key`werden Kosten für die Leistung anfallen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-452">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="c53e2-453">Die Leistungskosten sind nicht groß, sondern geben nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-453">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c53e2-454">Auch wenn `@key` nicht verwendet wird, behält blazor das untergeordnete Element und die Komponenten Instanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="c53e2-454">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c53e2-455">Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-455">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c53e2-456">Welche Werte müssen für \@Schlüssel verwendet werden?</span><span class="sxs-lookup"><span data-stu-id="c53e2-456">What values to use for \@key</span></span>

<span data-ttu-id="c53e2-457">Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-457">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="c53e2-458">Modell Objektinstanzen (z. b. eine `Person`-Instanz, wie im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="c53e2-458">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c53e2-459">Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-459">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c53e2-460">Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string`oder `Guid`).</span><span class="sxs-lookup"><span data-stu-id="c53e2-460">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c53e2-461">Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-461">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="c53e2-462">Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-462">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="c53e2-463">Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.</span><span class="sxs-lookup"><span data-stu-id="c53e2-463">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="c53e2-464">Routing</span><span class="sxs-lookup"><span data-stu-id="c53e2-464">Routing</span></span>

<span data-ttu-id="c53e2-465">Das Routing in blazor wird durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der APP erreicht.</span><span class="sxs-lookup"><span data-stu-id="c53e2-465">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="c53e2-466">Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-466">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c53e2-467">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-467">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="c53e2-468">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-468">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c53e2-469">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-469">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="c53e2-470">*Pages/blazorroute. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-470">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="c53e2-471">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-471">Route parameters</span></span>

<span data-ttu-id="c53e2-472">Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-472">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="c53e2-473">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-473">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="c53e2-474">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-474">*Pages/RouteParameter.razor*:</span></span>

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

<span data-ttu-id="c53e2-475">Optionale Parameter werden nicht unterstützt. Daher werden im obigen Beispiel zwei `@page` Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-475">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="c53e2-476">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="c53e2-476">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c53e2-477">Die zweite `@page`-Direktive übernimmt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-477">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="c53e2-478">Die *catch-all-* Parameter Syntax (`*`/`**`), die den Pfad über mehrere Ordner Grenzen hinweg erfasst, wird in Razor-*Komponenten (Razor*-Komponenten) **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-478">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="c53e2-479">Unterstützung für Teil Klassen</span><span class="sxs-lookup"><span data-stu-id="c53e2-479">Partial class support</span></span>

<span data-ttu-id="c53e2-480">Razor-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-480">Razor components are generated as partial classes.</span></span> <span data-ttu-id="c53e2-481">Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-481">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="c53e2-482">C#Code wird in einem [`@code`](xref:mvc/views/razor#code) -Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-482">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="c53e2-483">Blazor-Vorlagen definieren ihre Razor-Komponenten mithilfe dieses Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="c53e2-483">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="c53e2-484">C#der Code wird in einer Code Behind-Datei platziert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-484">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="c53e2-485">Das folgende Beispiel zeigt die Standard `Counter` Komponente mit einem `@code`-Block in einer APP, die aus einer blazor-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c53e2-485">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="c53e2-486">HTML-Markup, Razor-Code C# und Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="c53e2-486">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="c53e2-487">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-487">*Counter.razor*:</span></span>

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

<span data-ttu-id="c53e2-488">Die `Counter` Komponente kann auch mit einer Code Behind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-488">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="c53e2-489">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-489">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="c53e2-490">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-490">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="c53e2-491">Fügen Sie alle erforderlichen Namespaces der partiellen Klassendatei nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-491">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="c53e2-492">Typische Namespaces, die von Razor-Komponenten verwendet werden, sind:</span><span class="sxs-lookup"><span data-stu-id="c53e2-492">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="c53e2-493">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="c53e2-493">Specify a base class</span></span>

<span data-ttu-id="c53e2-494">Die [`@inherits`](xref:mvc/views/razor#inherits) -Direktive kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-494">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="c53e2-495">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse erben kann, `BlazorRocksBase`, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-495">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="c53e2-496">Die Basisklasse sollte von `ComponentBase`abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-496">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="c53e2-497">*Pages/blazorrocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-497">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="c53e2-498">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-498">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="c53e2-499">Attribut angeben</span><span class="sxs-lookup"><span data-stu-id="c53e2-499">Specify an attribute</span></span>

<span data-ttu-id="c53e2-500">Attribute können in Razor-Komponenten mit der [`@attribute`](xref:mvc/views/razor#attribute) -Direktive angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-500">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="c53e2-501">Im folgenden Beispiel wird das `[Authorize]`-Attribut auf die-Komponenten Klasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-501">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="c53e2-502">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="c53e2-502">Import components</span></span>

<span data-ttu-id="c53e2-503">Der Namespace einer mit Razor erstellten Komponente basiert auf (in Prioritäts Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="c53e2-503">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="c53e2-504">[`@namespace`](xref:mvc/views/razor#namespace) Bezeichnung in Razor-Datei ( *. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="c53e2-504">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="c53e2-505">Das Projekt `RootNamespace` in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="c53e2-505">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="c53e2-506">Der Projektname, der aus dem Dateinamen der Projektdatei ( *. csproj*) und dem Pfad des Projekt Stamms zur Komponente stammt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-506">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="c53e2-507">Das Framework löst z. b. *{Project root}/pages/index.Razor* (*blazorsample. csproj*) in den Namespace `BlazorSample.Pages`auf.</span><span class="sxs-lookup"><span data-stu-id="c53e2-507">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="c53e2-508">Komponenten folgen C# den namens Bindungs Regeln.</span><span class="sxs-lookup"><span data-stu-id="c53e2-508">Components follow C# name binding rules.</span></span> <span data-ttu-id="c53e2-509">Für die `Index` Komponente in diesem Beispiel sind die Komponenten im Bereich alle Komponenten:</span><span class="sxs-lookup"><span data-stu-id="c53e2-509">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="c53e2-510">Im gleichen Ordner, *Seiten*.</span><span class="sxs-lookup"><span data-stu-id="c53e2-510">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="c53e2-511">Die Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-511">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="c53e2-512">Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der [`@using`](xref:mvc/views/razor#using) Direktive von Razor in den Gültigkeitsbereich</span><span class="sxs-lookup"><span data-stu-id="c53e2-512">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="c53e2-513">Wenn eine andere Komponente, `NavMenu.razor`im Ordner " *blazorsample/Shared/* " vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using`-Anweisung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-513">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="c53e2-514">Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, die die [`@using`](xref:mvc/views/razor#using) -Direktive nicht benötigen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-514">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="c53e2-515">Die `global::` Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-515">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c53e2-516">Das Importieren von Komponenten mit Alias `using` Anweisungen (z. b. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-516">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c53e2-517">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-517">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c53e2-518">Beispielsweise wird das Hinzufügen von `@using BlazorSample` und verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-518">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c53e2-519">Attribute für bedingtes HTML-Element</span><span class="sxs-lookup"><span data-stu-id="c53e2-519">Conditional HTML element attributes</span></span>

<span data-ttu-id="c53e2-520">HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-520">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c53e2-521">Wenn der Wert `false` oder `null`ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-521">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c53e2-522">Wenn der Wert `true`ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-522">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c53e2-523">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-523">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c53e2-524">Wenn `IsCompleted` `true`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="c53e2-524">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c53e2-525">Wenn `IsCompleted` `false`ist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="c53e2-525">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c53e2-526">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-526">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="c53e2-527">Einige HTML-Attribute, wie z. b. [Aria-gedrückt](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), funktionieren nicht ordnungsgemäß, wenn der .NET-Typ ein `bool`ist.</span><span class="sxs-lookup"><span data-stu-id="c53e2-527">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="c53e2-528">Verwenden Sie in diesen Fällen einen `string` Typ anstelle eines `bool`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-528">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c53e2-529">Unformatierte HTML</span><span class="sxs-lookup"><span data-stu-id="c53e2-529">Raw HTML</span></span>

<span data-ttu-id="c53e2-530">Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-530">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c53e2-531">Um unformatierten HTML-Code zu erstellen, packen Sie den HTML-Inhalt in einen `MarkupString` Wert</span><span class="sxs-lookup"><span data-stu-id="c53e2-531">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c53e2-532">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-532">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c53e2-533">Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-533">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c53e2-534">Im folgenden Beispiel wird gezeigt, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-534">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="c53e2-535">Komponenten mit Vorlagen</span><span class="sxs-lookup"><span data-stu-id="c53e2-535">Templated components</span></span>

<span data-ttu-id="c53e2-536">Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="c53e2-536">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="c53e2-537">Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="c53e2-537">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="c53e2-538">Einige Beispiele sind:</span><span class="sxs-lookup"><span data-stu-id="c53e2-538">A couple of examples include:</span></span>

* <span data-ttu-id="c53e2-539">Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.</span><span class="sxs-lookup"><span data-stu-id="c53e2-539">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="c53e2-540">Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-540">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="c53e2-541">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-541">Template parameters</span></span>

<span data-ttu-id="c53e2-542">Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-542">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="c53e2-543">Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar.</span><span class="sxs-lookup"><span data-stu-id="c53e2-543">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="c53e2-544">`RenderFragment<T>` nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-544">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="c53e2-545">`TableTemplate` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-545">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="c53e2-546">Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können die Vorlagen Parameter mit untergeordneten Elementen angegeben werden, die den Namen der Parameter (`TableHeader` und `RowTemplate` im folgenden Beispiel) entsprechen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-546">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="c53e2-547">Vorlagen Kontext Parameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-547">Template context parameters</span></span>

<span data-ttu-id="c53e2-548">Komponenten Argumente vom Typ `RenderFragment<T>` als Elemente übergeben, haben einen impliziten Parameter mit dem Namen `context` (z. b. aus dem vorangehenden Codebeispiel `@context.PetId`), Sie können jedoch den Parameternamen mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-548">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="c53e2-549">Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate` Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="c53e2-549">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="c53e2-550">Alternativ können Sie das `Context`-Attribut für das Component-Element angeben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-550">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="c53e2-551">Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter.</span><span class="sxs-lookup"><span data-stu-id="c53e2-551">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="c53e2-552">Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element).</span><span class="sxs-lookup"><span data-stu-id="c53e2-552">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="c53e2-553">Im folgenden Beispiel wird das `Context`-Attribut im `TableTemplate`-Element angezeigt und gilt für alle Vorlagen Parameter:</span><span class="sxs-lookup"><span data-stu-id="c53e2-553">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="c53e2-554">Generisch typisierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="c53e2-554">Generic-typed components</span></span>

<span data-ttu-id="c53e2-555">Auf Vorlagen basierende Komponenten werden oft generisch typisiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-555">Templated components are often generically typed.</span></span> <span data-ttu-id="c53e2-556">Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` Werte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-556">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="c53e2-557">Verwenden Sie zum Definieren einer generischen Komponente die [`@typeparam`](xref:mvc/views/razor#typeparam) -Direktive, um Typparameter anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c53e2-557">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="c53e2-558">Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-558">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="c53e2-559">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-559">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="c53e2-560">Im folgenden Beispiel gibt `TItem="Pet"` den Typ an:</span><span class="sxs-lookup"><span data-stu-id="c53e2-560">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="c53e2-561">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="c53e2-561">Cascading values and parameters</span></span>

<span data-ttu-id="c53e2-562">In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c53e2-562">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="c53e2-563">Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-563">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="c53e2-564">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-564">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="c53e2-565">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="c53e2-565">Theme example</span></span>

<span data-ttu-id="c53e2-566">Im folgenden Beispiel der Beispiel-App gibt die `ThemeInfo`-Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.</span><span class="sxs-lookup"><span data-stu-id="c53e2-566">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="c53e2-567">*Uithmeclasses/themerfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="c53e2-567">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="c53e2-568">Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-568">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="c53e2-569">Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="c53e2-569">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="c53e2-570">Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-570">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="c53e2-571">`ButtonClass` wird der Wert `btn-success` in der Layoutkomponente zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-571">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="c53e2-572">Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierenden Objekts nutzen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-572">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="c53e2-573">`CascadingValuesParametersLayout` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-573">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="c53e2-574">Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="c53e2-574">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="c53e2-575">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-575">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="c53e2-576">In der Beispiel-App bindet die `CascadingValuesParametersTheme` Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="c53e2-576">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="c53e2-577">Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-577">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="c53e2-578">`CascadingValuesParametersTheme` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-578">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="c53e2-579">Um mehrere Werte desselben Typs innerhalb derselben Unterstruktur zu kaskadieren, stellen Sie jeder `CascadingValue` Komponente und der entsprechenden `CascadingParameter`eine eindeutige `Name` Zeichenfolge bereit.</span><span class="sxs-lookup"><span data-stu-id="c53e2-579">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="c53e2-580">Im folgenden Beispiel werden zwei `CascadingValue` Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen kaskadieren:</span><span class="sxs-lookup"><span data-stu-id="c53e2-580">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="c53e2-581">In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-581">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="c53e2-582">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="c53e2-582">TabSet example</span></span>

<span data-ttu-id="c53e2-583">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="c53e2-583">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="c53e2-584">Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.</span><span class="sxs-lookup"><span data-stu-id="c53e2-584">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="c53e2-585">Die Beispiel-App verfügt über eine `ITab` Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="c53e2-585">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="c53e2-586">Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab` Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="c53e2-586">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="c53e2-587">Die untergeordneten `Tab` Komponenten werden nicht explizit als Parameter an die `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-587">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="c53e2-588">Stattdessen sind die untergeordneten `Tab` Komponenten Teil des untergeordneten Inhalts des `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-588">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="c53e2-589">Allerdings müssen die `TabSet` weiterhin über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code erforderlich ist, kann die `TabSet` Komponente *sich selbst als kaskadierenden Wert bereitstellen* , der dann von den Nachfolger `Tab` Komponenten abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-589">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="c53e2-590">`TabSet` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-590">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="c53e2-591">Die Nachfolger `Tab` Komponenten erfassen den enthaltenden `TabSet` als kaskadierenden Parameter, sodass sich die `Tab` Komponenten dem `TabSet` und der Koordinate, auf der die Registerkarte aktiv ist, hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-591">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="c53e2-592">`Tab` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-592">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="c53e2-593">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="c53e2-593">Razor templates</span></span>

<span data-ttu-id="c53e2-594">Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-594">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c53e2-595">Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:</span><span class="sxs-lookup"><span data-stu-id="c53e2-595">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c53e2-596">Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`-und `RenderFragment<T>` Werte angeben und Vorlagen direkt in einer-Komponente darstellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-596">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="c53e2-597">Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-597">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="c53e2-598">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="c53e2-598">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c53e2-599">Manuelle rendertreebuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="c53e2-599">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c53e2-600">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.</span><span class="sxs-lookup"><span data-stu-id="c53e2-600">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c53e2-601">Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="c53e2-601">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c53e2-602">Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-602">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c53e2-603">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere-Komponente integriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="c53e2-603">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c53e2-604">Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-604">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c53e2-605">Wenn Sie `RenderTreeBuilder` Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-605">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c53e2-606">Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-606">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c53e2-607">Wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-607">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c53e2-608">**Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="c53e2-608">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c53e2-609">Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="c53e2-609">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c53e2-610">`BuiltContent` Komponente:</span><span class="sxs-lookup"><span data-stu-id="c53e2-610">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="c53e2-611">Die Typen in `Microsoft.AspNetCore.Components.RenderTree` die Verarbeitung der *Ergebnisse* von Renderingvorgängen zulassen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-611">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="c53e2-612">Hierbei handelt es sich um interne Details der blazor-Framework-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="c53e2-612">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="c53e2-613">Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="c53e2-613">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c53e2-614">Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="c53e2-614">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c53e2-615">Blazor-`.razor` Dateien werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-615">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="c53e2-616">Dies ist möglicherweise ein großer Vorteil für `.razor`, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-616">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="c53e2-617">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="c53e2-617">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="c53e2-618">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-618">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c53e2-619">Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.</span><span class="sxs-lookup"><span data-stu-id="c53e2-619">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c53e2-620">Sehen Sie sich die folgende Razor-Komponenten Datei ( *. Razor*) an:</span><span class="sxs-lookup"><span data-stu-id="c53e2-620">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c53e2-621">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="c53e2-621">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c53e2-622">Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true`ist:</span><span class="sxs-lookup"><span data-stu-id="c53e2-622">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c53e2-623">Sequenz</span><span class="sxs-lookup"><span data-stu-id="c53e2-623">Sequence</span></span> | <span data-ttu-id="c53e2-624">Typ</span><span class="sxs-lookup"><span data-stu-id="c53e2-624">Type</span></span>      | <span data-ttu-id="c53e2-625">importieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-625">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c53e2-626">0</span><span class="sxs-lookup"><span data-stu-id="c53e2-626">0</span></span>        | <span data-ttu-id="c53e2-627">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-627">Text node</span></span> | <span data-ttu-id="c53e2-628">Erster</span><span class="sxs-lookup"><span data-stu-id="c53e2-628">First</span></span>  |
| <span data-ttu-id="c53e2-629">1</span><span class="sxs-lookup"><span data-stu-id="c53e2-629">1</span></span>        | <span data-ttu-id="c53e2-630">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-630">Text node</span></span> | <span data-ttu-id="c53e2-631">Second</span><span class="sxs-lookup"><span data-stu-id="c53e2-631">Second</span></span> |

<span data-ttu-id="c53e2-632">Stellen Sie sich vor, dass `someFlag` `false`wird und das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-632">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c53e2-633">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c53e2-633">This time, the builder receives:</span></span>

| <span data-ttu-id="c53e2-634">Sequenz</span><span class="sxs-lookup"><span data-stu-id="c53e2-634">Sequence</span></span> | <span data-ttu-id="c53e2-635">Typ</span><span class="sxs-lookup"><span data-stu-id="c53e2-635">Type</span></span>       | <span data-ttu-id="c53e2-636">importieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-636">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c53e2-637">1</span><span class="sxs-lookup"><span data-stu-id="c53e2-637">1</span></span>        | <span data-ttu-id="c53e2-638">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-638">Text node</span></span>  | <span data-ttu-id="c53e2-639">Second</span><span class="sxs-lookup"><span data-stu-id="c53e2-639">Second</span></span> |

<span data-ttu-id="c53e2-640">Wenn die Laufzeit einen diff ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:</span><span class="sxs-lookup"><span data-stu-id="c53e2-640">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c53e2-641">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-641">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="c53e2-642">Was schief geht, wenn Sie Programm gesteuert Sequenznummern generieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-642">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="c53e2-643">Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:</span><span class="sxs-lookup"><span data-stu-id="c53e2-643">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c53e2-644">Nun lautet die erste Ausgabe wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-644">Now, the first output is:</span></span>

| <span data-ttu-id="c53e2-645">Sequenz</span><span class="sxs-lookup"><span data-stu-id="c53e2-645">Sequence</span></span> | <span data-ttu-id="c53e2-646">Typ</span><span class="sxs-lookup"><span data-stu-id="c53e2-646">Type</span></span>      | <span data-ttu-id="c53e2-647">importieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-647">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c53e2-648">0</span><span class="sxs-lookup"><span data-stu-id="c53e2-648">0</span></span>        | <span data-ttu-id="c53e2-649">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-649">Text node</span></span> | <span data-ttu-id="c53e2-650">Erster</span><span class="sxs-lookup"><span data-stu-id="c53e2-650">First</span></span>  |
| <span data-ttu-id="c53e2-651">1</span><span class="sxs-lookup"><span data-stu-id="c53e2-651">1</span></span>        | <span data-ttu-id="c53e2-652">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-652">Text node</span></span> | <span data-ttu-id="c53e2-653">Second</span><span class="sxs-lookup"><span data-stu-id="c53e2-653">Second</span></span> |

<span data-ttu-id="c53e2-654">Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="c53e2-654">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c53e2-655">`someFlag` wird im zweiten Rendering `false`, und die Ausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="c53e2-655">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c53e2-656">Sequenz</span><span class="sxs-lookup"><span data-stu-id="c53e2-656">Sequence</span></span> | <span data-ttu-id="c53e2-657">Typ</span><span class="sxs-lookup"><span data-stu-id="c53e2-657">Type</span></span>      | <span data-ttu-id="c53e2-658">importieren</span><span class="sxs-lookup"><span data-stu-id="c53e2-658">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c53e2-659">0</span><span class="sxs-lookup"><span data-stu-id="c53e2-659">0</span></span>        | <span data-ttu-id="c53e2-660">Textknoten</span><span class="sxs-lookup"><span data-stu-id="c53e2-660">Text node</span></span> | <span data-ttu-id="c53e2-661">Second</span><span class="sxs-lookup"><span data-stu-id="c53e2-661">Second</span></span> |

<span data-ttu-id="c53e2-662">Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:</span><span class="sxs-lookup"><span data-stu-id="c53e2-662">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c53e2-663">Ändern Sie den Wert für den ersten Textknoten in `Second`.</span><span class="sxs-lookup"><span data-stu-id="c53e2-663">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c53e2-664">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-664">Remove the second text node.</span></span>

<span data-ttu-id="c53e2-665">Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo die `if/else` branches und Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="c53e2-665">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c53e2-666">Dies führt **doppelt so lange** wie zuvor zu einem diff.</span><span class="sxs-lookup"><span data-stu-id="c53e2-666">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c53e2-667">Dies ist ein sehr einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="c53e2-667">This is a trivial example.</span></span> <span data-ttu-id="c53e2-668">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer.</span><span class="sxs-lookup"><span data-stu-id="c53e2-668">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="c53e2-669">Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="c53e2-669">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="c53e2-670">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="c53e2-670">Guidance and conclusions</span></span>

* <span data-ttu-id="c53e2-671">Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-671">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c53e2-672">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c53e2-672">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c53e2-673">Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik.</span><span class="sxs-lookup"><span data-stu-id="c53e2-673">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c53e2-674">Bevorzugen Sie `.razor` Dateien, und ermöglichen Sie dem Compiler, die Sequenznummern zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="c53e2-674">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="c53e2-675">Wenn Sie manuelle `RenderTreeBuilder` Logik nicht vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die `OpenRegion`/`CloseRegion` aufrufen umschließen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-675">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="c53e2-676">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.</span><span class="sxs-lookup"><span data-stu-id="c53e2-676">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="c53e2-677">Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-677">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c53e2-678">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="c53e2-678">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c53e2-679">Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-679">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="c53e2-680"> verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-680"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c53e2-681">Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und Blazor hat den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die *Razor* -Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-681">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="c53e2-682">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="c53e2-682">Localization</span></span>

Blazor<span data-ttu-id="c53e2-683"> Server-apps werden mithilfe von [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="c53e2-683"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="c53e2-684">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-684">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="c53e2-685">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c53e2-685">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="c53e2-686">Cookies</span><span class="sxs-lookup"><span data-stu-id="c53e2-686">Cookies</span></span>](#cookies)
* [<span data-ttu-id="c53e2-687">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="c53e2-687">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="c53e2-688">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-688">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="c53e2-689">Konfigurieren des Linkers für die Internationalisierung (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="c53e2-689">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="c53e2-690">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="c53e2-690">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="c53e2-691">Weitere Informationen und Anleitungen zum Steuern des linkerverhaltens finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-691">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="c53e2-692">Cookies</span><span class="sxs-lookup"><span data-stu-id="c53e2-692">Cookies</span></span>

<span data-ttu-id="c53e2-693">Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="c53e2-693">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="c53e2-694">Das Cookie wird durch die `OnGet`-Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-694">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="c53e2-695">Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-695">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="c53e2-696">Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-696">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="c53e2-697">Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-697">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="c53e2-698">Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="c53e2-698">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="c53e2-699">Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-699">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="c53e2-700">Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.</span><span class="sxs-lookup"><span data-stu-id="c53e2-700">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="c53e2-701">Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="c53e2-701">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="c53e2-702">Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="c53e2-702">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="c53e2-703">Die Lokalisierung wird in der APP behandelt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-703">Localization is handled in the app:</span></span>

1. <span data-ttu-id="c53e2-704">Der Browser sendet eine anfängliche http-Anforderung an die app.</span><span class="sxs-lookup"><span data-stu-id="c53e2-704">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="c53e2-705">Die Kultur wird von der Lokalisierungs Middleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-705">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="c53e2-706">Die `OnGet`-Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.</span><span class="sxs-lookup"><span data-stu-id="c53e2-706">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="c53e2-707">Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive Blazor Server-Sitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-707">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="c53e2-708">Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="c53e2-708">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="c53e2-709">Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="c53e2-709">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="c53e2-710">Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="c53e2-710">Provide UI to choose the culture</span></span>

<span data-ttu-id="c53e2-711">Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-711">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="c53e2-712">Der Prozess ähnelt dem, was in einer Web-App geschieht, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen,&mdash;der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c53e2-712">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="c53e2-713">Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller.</span><span class="sxs-lookup"><span data-stu-id="c53e2-713">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="c53e2-714">Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.</span><span class="sxs-lookup"><span data-stu-id="c53e2-714">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="c53e2-715">Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:</span><span class="sxs-lookup"><span data-stu-id="c53e2-715">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="c53e2-716">Verwenden Sie das Ergebnis der `LocalRedirect` Aktion, um Open Redirect-Angriffe zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c53e2-716">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="c53e2-717">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-717">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="c53e2-718">Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-718">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="c53e2-719">Verwenden von .net-Lokalisierungs Szenarien in Blazor-apps</span><span class="sxs-lookup"><span data-stu-id="c53e2-719">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="c53e2-720">In Blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c53e2-720">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="c53e2-721">. NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="c53e2-721">.NET's resources system</span></span>
* <span data-ttu-id="c53e2-722">Kulturspezifische Zahlen-und Datums Formatierung</span><span class="sxs-lookup"><span data-stu-id="c53e2-722">Culture-specific number and date formatting</span></span>

<span data-ttu-id="c53e2-723">die `@bind` Funktionalität von Blazorführt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch.</span><span class="sxs-lookup"><span data-stu-id="c53e2-723">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="c53e2-724">Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="c53e2-724">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="c53e2-725">Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-725">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="c53e2-726">`IStringLocalizer<>` wird in Blazor-apps *unterstützt* .</span><span class="sxs-lookup"><span data-stu-id="c53e2-726">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="c53e2-727">die Lokalisierung von `IHtmlLocalizer<>`, `IViewLocalizer<>`und Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in Blazor-apps **nicht unterstützt** .</span><span class="sxs-lookup"><span data-stu-id="c53e2-727">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="c53e2-728">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="c53e2-728">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="c53e2-729">SVG-Bilder (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="c53e2-729">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="c53e2-730">Seit Blazor rendert HTML-Bilder, die vom Browser unterstützt werden, einschließlich der SVG (Scalable Vector Graphics)-*Bilder (SVG),* werden über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-730">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="c53e2-731">Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c53e2-731">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="c53e2-732">Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-732">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="c53e2-733">Wenn Sie ein `<svg>`-Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c53e2-733">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="c53e2-734">Beispielsweise werden `<use>` Tags derzeit nicht beachtet, und `@bind` können nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-734">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="c53e2-735">Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c53e2-735">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c53e2-736">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c53e2-736">Additional resources</span></span>

* <span data-ttu-id="c53e2-737"><xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von Blazor Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="c53e2-737"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
