---
<span data-ttu-id="2ac65-101">title: 'Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten' author: description: 'Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.'</span><span class="sxs-lookup"><span data-stu-id="2ac65-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="2ac65-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2ac65-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2ac65-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2ac65-103">'Blazor'</span></span>
- <span data-ttu-id="2ac65-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2ac65-104">'Identity'</span></span>
- <span data-ttu-id="2ac65-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2ac65-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2ac65-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2ac65-106">'Razor'</span></span>
- <span data-ttu-id="2ac65-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2ac65-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="2ac65-108">Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="2ac65-109">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="2ac65-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="2ac65-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2ac65-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="2ac65-111">-Apps werden mithilfe von *Komponenten* erstellt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-111"> apps are built using *components*.</span></span> <span data-ttu-id="2ac65-112">Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="2ac65-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="2ac65-113">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="2ac65-114">Komponenten sind flexibel und kompakt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="2ac65-115">Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="2ac65-116">Komponentenklassen</span><span class="sxs-lookup"><span data-stu-id="2ac65-116">Component classes</span></span>

<span data-ttu-id="2ac65-117">Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien ( *.razor*) implementiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="2ac65-118">Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="2ac65-119">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="2ac65-120">Beispielsweise ist *MyCoolComponent.razor* zulässig, *mycoolcomponent.razor* aber nicht.</span><span class="sxs-lookup"><span data-stu-id="2ac65-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="2ac65-121">Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="2ac65-122">Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen *Razor* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="2ac65-123">Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="2ac65-124">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="2ac65-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="2ac65-125">Member der Komponentenklasse werden in einem [`@code`][1]-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="2ac65-126">Im [`@code`][1]-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="2ac65-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="2ac65-127">Mehrere [`@code`][1]-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="2ac65-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="2ac65-128">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="2ac65-129">Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="2ac65-130">Das Beispiel wertet Folgendes aus und führt ein Rendering durch:</span><span class="sxs-lookup"><span data-stu-id="2ac65-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="2ac65-131">`headingFontStyle` in den CSS-Eigenschaftswert für `font-style`</span><span class="sxs-lookup"><span data-stu-id="2ac65-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="2ac65-132">`headingText` in den Inhalt des `<h1>`-Elements</span><span class="sxs-lookup"><span data-stu-id="2ac65-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="2ac65-133">Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="2ac65-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="2ac65-134"> vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.</span><span class="sxs-lookup"><span data-stu-id="2ac65-134"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="2ac65-135">Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="2ac65-136">Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2ac65-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="2ac65-137">Nicht für Seiten verwendete Komponenten werden häufig im Ordner *Shared* oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="2ac65-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="2ac65-138">In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="2ac65-139">Wenn der Stammnamespace der App `BlazorApp` ist und sich die `Counter` Komponente im Ordner *Pages* befindet, ist</span><span class="sxs-lookup"><span data-stu-id="2ac65-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="2ac65-140">der Namespace der `Counter`-Komponente `BlazorApp.Pages`, und</span><span class="sxs-lookup"><span data-stu-id="2ac65-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="2ac65-141">der vollqualifizierten Typname der Komponente ist `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="2ac65-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="2ac65-142">Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine `using`-Anweisung der übergeordneten Komponente oder der *_Imports.razor*-Datei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="2ac65-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="2ac65-143">Im folgenden Beispiel werden Komponenten im Ordner *Components* (Komponenten) zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="2ac65-144">Alternativ kann auf eine Komponente direkt verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="2ac65-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="2ac65-145">Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components).</span><span class="sxs-lookup"><span data-stu-id="2ac65-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="2ac65-146">-Syntax</span><span class="sxs-lookup"><span data-stu-id="2ac65-146"> syntax</span></span>

