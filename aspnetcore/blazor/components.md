---
title: Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: a7009bf1cf99a15f3617b47a904d52f5787b9ce1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153517"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="9eb77-103">Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="9eb77-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="9eb77-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="9eb77-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9eb77-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="9eb77-106">-Apps werden mithilfe von *Komponenten* erstellt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-106"> apps are built using *components*.</span></span> <span data-ttu-id="9eb77-107">Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="9eb77-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="9eb77-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="9eb77-109">Komponenten sind flexibel und kompakt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="9eb77-110">Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="9eb77-111">Komponentenklassen</span><span class="sxs-lookup"><span data-stu-id="9eb77-111">Component classes</span></span>

<span data-ttu-id="9eb77-112">Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien ( *.razor*) implementiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="9eb77-113">Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="9eb77-114">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="9eb77-115">Beispielsweise ist *MyCoolComponent.razor* zulässig, *mycoolcomponent.razor* aber nicht.</span><span class="sxs-lookup"><span data-stu-id="9eb77-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="9eb77-116">Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="9eb77-117">Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="9eb77-118">Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="9eb77-119">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="9eb77-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="9eb77-120">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="9eb77-121">Im `@code`-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="9eb77-122">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="9eb77-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="9eb77-123">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="9eb77-124">Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="9eb77-125">Das Beispiel wertet Folgendes aus und führt ein Rendering durch:</span><span class="sxs-lookup"><span data-stu-id="9eb77-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="9eb77-126">`headingFontStyle` in den CSS-Eigenschaftswert für `font-style`</span><span class="sxs-lookup"><span data-stu-id="9eb77-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="9eb77-127">`headingText` in den Inhalt des `<h1>`-Elements</span><span class="sxs-lookup"><span data-stu-id="9eb77-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="9eb77-128">Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="9eb77-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="9eb77-129"> vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.</span><span class="sxs-lookup"><span data-stu-id="9eb77-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="9eb77-130">Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="9eb77-131">Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="9eb77-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="9eb77-132">Nicht für Seiten verwendete Komponenten werden häufig im Ordner *Shared* oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="9eb77-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="9eb77-133">In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="9eb77-134">Wenn der Stammnamespace der App `BlazorApp` ist und sich die `Counter` Komponente im Ordner *Pages* befindet, ist</span><span class="sxs-lookup"><span data-stu-id="9eb77-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="9eb77-135">der Namespace der `Counter`-Komponente `BlazorApp.Pages`, und</span><span class="sxs-lookup"><span data-stu-id="9eb77-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="9eb77-136">der vollqualifizierten Typname der Komponente ist `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="9eb77-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="9eb77-137">Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine `using`-Anweisung der übergeordneten Komponente oder der *_Imports.razor*-Datei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="9eb77-137">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="9eb77-138">Im folgenden Beispiel werden Komponenten im Ordner *Components* (Komponenten) zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-138">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="9eb77-139">Alternativ kann auf eine Komponente direkt verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="9eb77-139">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="9eb77-140">Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components).</span><span class="sxs-lookup"><span data-stu-id="9eb77-140">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="static-assets"></a><span data-ttu-id="9eb77-141">Statische Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9eb77-141">Static assets</span></span>

Blazor<span data-ttu-id="9eb77-142"> befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen unter dem Webstammordner ([wwwroot](xref:fundamentals/index#web-root)) des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="9eb77-142"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="9eb77-143">Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-143">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="9eb77-144">Im folgenden Beispiel wird *logo.png* im Ordner *{Projektstamm}/wwwroot/images* gespeichert:</span><span class="sxs-lookup"><span data-stu-id="9eb77-144">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="9eb77-145">-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).</span><span class="sxs-lookup"><span data-stu-id="9eb77-145"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="9eb77-146">Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9eb77-146">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="9eb77-147">Keine Unterstützung von Taghilfsprogrammen in Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-147">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="9eb77-148">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten ( *.razor*-Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-148">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="9eb77-149">Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-149">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="9eb77-150">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-150">Use components</span></span>

<span data-ttu-id="9eb77-151">Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="9eb77-151">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="9eb77-152">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-152">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="9eb77-153">Das folgende Markup in *Index.razor* rendert eine `HeadingComponent`-Instanz:</span><span class="sxs-lookup"><span data-stu-id="9eb77-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="9eb77-154">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-154">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="9eb77-155">Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="9eb77-156">Durch das Hinzufügen einer `@using`-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-156">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="9eb77-157">Routing</span><span class="sxs-lookup"><span data-stu-id="9eb77-157">Routing</span></span>

<span data-ttu-id="9eb77-158">Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App.</span><span class="sxs-lookup"><span data-stu-id="9eb77-158">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="9eb77-159">Wenn eine Razor-Datei mit einer `@page`-Anweisung kompiliert wird, wird der generierten Klasse <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-159">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="9eb77-160">Zur Laufzeit sucht der Router nach Komponentenklassen mit `RouteAttribute` und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-160">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="9eb77-161">Weitere Informationen finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="9eb77-161">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="9eb77-162">Parameter</span><span class="sxs-lookup"><span data-stu-id="9eb77-162">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="9eb77-163">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="9eb77-163">Route parameters</span></span>

<span data-ttu-id="9eb77-164">Komponenten können Routenparameter von der Routenvorlage empfangen, die in der `@page`-Anweisung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-164">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="9eb77-165">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-165">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="9eb77-166">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-166">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="9eb77-167">Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei `@page`-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-167">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="9eb77-168">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="9eb77-168">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9eb77-169">Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="9eb77-169">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="9eb77-170">Die *Catch-all*-Parametersyntax (`*`/`**`), die den Pfad ordnerübergreifend erfasst, wird in Razor-Komponenten ( *.razor*) **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-170">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="9eb77-171">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="9eb77-171">Component parameters</span></span>

<span data-ttu-id="9eb77-172">Komponenten können *Komponentenparameter enthalten*, die mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem `[Parameter]`-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-172">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="9eb77-173">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-173">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="9eb77-174">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-174">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="9eb77-175">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.</span><span class="sxs-lookup"><span data-stu-id="9eb77-175">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="9eb77-176">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-176">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="9eb77-177">Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld.</span><span class="sxs-lookup"><span data-stu-id="9eb77-177">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="9eb77-178">Weitere Informationen finden Sie im Abschnitt [Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben](#dont-create-components-that-write-to-their-own-parameter-properties).</span><span class="sxs-lookup"><span data-stu-id="9eb77-178">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="9eb77-179">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="9eb77-179">Child content</span></span>

<span data-ttu-id="9eb77-180">Komponenten können den Inhalt anderer Komponenten festlegen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-180">Components can set the content of another component.</span></span> <span data-ttu-id="9eb77-181">Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-181">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="9eb77-182">Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen `RenderFragment`-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-182">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="9eb77-183">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="9eb77-183">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="9eb77-184">Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-184">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="9eb77-185">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="9eb77-186">Die Eigenschaft, die den Inhalt von `RenderFragment` empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-186">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="9eb77-187">Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-187">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="9eb77-188">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-188">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="9eb77-189">Attributsplatting und arbiträre Parameter</span><span class="sxs-lookup"><span data-stu-id="9eb77-189">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="9eb77-190">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern.</span><span class="sxs-lookup"><span data-stu-id="9eb77-190">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="9eb77-191">Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`](xref:mvc/views/razor#attributes)-Anweisung Razor gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-191">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="9eb77-192">Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-192">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="9eb77-193">Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-193">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="9eb77-194">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="9eb77-194">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="9eb77-195">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichenfolgenschlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="9eb77-195">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="9eb77-196">Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch möglich.</span><span class="sxs-lookup"><span data-stu-id="9eb77-196">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="9eb77-197">Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:</span><span class="sxs-lookup"><span data-stu-id="9eb77-197">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="9eb77-198">Definieren Sie einen Komponentenparameter mithilfe des `[Parameter]`-Attributs, bei dem die `CaptureUnmatchedValues`-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="9eb77-198">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="9eb77-199">Wenn die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` festgelegt ist, kann der Parameter alle Attribute abgleichen, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-199">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="9eb77-200">Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues` definieren.</span><span class="sxs-lookup"><span data-stu-id="9eb77-200">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="9eb77-201">Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="9eb77-201">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="9eb77-202">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-202">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="9eb77-203">Die Position von `@attributes` relativ zur Position der Elementattribute ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="9eb77-203">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="9eb77-204">Wenn `@attributes`-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-204">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="9eb77-205">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="9eb77-205">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="9eb77-206">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-206">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9eb77-207">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-207">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9eb77-208">Das `extra`-Attribut der `Child`-Komponente ist rechts von `@attributes` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-208">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="9eb77-209">Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="9eb77-209">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="9eb77-210">Im folgenden Beispiel wird die Reihenfolge von `extra` und `@attributes` im `<div>`-Element der `Child`-Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-210">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="9eb77-211">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-211">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9eb77-212">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-212">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9eb77-213">Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="9eb77-213">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="9eb77-214">Erfassen von Verweisen auf Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-214">Capture references to components</span></span>

<span data-ttu-id="9eb77-215">Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="9eb77-215">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="9eb77-216">So erfassen Sie einen Komponentenverweis:</span><span class="sxs-lookup"><span data-stu-id="9eb77-216">To capture a component reference:</span></span>

* <span data-ttu-id="9eb77-217">Fügen Sie der untergeordneten Komponente ein [`@ref`](xref:mvc/views/razor#ref)-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="9eb77-217">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="9eb77-218">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="9eb77-218">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="9eb77-219">Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `MyLoginDialog`-Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-219">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="9eb77-220">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-220">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9eb77-221">Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="9eb77-221">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="9eb77-222">Bis zu diesem Punkt sind keine Verweise nötig.</span><span class="sxs-lookup"><span data-stu-id="9eb77-222">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="9eb77-223">Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die Methode [OnAfterRenderAsync oder OnAfterRender](xref:blazor/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-223">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="9eb77-224">Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="9eb77-224">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="9eb77-225">Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature.</span><span class="sxs-lookup"><span data-stu-id="9eb77-225">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="9eb77-226">Komponentenverweise werden nicht an JavaScript-Code übermittelt, sondern werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-226">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="9eb77-227">Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern.</span><span class="sxs-lookup"><span data-stu-id="9eb77-227">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="9eb77-228">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-228">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="9eb77-229">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-229">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="9eb77-230">Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status</span><span class="sxs-lookup"><span data-stu-id="9eb77-230">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="9eb77-231"> verwendet einen Synchronisierungskontext (`SynchronizationContext`), um einen einzelnen logischen Ausführungsthread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-231"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="9eb77-232">Die [Lebenszyklusmethoden](xref:blazor/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-232">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="9eb77-233">Der Synchronisierungskontext des Blazor-Servers versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-233">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="9eb77-234">Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht.</span><span class="sxs-lookup"><span data-stu-id="9eb77-234">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="9eb77-235">Zwei Vorgänge werden nicht gleichzeitig ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-235">No two operations execute concurrently.</span></span>

<span data-ttu-id="9eb77-236">Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-236">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="9eb77-237">Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="9eb77-237">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="9eb77-238">Registrieren Sie `NotifierService` als Singleton:</span><span class="sxs-lookup"><span data-stu-id="9eb77-238">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="9eb77-239">Registrieren Sie den Dienst in Blazor WebAssembly in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9eb77-239">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="9eb77-240">Registrieren Sie den Dienst in Blazor Server in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9eb77-240">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="9eb77-241">Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="9eb77-241">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="9eb77-242">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf.</span><span class="sxs-lookup"><span data-stu-id="9eb77-242">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="9eb77-243">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-243">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="9eb77-244">Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-244">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="9eb77-245">Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-245">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="9eb77-246">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.</span><span class="sxs-lookup"><span data-stu-id="9eb77-246">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="9eb77-247">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9eb77-247">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="9eb77-248">Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="9eb77-248">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="9eb77-249">Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann.</span><span class="sxs-lookup"><span data-stu-id="9eb77-249">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="9eb77-250">Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-250">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="9eb77-251">In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="9eb77-251">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="9eb77-252">Der Zuordnungsprozess kann mit dem [`@key`](xref:mvc/views/razor#key)-Anweisungsattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-252">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="9eb77-253">`@key` bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-253">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="9eb77-254">Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="9eb77-254">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="9eb77-255">Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-255">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="9eb77-256">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-256">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9eb77-257">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-257">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="9eb77-258">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-258">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9eb77-259">Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="9eb77-259">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="9eb77-260">In einigen Szenarios minimiert die Verwendung von `@key` die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="9eb77-260">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9eb77-261">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="9eb77-261">Keys are local to each container element or component.</span></span> <span data-ttu-id="9eb77-262">Schlüssel werden nicht dokumentübergreifend global verglichen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-262">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="9eb77-263">Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="9eb77-263">When to use \@key</span></span>

<span data-ttu-id="9eb77-264">In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. B. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um `@key` zu definieren.</span><span class="sxs-lookup"><span data-stu-id="9eb77-264">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="9eb77-265">Sie können `@key` auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="9eb77-265">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="9eb77-266">Wenn `@currentPerson` geändert wird, zwingt die `@key`-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-266">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="9eb77-267">Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-267">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="9eb77-268">Ungeeignete Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="9eb77-268">When not to use \@key</span></span>

<span data-ttu-id="9eb77-269">Bei einem Vergleich mit `@key` wird die Leistung beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-269">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="9eb77-270">Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten `@key` jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.</span><span class="sxs-lookup"><span data-stu-id="9eb77-270">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="9eb77-271">Auch wenn `@key` nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="9eb77-271">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="9eb77-272">Der einzige Vorteil bei der Verwendung von `@key` besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet wird, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-272">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="9eb77-273">Zu verwendende Werte für \@key</span><span class="sxs-lookup"><span data-stu-id="9eb77-273">What values to use for \@key</span></span>

<span data-ttu-id="9eb77-274">Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für `@key` bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="9eb77-274">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="9eb77-275">Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel):</span><span class="sxs-lookup"><span data-stu-id="9eb77-275">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="9eb77-276">Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-276">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="9eb77-277">Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):</span><span class="sxs-lookup"><span data-stu-id="9eb77-277">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="9eb77-278">Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="9eb77-278">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="9eb77-279">Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="9eb77-279">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="9eb77-280">Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.</span><span class="sxs-lookup"><span data-stu-id="9eb77-280">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="9eb77-281">Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben</span><span class="sxs-lookup"><span data-stu-id="9eb77-281">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="9eb77-282">Parameter werden unter den folgenden Bedingungen überschrieben:</span><span class="sxs-lookup"><span data-stu-id="9eb77-282">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="9eb77-283">Der Inhalt einer untergeordneten Komponente wird mit einem `RenderFragment` gerendert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-283">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="9eb77-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird in der übergeordneten Komponente aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="9eb77-285">Parameter werden zurückgesetzt, weil die übergeordnete Komponente erneut gerendert wird, wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufgerufen wird und der untergeordneten Komponente neue Parameterwerte bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-285">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="9eb77-286">Angenommen, die folgende `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-286">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="9eb77-287">rendert untergeordneten Inhalt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-287">Renders child content.</span></span>
* <span data-ttu-id="9eb77-288">schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter um.</span><span class="sxs-lookup"><span data-stu-id="9eb77-288">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="9eb77-289">Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die `StateHasChanged` aufrufen kann:</span><span class="sxs-lookup"><span data-stu-id="9eb77-289">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="9eb77-290">Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-290">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="9eb77-291">Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei.</span><span class="sxs-lookup"><span data-stu-id="9eb77-291">The child components maintain their states as expected.</span></span> <span data-ttu-id="9eb77-292">Wenn `StateHasChanged` in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`).</span><span class="sxs-lookup"><span data-stu-id="9eb77-292">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="9eb77-293">Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-293">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="9eb77-294">Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="9eb77-294">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="9eb77-295">Die folgende `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-295">The following `Expander` component:</span></span>

* <span data-ttu-id="9eb77-296">akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="9eb77-296">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="9eb77-297">weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/lifecycle#component-initialization-methods) zu.</span><span class="sxs-lookup"><span data-stu-id="9eb77-297">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="9eb77-298">verwendet das private Feld, um seinen internen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="9eb77-298">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="9eb77-299">Unterstützung für partielle Klassen</span><span class="sxs-lookup"><span data-stu-id="9eb77-299">Partial class support</span></span>

Razor<span data-ttu-id="9eb77-300">-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-300"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="9eb77-301">-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-301"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="9eb77-302">C#-Code wird in einem [`@code`](xref:mvc/views/razor#code)-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-302">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="9eb77-303">-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="9eb77-303"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="9eb77-304">C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-304">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="9eb77-305">Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem `@code`-Block in einer App, die aus einer Blazor-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9eb77-305">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="9eb77-306">HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="9eb77-306">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="9eb77-307">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-307">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="9eb77-308">Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="9eb77-308">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="9eb77-309">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-309">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="9eb77-310">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-310">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="9eb77-311">Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="9eb77-311">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="9eb77-312">Folgende typischen Namespaces werden von Razor-Komponenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="9eb77-312">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="9eb77-313">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="9eb77-313">Specify a base class</span></span>

<span data-ttu-id="9eb77-314">Die [`@inherits`](xref:mvc/views/razor#inherits)-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-314">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="9eb77-315">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-315">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="9eb77-316">Die Basisklasse sollte von `ComponentBase` abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-316">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="9eb77-317">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-317">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="9eb77-318">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-318">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="9eb77-319">Angeben eines Attributs</span><span class="sxs-lookup"><span data-stu-id="9eb77-319">Specify an attribute</span></span>

<span data-ttu-id="9eb77-320">Attribute können in Razor-Komponenten mit der [`@attribute`](xref:mvc/views/razor#attribute)-Anweisung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-320">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="9eb77-321">Im folgenden Beispiel wird das `[Authorize]`-Attribut auf die Komponentenklasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="9eb77-321">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="9eb77-322">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="9eb77-322">Import components</span></span>

<span data-ttu-id="9eb77-323">Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):</span><span class="sxs-lookup"><span data-stu-id="9eb77-323">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="9eb77-324">[`@namespace`](xref:mvc/views/razor#namespace)-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="9eb77-324">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="9eb77-325">Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)</span><span class="sxs-lookup"><span data-stu-id="9eb77-325">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="9eb77-326">Der Projektname, der aus dem Namen der Projektdatei ( *.csproj*) und dem Pfad vom Projektstamm zur Komponente resultiert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-326">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="9eb77-327">Das Framework löst z. B. *{Projektstamm}/Pages/Index.razor* (*BlazorSample.csproj*) in den Namespace `BlazorSample.Pages` auf.</span><span class="sxs-lookup"><span data-stu-id="9eb77-327">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="9eb77-328">Komponenten folgen den Namensbindungsregeln für C#.</span><span class="sxs-lookup"><span data-stu-id="9eb77-328">Components follow C# name binding rules.</span></span> <span data-ttu-id="9eb77-329">Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-329">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="9eb77-330">Komponenten im selben Ordner (*Pages*)</span><span class="sxs-lookup"><span data-stu-id="9eb77-330">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="9eb77-331">Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben</span><span class="sxs-lookup"><span data-stu-id="9eb77-331">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="9eb77-332">Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der Razor-Anweisung [`@using`](xref:mvc/views/razor#using) in den Geltungsbereich einbezogen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-332">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="9eb77-333">Wenn eine andere Komponente (`NavMenu.razor`) im Ordner *BlazorSample/Shared/* vorhanden ist, kann die Komponente mithilfe der folgenden `@using`-Anweisung in `Index.razor` verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9eb77-333">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="9eb77-334">Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`](xref:mvc/views/razor#using) nicht erforderlich:</span><span class="sxs-lookup"><span data-stu-id="9eb77-334">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="9eb77-335">Die `global::`-Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-335">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="9eb77-336">Das Importieren von Komponenten mit `using`-Aliasanweisungen (z B. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-336">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="9eb77-337">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-337">Partially qualified names aren't supported.</span></span> <span data-ttu-id="9eb77-338">Das Hinzufügen von `@using BlazorSample` und das Verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-338">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="9eb77-339">Attribute für bedingte HTML-Elemente</span><span class="sxs-lookup"><span data-stu-id="9eb77-339">Conditional HTML element attributes</span></span>

<span data-ttu-id="9eb77-340">HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-340">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="9eb77-341">Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-341">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="9eb77-342">Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="9eb77-342">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="9eb77-343">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="9eb77-343">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="9eb77-344">Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="9eb77-344">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="9eb77-345">Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="9eb77-345">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="9eb77-346">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="9eb77-346">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="9eb77-347">Einige HTML-Attribute wie [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-347">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="9eb77-348">Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="9eb77-348">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="9eb77-349">Unformatierter HTML-Code</span><span class="sxs-lookup"><span data-stu-id="9eb77-349">Raw HTML</span></span>

<span data-ttu-id="9eb77-350">Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-350">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="9eb77-351">Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-351">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="9eb77-352">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-352">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="9eb77-353">Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-353">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="9eb77-354">Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="9eb77-354">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="9eb77-355">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="9eb77-355">Cascading values and parameters</span></span>

<span data-ttu-id="9eb77-356">In einigen Szenarios ist es unpraktisch, Daten mithilfe von [Komponentenparametern](#component-parameters) von einer Vorgängerkomponente an eine Nachfolgerkomponente zu übertragen. Das gilt insbesondere, wenn es mehrere Komponentenebenen gibt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-356">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="9eb77-357">Kaskadierende Werte und Parameter lösen dieses Problem, indem es Vorgängerkomponenten einfach ermöglicht wird, für alle Nachfolgerkomponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-357">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="9eb77-358">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="9eb77-358">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="9eb77-359">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="9eb77-359">Theme example</span></span>

<span data-ttu-id="9eb77-360">Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo`-Klasse die Designinformationen an, die in der Komponentenhierarchie nach unten weitergegeben werden, sodass alle Schaltflächen innerhalb eines bestimmten Teils der App denselben Stil aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-360">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="9eb77-361">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="9eb77-361">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="9eb77-362">Eine Vorgängerkomponente kann einen kaskadierenden Wert mithilfe der CascadingValue-Komponente bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-362">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="9eb77-363">Die `CascadingValue`-Komponente umschließt eine Teilstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="9eb77-363">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="9eb77-364">Beispielsweise gibt die Beispiel-App Designinformationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-364">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="9eb77-365">`ButtonClass` wird in der Layoutkomponente der Wert `btn-success` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-365">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="9eb77-366">Jede Nachfolgerkomponente kann diese Eigenschaft über das kaskadierende `ThemeInfo`-Objekt nutzen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-366">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="9eb77-367">`CascadingValuesParametersLayout`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-367">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="9eb77-368">Komponenten deklarieren kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs, um kaskadierende Werte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-368">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="9eb77-369">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-369">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="9eb77-370">In der Beispiel-App bindet die `CascadingValuesParametersTheme`-Komponente den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="9eb77-370">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="9eb77-371">Der Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-371">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="9eb77-372">`CascadingValuesParametersTheme`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-372">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="9eb77-373">Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder `CascadingValue`-Komponente und dem entsprechenden `CascadingParameter`-Attribut eine eindeutige `Name`-Zeichenfolge bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9eb77-373">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="9eb77-374">Im folgenden Beispiel kaskadieren zwei `CascadingValue`-Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen:</span><span class="sxs-lookup"><span data-stu-id="9eb77-374">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="9eb77-375">In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre Werte nach Namen von den entsprechenden kaskadierenden Werten in der Vorgängerkomponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-375">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="9eb77-376">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9eb77-376">TabSet example</span></span>

<span data-ttu-id="9eb77-377">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponentenhierarchie.</span><span class="sxs-lookup"><span data-stu-id="9eb77-377">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="9eb77-378">Sehen Sie sich z. B. das folgende *TabSet*-Beispiel in der Beispiel-App an.</span><span class="sxs-lookup"><span data-stu-id="9eb77-378">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="9eb77-379">Die Beispiel-App enthält eine `ITab`-Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="9eb77-379">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="9eb77-380">Die `CascadingValuesParametersTabSet`-Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab`-Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="9eb77-380">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="9eb77-381">Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-381">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="9eb77-382">Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="9eb77-382">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="9eb77-383">Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben*, der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="9eb77-383">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="9eb77-384">`TabSet`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-384">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="9eb77-385">Die `Tab`-Nachfolgerkomponenten erfassen die enthaltende `TabSet`-Komponente als kaskadierenden Parameter. Das bedeutet, dass die `Tab`-Komponenten sich zu `TabSet` hinzufügen und koordinieren, welche Registerkarte aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="9eb77-385">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="9eb77-386">`Tab`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="9eb77-386">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="9eb77-387">-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="9eb77-387"> templates</span></span>

<span data-ttu-id="9eb77-388">Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-388">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="9eb77-389">Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:</span><span class="sxs-lookup"><span data-stu-id="9eb77-389">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="9eb77-390">Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`- und `RenderFragment<T>`-Werte angeben und Vorlagen direkt in einer-Komponente rendern können.</span><span class="sxs-lookup"><span data-stu-id="9eb77-390">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="9eb77-391">Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/templated-components) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-391">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="9eb77-392">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="9eb77-392">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="9eb77-393">SVG-Bilder</span><span class="sxs-lookup"><span data-stu-id="9eb77-393">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="9eb77-394">Da Blazor HTML rendert, werden browsergestützte Bilder wie SVG-Bilder (Scalable Vector Graphics, skalierbare Vektorgrafiken, *.svg*) über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-394">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="9eb77-395">Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei ( *.css*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="9eb77-395">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="9eb77-396">SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb77-396">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="9eb77-397">Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei ( *.razor*) einfügen, wird das grundlegende Bildrendering unterstützt, aber viele fortgeschrittene Szenarios noch nicht.</span><span class="sxs-lookup"><span data-stu-id="9eb77-397">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="9eb77-398">Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und `@bind` kann nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9eb77-398">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="9eb77-399">Wir planen, diese Einschränkungen in einem zukünftigen Release zu beheben.</span><span class="sxs-lookup"><span data-stu-id="9eb77-399">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9eb77-400">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9eb77-400">Additional resources</span></span>

* <span data-ttu-id="9eb77-401"><xref:security/blazor/server/threat-mitigation> &ndash;: Anleitung zum Entwickeln von Blazor Server-Apps, die sich mit Ressourcenauslastung auseinandersetzen müssen</span><span class="sxs-lookup"><span data-stu-id="9eb77-401"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