Razor<span data-ttu-id="2ac65-147">-Komponenten in Blazor-Apps verwenden Razor-Syntax ausführlich.</span><span class="sxs-lookup"><span data-stu-id="2ac65-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="2ac65-148">Wenn Sie mit der Razor-Markupsprache nicht vertraut sind, sollten Sie <xref:mvc/views/razor> lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="2ac65-149">Beachten Sie beim Zugriff auf den Inhalt der Razor-Syntax besonders die folgenden Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="2ac65-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="2ac65-150">[Direktiven](xref:mvc/views/razor#directives): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie das Komponentenmarkup analysiert wird oder funktioniert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-150">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="2ac65-151">[Direktivenattribute](xref:mvc/views/razor#directive-attributes): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie Komponentenelemente analysiert werden oder funktionieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-151">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="2ac65-152">Statische Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2ac65-152">Static assets</span></span>

Blazor<span data-ttu-id="2ac65-153"> befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen unter dem Webstammordner ([wwwroot](xref:fundamentals/index#web-root)) des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="2ac65-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="2ac65-154">Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="2ac65-155">Im folgenden Beispiel wird *logo.png* im Ordner *{Projektstamm}/wwwroot/images* gespeichert:</span><span class="sxs-lookup"><span data-stu-id="2ac65-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="2ac65-156">-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).</span><span class="sxs-lookup"><span data-stu-id="2ac65-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="2ac65-157">Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="2ac65-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="2ac65-158">Keine Unterstützung von Taghilfsprogrammen in Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="2ac65-159">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten ( *.razor*-Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="2ac65-160">Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="2ac65-161">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-161">Use components</span></span>

<span data-ttu-id="2ac65-162">Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="2ac65-163">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="2ac65-164">Das folgende Markup in *Index.razor* rendert eine `HeadingComponent`-Instanz:</span><span class="sxs-lookup"><span data-stu-id="2ac65-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="2ac65-165">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="2ac65-166">Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="2ac65-167">Durch das Hinzufügen einer [`@using`][2]-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="2ac65-168">Routing</span><span class="sxs-lookup"><span data-stu-id="2ac65-168">Routing</span></span>

<span data-ttu-id="2ac65-169">Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App.</span><span class="sxs-lookup"><span data-stu-id="2ac65-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="2ac65-170">Wenn eine Razor-Datei mit einer [`@page`][9]-Anweisung kompiliert wird, wird der generierten Klasse ein <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben.</span><span class="sxs-lookup"><span data-stu-id="2ac65-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="2ac65-171">Zur Laufzeit sucht der Router nach Komponentenklassen mit <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-171">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="2ac65-172">Weitere Informationen finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="2ac65-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="2ac65-173">Parameter</span><span class="sxs-lookup"><span data-stu-id="2ac65-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="2ac65-174">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="2ac65-174">Route parameters</span></span>

<span data-ttu-id="2ac65-175">Komponenten können Routenparameter von der Routenvorlage empfangen, die in der [`@page`][9]-Anweisung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="2ac65-176">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="2ac65-177">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="2ac65-178">Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei [`@page`][9]-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="2ac65-179">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="2ac65-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2ac65-180">Die zweite [`@page`][9]-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="2ac65-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="2ac65-181">Die *Catch-all*-Parametersyntax (`*`/`**`), die den Pfad ordnerübergreifend erfasst, wird in Razor-Komponenten ( *.razor*) **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="2ac65-182">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="2ac65-182">Component parameters</span></span>

<span data-ttu-id="2ac65-183">Komponenten können *Komponentenparameter* enthalten, die mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-183">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="2ac65-184">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2ac65-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="2ac65-185">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="2ac65-186">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.</span><span class="sxs-lookup"><span data-stu-id="2ac65-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="2ac65-187">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="2ac65-188">Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld.</span><span class="sxs-lookup"><span data-stu-id="2ac65-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="2ac65-189">Weitere Informationen finden Sie im Abschnitt [Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben](#dont-create-components-that-write-to-their-own-parameter-properties).</span><span class="sxs-lookup"><span data-stu-id="2ac65-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="2ac65-190">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="2ac65-190">Child content</span></span>

<span data-ttu-id="2ac65-191">Komponenten können den Inhalt anderer Komponenten festlegen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-191">Components can set the content of another component.</span></span> <span data-ttu-id="2ac65-192">Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="2ac65-193">Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="2ac65-194">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="2ac65-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="2ac65-195">Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="2ac65-196">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="2ac65-197">Die Eigenschaft, die den Inhalt von <xref:Microsoft.AspNetCore.Components.RenderFragment> empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-197">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="2ac65-198">Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="2ac65-199">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="2ac65-200">Attributsplatting und arbiträre Parameter</span><span class="sxs-lookup"><span data-stu-id="2ac65-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="2ac65-201">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern.</span><span class="sxs-lookup"><span data-stu-id="2ac65-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="2ac65-202">Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`][3]-Anweisung Razor gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="2ac65-203">Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="2ac65-204">Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="2ac65-205">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="2ac65-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="2ac65-206">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichenfolgenschlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="2ac65-207">Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch möglich.</span><span class="sxs-lookup"><span data-stu-id="2ac65-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="2ac65-208">Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:</span><span class="sxs-lookup"><span data-stu-id="2ac65-208">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="2ac65-209">Definieren Sie einen Komponentenparameter mithilfe des [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attributs, bei dem die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="2ac65-209">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="2ac65-210">Die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft für [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ermöglicht dem Parameter den Abgleich aller Attribute, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-210">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="2ac65-211">Eine Komponente kann nur einen einzelnen Parameter mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> definieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-211">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="2ac65-212">Der mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="2ac65-212">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="2ac65-213">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="2ac65-214">Die Position von [`@attributes`][3] relativ zur Position der Elementattribute ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="2ac65-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="2ac65-215">Wenn [`@attributes`][3]-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="2ac65-216">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="2ac65-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="2ac65-217">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="2ac65-218">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="2ac65-219">Das `extra`-Attribut der `Child`-Komponente ist rechts von [`@attributes`][3] festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="2ac65-220">Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="2ac65-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="2ac65-221">Im folgenden Beispiel wird die Reihenfolge von `extra` und [`@attributes`][3] in `<div>` der `Child`-Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="2ac65-222">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="2ac65-223">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="2ac65-224">Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="2ac65-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="2ac65-225">Erfassen von Verweisen auf Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-225">Capture references to components</span></span>

<span data-ttu-id="2ac65-226">Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="2ac65-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="2ac65-227">So erfassen Sie einen Komponentenverweis:</span><span class="sxs-lookup"><span data-stu-id="2ac65-227">To capture a component reference:</span></span>

* <span data-ttu-id="2ac65-228">Fügen Sie der untergeordneten Komponente ein [`@ref`][4]-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="2ac65-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="2ac65-229">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="2ac65-229">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="2ac65-230">Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `MyLoginDialog`-Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="2ac65-231">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2ac65-232">Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="2ac65-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="2ac65-233">Bis zu diesem Punkt sind keine Verweise nötig.</span><span class="sxs-lookup"><span data-stu-id="2ac65-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="2ac65-234">Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die Methode [OnAfterRenderAsync oder OnAfterRender](xref:blazor/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="2ac65-235">Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="2ac65-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="2ac65-236">Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature.</span><span class="sxs-lookup"><span data-stu-id="2ac65-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="2ac65-237">Komponentenverweise werden nicht an JavaScript-Code übermittelt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="2ac65-238">Komponentenverweise werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="2ac65-239">Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern.</span><span class="sxs-lookup"><span data-stu-id="2ac65-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="2ac65-240">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="2ac65-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="2ac65-241">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="2ac65-242">Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status</span><span class="sxs-lookup"><span data-stu-id="2ac65-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="2ac65-243"> verwendet einen Synchronisierungskontext (<xref:System.Threading.SynchronizationContext>), um einen einzelnen logischen Ausführungsthread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-243"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="2ac65-244">Die [Lebenszyklusmethoden](xref:blazor/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="2ac65-245">Der Synchronisierungskontext des Blazor-Servers versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-245">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="2ac65-246">Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht.</span><span class="sxs-lookup"><span data-stu-id="2ac65-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="2ac65-247">Zwei Vorgänge werden nicht gleichzeitig ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="2ac65-248">Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="2ac65-249">Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="2ac65-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="2ac65-250">Registrieren Sie `NotifierService` als Singleton:</span><span class="sxs-lookup"><span data-stu-id="2ac65-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="2ac65-251">Registrieren Sie den Dienst in Blazor WebAssembly in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2ac65-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="2ac65-252">Registrieren Sie den Dienst in Blazor Server in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2ac65-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="2ac65-253">Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="2ac65-253">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="2ac65-254">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf.</span><span class="sxs-lookup"><span data-stu-id="2ac65-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="2ac65-255">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="2ac65-256">Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="2ac65-257">Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="2ac65-258">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.</span><span class="sxs-lookup"><span data-stu-id="2ac65-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="2ac65-259">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2ac65-259">Consider the following example:</span></span>

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

<span data-ttu-id="2ac65-260">Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="2ac65-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="2ac65-261">Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann.</span><span class="sxs-lookup"><span data-stu-id="2ac65-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="2ac65-262">Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="2ac65-263">In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="2ac65-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="2ac65-264">Der Zuordnungsprozess kann mit dem [`@key`][5]-Anweisungsattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="2ac65-265">[`@key`][5] bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert:</span><span class="sxs-lookup"><span data-stu-id="2ac65-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="2ac65-266">Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="2ac65-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="2ac65-267">Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="2ac65-268">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="2ac65-269">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="2ac65-270">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="2ac65-271">Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="2ac65-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="2ac65-272">In einigen Szenarios minimiert die Verwendung von [`@key`][5] die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="2ac65-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2ac65-273">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="2ac65-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="2ac65-274">Schlüssel werden nicht dokumentübergreifend global verglichen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="2ac65-275">Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="2ac65-275">When to use \@key</span></span>

<span data-ttu-id="2ac65-276">In der Regel ist es sinnvoll, [`@key`][5] zu verwenden, wenn eine Liste gerendert wird (z. B. in einem [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Block) und ein geeigneter Wert vorhanden ist, um den [`@key`][5] zu definieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="2ac65-277">Sie können [`@key`][5] auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="2ac65-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="2ac65-278">Wenn `@currentPerson` geändert wird, zwingt die [`@key`][5]-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="2ac65-279">Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="2ac65-280">Ungeeignete Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="2ac65-280">When not to use \@key</span></span>

<span data-ttu-id="2ac65-281">Bei einem Vergleich mit [`@key`][5] wird die Leistung beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="2ac65-282">Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten [`@key`][5] jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.</span><span class="sxs-lookup"><span data-stu-id="2ac65-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="2ac65-283">Auch wenn [`@key`][5] nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="2ac65-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="2ac65-284">Der einzige Vorteil bei der Verwendung von [`@key`][5] besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet werden, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="2ac65-285">Zu verwendende Werte für \@key</span><span class="sxs-lookup"><span data-stu-id="2ac65-285">What values to use for \@key</span></span>

<span data-ttu-id="2ac65-286">Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für [`@key`][5] bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="2ac65-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="2ac65-287">Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel):</span><span class="sxs-lookup"><span data-stu-id="2ac65-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="2ac65-288">Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="2ac65-289">Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):</span><span class="sxs-lookup"><span data-stu-id="2ac65-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="2ac65-290">Stellen Sie sicher, dass die für [`@key`][5] verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="2ac65-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="2ac65-291">Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="2ac65-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="2ac65-292">Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.</span><span class="sxs-lookup"><span data-stu-id="2ac65-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="2ac65-293">Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben</span><span class="sxs-lookup"><span data-stu-id="2ac65-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="2ac65-294">Parameter werden unter den folgenden Bedingungen überschrieben:</span><span class="sxs-lookup"><span data-stu-id="2ac65-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="2ac65-295">Der Inhalt einer untergeordneten Komponente wird mit einem <xref:Microsoft.AspNetCore.Components.RenderFragment> gerendert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-295">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="2ac65-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird in der übergeordneten Komponente aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="2ac65-297">Parameter werden zurückgesetzt, weil die übergeordnete Komponente erneut gerendert wird, wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufgerufen wird und der untergeordneten Komponente neue Parameterwerte bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="2ac65-298">Angenommen, die folgende `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="2ac65-299">rendert untergeordneten Inhalt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-299">Renders child content.</span></span>
* <span data-ttu-id="2ac65-300">schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter um.</span><span class="sxs-lookup"><span data-stu-id="2ac65-300">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="2ac65-301">Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen kann:</span><span class="sxs-lookup"><span data-stu-id="2ac65-301">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="2ac65-302">Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="2ac65-303">Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei.</span><span class="sxs-lookup"><span data-stu-id="2ac65-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="2ac65-304">Wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`).</span><span class="sxs-lookup"><span data-stu-id="2ac65-304">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="2ac65-305">Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="2ac65-306">Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="2ac65-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="2ac65-307">Die folgende `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-307">The following `Expander` component:</span></span>

* <span data-ttu-id="2ac65-308">akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="2ac65-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="2ac65-309">weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/lifecycle#component-initialization-methods) zu.</span><span class="sxs-lookup"><span data-stu-id="2ac65-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="2ac65-310">verwendet das private Feld, um seinen internen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="2ac65-310">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="2ac65-311">Unterstützung für partielle Klassen</span><span class="sxs-lookup"><span data-stu-id="2ac65-311">Partial class support</span></span>

Razor<span data-ttu-id="2ac65-312">-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="2ac65-313">-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="2ac65-314">C#-Code wird in einem [`@code`][1]-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="2ac65-315">-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="2ac65-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="2ac65-316">C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="2ac65-317">Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem [`@code`][1]-Block in einer App, die aus einer Blazor-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="2ac65-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="2ac65-318">HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="2ac65-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="2ac65-319">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-319">*Counter.razor*:</span></span>

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

<span data-ttu-id="2ac65-320">Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="2ac65-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="2ac65-321">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="2ac65-322">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-322">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="2ac65-323">Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="2ac65-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="2ac65-324">Folgende typischen Namespaces werden von Razor-Komponenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="2ac65-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="2ac65-325">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="2ac65-325">Specify a base class</span></span>

<span data-ttu-id="2ac65-326">Die [`@inherits`][6]-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2ac65-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="2ac65-327">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="2ac65-328">Die Basisklasse sollte von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-328">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="2ac65-329">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="2ac65-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-330">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="2ac65-331">Angeben eines Attributs</span><span class="sxs-lookup"><span data-stu-id="2ac65-331">Specify an attribute</span></span>

<span data-ttu-id="2ac65-332">Attribute können in Razor-Komponenten mit der [`@attribute`][7]-Anweisung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="2ac65-333">Im folgenden Beispiel wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Komponentenklasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="2ac65-333">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="2ac65-334">Importieren von Komponenten</span><span class="sxs-lookup"><span data-stu-id="2ac65-334">Import components</span></span>

<span data-ttu-id="2ac65-335">Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):</span><span class="sxs-lookup"><span data-stu-id="2ac65-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="2ac65-336">[`@namespace`][8]-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="2ac65-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="2ac65-337">Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)</span><span class="sxs-lookup"><span data-stu-id="2ac65-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="2ac65-338">Der Projektname, der aus dem Namen der Projektdatei ( *.csproj*) und dem Pfad vom Projektstamm zur Komponente resultiert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="2ac65-339">Das Framework löst z. B. *{Projektstamm}/Pages/Index.razor* (*BlazorSample.csproj*) in den Namespace `BlazorSample.Pages` auf.</span><span class="sxs-lookup"><span data-stu-id="2ac65-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="2ac65-340">Komponenten folgen den Namensbindungsregeln für C#.</span><span class="sxs-lookup"><span data-stu-id="2ac65-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="2ac65-341">Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="2ac65-342">Komponenten im selben Ordner (*Pages*)</span><span class="sxs-lookup"><span data-stu-id="2ac65-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="2ac65-343">Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben</span><span class="sxs-lookup"><span data-stu-id="2ac65-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="2ac65-344">Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der Razor-Anweisung [`@using`][2] in den Geltungsbereich einbezogen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="2ac65-345">Wenn eine andere Komponente (`NavMenu.razor`) im Ordner *BlazorSample/Shared/* vorhanden ist, kann die Komponente mithilfe der folgenden [`@using`][2]-Anweisung in `Index.razor` verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2ac65-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="2ac65-346">Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`][2] nicht erforderlich:</span><span class="sxs-lookup"><span data-stu-id="2ac65-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="2ac65-347">Die `global::`-Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="2ac65-348">Das Importieren von Komponenten mit [using](/dotnet/csharp/language-reference/keywords/using-statement)-Aliasanweisungen (z B. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-348">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="2ac65-349">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="2ac65-350">Das Hinzufügen von `@using BlazorSample` und das Verweisen auf die `NavMenu`-Komponente (`NavMenu.razor`) mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-350">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="2ac65-351">Attribute für bedingte HTML-Elemente</span><span class="sxs-lookup"><span data-stu-id="2ac65-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="2ac65-352">HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="2ac65-353">Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="2ac65-354">Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="2ac65-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="2ac65-355">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="2ac65-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="2ac65-356">Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="2ac65-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="2ac65-357">Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="2ac65-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="2ac65-358">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="2ac65-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="2ac65-359">Einige HTML-Attribute wie [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="2ac65-360">Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="2ac65-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="2ac65-361">Unformatierter HTML-Code</span><span class="sxs-lookup"><span data-stu-id="2ac65-361">Raw HTML</span></span>

<span data-ttu-id="2ac65-362">Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="2ac65-363">Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="2ac65-364">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="2ac65-365">Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="2ac65-366">Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="2ac65-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="2ac65-367">Kaskadierende Werte und Parameter</span><span class="sxs-lookup"><span data-stu-id="2ac65-367">Cascading values and parameters</span></span>

<span data-ttu-id="2ac65-368">In einigen Szenarios ist es unpraktisch, Daten mithilfe von [Komponentenparametern](#component-parameters) von einer Vorgängerkomponente an eine Nachfolgerkomponente zu übertragen. Das gilt insbesondere, wenn es mehrere Komponentenebenen gibt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="2ac65-369">Kaskadierende Werte und Parameter lösen dieses Problem, indem es Vorgängerkomponenten einfach ermöglicht wird, für alle Nachfolgerkomponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="2ac65-370">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2ac65-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="2ac65-371">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="2ac65-371">Theme example</span></span>

<span data-ttu-id="2ac65-372">Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo`-Klasse die Designinformationen an, die in der Komponentenhierarchie nach unten weitergegeben werden, sodass alle Schaltflächen innerhalb eines bestimmten Teils der App denselben Stil aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="2ac65-373">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ac65-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="2ac65-374">Eine Vorgängerkomponente kann einen kaskadierenden Wert mithilfe der CascadingValue-Komponente bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="2ac65-375">Die <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente umschließt eine Teilstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="2ac65-375">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="2ac65-376">Beispielsweise gibt die Beispiel-App Designinformationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="2ac65-377">`ButtonClass` wird in der Layoutkomponente der Wert `btn-success` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="2ac65-378">Jede Nachfolgerkomponente kann diese Eigenschaft über das kaskadierende `ThemeInfo`-Objekt nutzen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="2ac65-379">`CascadingValuesParametersLayout`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-379">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="2ac65-380">Komponenten deklarieren kaskadierende Parameter mithilfe des [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attributs, um kaskadierende Werte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-380">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="2ac65-381">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="2ac65-382">In der Beispiel-App bindet die `CascadingValuesParametersTheme`-Komponente den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="2ac65-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="2ac65-383">Der Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="2ac65-384">`CascadingValuesParametersTheme`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-384">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="2ac65-385">Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente und dem entsprechenden [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attribut eine eindeutige <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>-Zeichenfolge bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-385">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="2ac65-386">Im folgenden Beispiel kaskadieren zwei <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen:</span><span class="sxs-lookup"><span data-stu-id="2ac65-386">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="2ac65-387">In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre Werte nach Namen von den entsprechenden kaskadierenden Werten in der Vorgängerkomponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="2ac65-388">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="2ac65-388">TabSet example</span></span>

<span data-ttu-id="2ac65-389">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponentenhierarchie.</span><span class="sxs-lookup"><span data-stu-id="2ac65-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="2ac65-390">Sehen Sie sich z. B. das folgende *TabSet*-Beispiel in der Beispiel-App an.</span><span class="sxs-lookup"><span data-stu-id="2ac65-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="2ac65-391">Die Beispiel-App enthält eine `ITab`-Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="2ac65-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="2ac65-392">Die `CascadingValuesParametersTabSet`-Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab`-Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="2ac65-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="2ac65-393">Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="2ac65-394">Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="2ac65-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="2ac65-395">Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben*, der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2ac65-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="2ac65-396">`TabSet`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="2ac65-397">Die `Tab`-Nachfolgerkomponenten erfassen die enthaltende `TabSet`-Komponente als kaskadierenden Parameter. Das bedeutet, dass die `Tab`-Komponenten sich zu `TabSet` hinzufügen und koordinieren, welche Registerkarte aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="2ac65-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="2ac65-398">`Tab`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2ac65-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="2ac65-399">-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="2ac65-399"> templates</span></span>

<span data-ttu-id="2ac65-400">Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-400">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="2ac65-401">Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:</span><span class="sxs-lookup"><span data-stu-id="2ac65-401">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="2ac65-402">Im folgenden Beispiel wird veranschaulicht, wie Sie <xref:Microsoft.AspNetCore.Components.RenderFragment>- und <xref:Microsoft.AspNetCore.Components.RenderFragment%601>-Werte angeben und Vorlagen direkt in einer-Komponente rendern können.</span><span class="sxs-lookup"><span data-stu-id="2ac65-402">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="2ac65-403">Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/templated-components) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="2ac65-404">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="2ac65-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="2ac65-405">SVG-Bilder</span><span class="sxs-lookup"><span data-stu-id="2ac65-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="2ac65-406">Da Blazor HTML rendert, werden browsergestützte Bilder wie SVG-Bilder (Scalable Vector Graphics, skalierbare Vektorgrafiken, *.svg*) über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="2ac65-407">Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei ( *.css*) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="2ac65-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="2ac65-408">SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2ac65-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="2ac65-409">Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei ( *.razor*) einfügen, wird das grundlegende Bildrendering unterstützt, aber viele fortgeschrittene Szenarios noch nicht.</span><span class="sxs-lookup"><span data-stu-id="2ac65-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="2ac65-410">Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und [`@bind`][10] kann nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2ac65-410">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="2ac65-411">Weitere Informationen finden Sie unter [Improve SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271) (Verbessern der SVG-Unterstützung in Blazor).</span><span class="sxs-lookup"><span data-stu-id="2ac65-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2ac65-412">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2ac65-412">Additional resources</span></span>

* <span data-ttu-id="2ac65-413"><xref:security/blazor/server/threat-mitigation>: Enthält Anleitung zum Entwickeln von Blazor-Server-Apps, die sich mit Ressourcenauslastung auseinandersetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="2ac65-413"><xref:security/blazor/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
