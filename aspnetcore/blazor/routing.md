---
<span data-ttu-id="80be8-101">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-102">'Blazor'</span></span>
- <span data-ttu-id="80be8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-103">'Identity'</span></span>
- <span data-ttu-id="80be8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-105">'Razor'</span></span>
- <span data-ttu-id="80be8-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="80be8-107">ASP.NET Core Blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="80be8-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="80be8-108">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="80be8-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="80be8-109">Erfahren Sie, wie Sie Anforderungen weiterleiten und die `NavLink`-Komponente verwenden, um Navigationslinks in Blazor-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="80be8-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="80be8-110">Integration von ASP.NET Core-Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="80be8-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="80be8-111"> Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert.</span><span class="sxs-lookup"><span data-stu-id="80be8-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="80be8-112">Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit `MapBlazorHub` in `Startup.Configure` akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="80be8-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="80be8-113">Die typischste Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="80be8-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="80be8-114">Gemäß der Konvention wird die *Hostseite* normalerweise *_Host.cshtml* genannt.</span><span class="sxs-lookup"><span data-stu-id="80be8-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="80be8-115">Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="80be8-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="80be8-116">Die Fallbackroute wird verwendet, wenn andere Routen nicht passen.</span><span class="sxs-lookup"><span data-stu-id="80be8-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="80be8-117">Dadurch kann die App andere Controller und Seiten verwenden, ohne die Blazor Server-App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="80be8-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="80be8-118">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="80be8-118">Route templates</span></span>

<span data-ttu-id="80be8-119">Die `Router`-Komponente ermöglicht das Routing zu jeder Komponente mit einer bestimmten Route.</span><span class="sxs-lookup"><span data-stu-id="80be8-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="80be8-120">Die `Router`-Komponente wird in der Datei *App.razor* angezeigt:</span><span class="sxs-lookup"><span data-stu-id="80be8-120">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="80be8-121">Wenn eine *.razor*-Datei mit einer `@page`-Anweisung kompiliert wird, erhält die generierte Klasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Element, das die Routenvorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="80be8-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="80be8-122">Zur Laufzeit führt die `RouteView`-Komponente Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="80be8-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="80be8-123">Sie empfängt das `RouteData`-Element aus dem `Router`-Element zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="80be8-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="80be8-124">Sie rendert die angegebene Komponente mit ihrem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="80be8-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="80be8-125">Optional können Sie einen `DefaultLayout`-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout festlegen.</span><span class="sxs-lookup"><span data-stu-id="80be8-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="80be8-126">In den Blazor-Standardvorlagen wird die `MainLayout`-Komponente angegeben.</span><span class="sxs-lookup"><span data-stu-id="80be8-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="80be8-127">*MainLayout.razor* befindet sich im *Freigabeordner* mit den Vorlagen für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="80be8-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="80be8-128">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="80be8-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="80be8-129">Mehrere Routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="80be8-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="80be8-130">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="80be8-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="80be8-131">Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer *wwwwroot/index.html*-Datei (Blazor WebAssembly) oder *Pages/_Host.cshtml*-Datei (Blazor Server) mit dem im `href`-Attribut (`<base href="/">`) angegebenen App-Basispfad enthalten.</span><span class="sxs-lookup"><span data-stu-id="80be8-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="80be8-132">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="80be8-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="80be8-133">Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde</span><span class="sxs-lookup"><span data-stu-id="80be8-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="80be8-134">Die `Router`-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="80be8-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="80be8-135">Legen Sie in der Datei *App.razor* den benutzerdefinierten Inhalt im `NotFound`-Vorlagenparameter der `Router`-Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="80be8-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="80be8-136">Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="80be8-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="80be8-137">Informationen zum Anwenden eines Standardlayouts auf `NotFound`-Inhalte finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="80be8-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="80be8-138">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="80be8-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="80be8-139">Verwenden Sie den `AdditionalAssemblies`-Parameter, um zusätzliche Assemblys anzugeben, die die `Router`-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll.</span><span class="sxs-lookup"><span data-stu-id="80be8-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="80be8-140">Angegebene Assemblys werden zusätzlich zur mit `AppAssembly` angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="80be8-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="80be8-141">Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="80be8-142">Im folgenden Beispiel zu `AdditionalAssemblies` wird die Routingunterstützung für `Component1` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="80be8-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="80be8-143">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="80be8-143">Route parameters</span></span>

<span data-ttu-id="80be8-144">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter mit demselben Namen (mit Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="80be8-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="80be8-145">Optionale Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="80be8-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="80be8-146">Im vorherigen Beispiel werden zwei `@page`-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="80be8-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="80be8-147">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="80be8-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="80be8-148">Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="80be8-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="80be8-149">Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="80be8-149">Route constraints</span></span>

<span data-ttu-id="80be8-150">Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="80be8-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="80be8-151">Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur überein, wenn:</span><span class="sxs-lookup"><span data-stu-id="80be8-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="80be8-152">Ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="80be8-153">Das `Id`-Segment eine Ganzzahl (`int`) ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="80be8-154">Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="80be8-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="80be8-155">Informationen zu den Routeneinschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="80be8-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="80be8-156">Constraint</span><span class="sxs-lookup"><span data-stu-id="80be8-156">Constraint</span></span> | <span data-ttu-id="80be8-157">Beispiel</span><span class="sxs-lookup"><span data-stu-id="80be8-157">Example</span></span>           | <span data-ttu-id="80be8-158">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="80be8-158">Example Matches</span></span>                                                                  | <span data-ttu-id="80be8-159">Invariante</span><span class="sxs-lookup"><span data-stu-id="80be8-159">Invariant</span></span><br><span data-ttu-id="80be8-160">Kultur</span><span class="sxs-lookup"><span data-stu-id="80be8-160">culture</span></span><br><span data-ttu-id="80be8-161">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="80be8-161">matching</span></span> |
| ---
<span data-ttu-id="80be8-162">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-163">'Blazor'</span></span>
- <span data-ttu-id="80be8-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-164">'Identity'</span></span>
- <span data-ttu-id="80be8-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-166">'Razor'</span></span>
- <span data-ttu-id="80be8-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-168">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-169">'Blazor'</span></span>
- <span data-ttu-id="80be8-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-170">'Identity'</span></span>
- <span data-ttu-id="80be8-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-172">'Razor'</span></span>
- <span data-ttu-id="80be8-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-174">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-175">'Blazor'</span></span>
- <span data-ttu-id="80be8-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-176">'Identity'</span></span>
- <span data-ttu-id="80be8-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-178">'Razor'</span></span>
- <span data-ttu-id="80be8-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-179">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-180">----- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-181">'Blazor'</span></span>
- <span data-ttu-id="80be8-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-182">'Identity'</span></span>
- <span data-ttu-id="80be8-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-184">'Razor'</span></span>
- <span data-ttu-id="80be8-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-186">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-187">'Blazor'</span></span>
- <span data-ttu-id="80be8-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-188">'Identity'</span></span>
- <span data-ttu-id="80be8-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-190">'Razor'</span></span>
- <span data-ttu-id="80be8-191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-192">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-193">'Blazor'</span></span>
- <span data-ttu-id="80be8-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-194">'Identity'</span></span>
- <span data-ttu-id="80be8-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-196">'Razor'</span></span>
- <span data-ttu-id="80be8-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-198">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-199">'Blazor'</span></span>
- <span data-ttu-id="80be8-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-200">'Identity'</span></span>
- <span data-ttu-id="80be8-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-202">'Razor'</span></span>
- <span data-ttu-id="80be8-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-204">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-205">'Blazor'</span></span>
- <span data-ttu-id="80be8-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-206">'Identity'</span></span>
- <span data-ttu-id="80be8-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-208">'Razor'</span></span>
- <span data-ttu-id="80be8-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-210">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-211">'Blazor'</span></span>
- <span data-ttu-id="80be8-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-212">'Identity'</span></span>
- <span data-ttu-id="80be8-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-214">'Razor'</span></span>
- <span data-ttu-id="80be8-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-215">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-216">--------- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-217">'Blazor'</span></span>
- <span data-ttu-id="80be8-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-218">'Identity'</span></span>
- <span data-ttu-id="80be8-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-220">'Razor'</span></span>
- <span data-ttu-id="80be8-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-222">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-223">'Blazor'</span></span>
- <span data-ttu-id="80be8-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-224">'Identity'</span></span>
- <span data-ttu-id="80be8-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-226">'Razor'</span></span>
- <span data-ttu-id="80be8-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-228">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-229">'Blazor'</span></span>
- <span data-ttu-id="80be8-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-230">'Identity'</span></span>
- <span data-ttu-id="80be8-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-232">'Razor'</span></span>
- <span data-ttu-id="80be8-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-234">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-235">'Blazor'</span></span>
- <span data-ttu-id="80be8-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-236">'Identity'</span></span>
- <span data-ttu-id="80be8-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-238">'Razor'</span></span>
- <span data-ttu-id="80be8-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-240">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-241">'Blazor'</span></span>
- <span data-ttu-id="80be8-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-242">'Identity'</span></span>
- <span data-ttu-id="80be8-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-244">'Razor'</span></span>
- <span data-ttu-id="80be8-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-246">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-247">'Blazor'</span></span>
- <span data-ttu-id="80be8-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-248">'Identity'</span></span>
- <span data-ttu-id="80be8-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-250">'Razor'</span></span>
- <span data-ttu-id="80be8-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-252">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-253">'Blazor'</span></span>
- <span data-ttu-id="80be8-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-254">'Identity'</span></span>
- <span data-ttu-id="80be8-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-256">'Razor'</span></span>
- <span data-ttu-id="80be8-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-258">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-259">'Blazor'</span></span>
- <span data-ttu-id="80be8-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-260">'Identity'</span></span>
- <span data-ttu-id="80be8-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-262">'Razor'</span></span>
- <span data-ttu-id="80be8-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-264">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-265">'Blazor'</span></span>
- <span data-ttu-id="80be8-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-266">'Identity'</span></span>
- <span data-ttu-id="80be8-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-268">'Razor'</span></span>
- <span data-ttu-id="80be8-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-270">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-271">'Blazor'</span></span>
- <span data-ttu-id="80be8-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-272">'Identity'</span></span>
- <span data-ttu-id="80be8-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-274">'Razor'</span></span>
- <span data-ttu-id="80be8-275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-276">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-277">'Blazor'</span></span>
- <span data-ttu-id="80be8-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-278">'Identity'</span></span>
- <span data-ttu-id="80be8-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-280">'Razor'</span></span>
- <span data-ttu-id="80be8-281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-282">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-283">'Blazor'</span></span>
- <span data-ttu-id="80be8-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-284">'Identity'</span></span>
- <span data-ttu-id="80be8-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-286">'Razor'</span></span>
- <span data-ttu-id="80be8-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-288">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-289">'Blazor'</span></span>
- <span data-ttu-id="80be8-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-290">'Identity'</span></span>
- <span data-ttu-id="80be8-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-292">'Razor'</span></span>
- <span data-ttu-id="80be8-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-294">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-295">'Blazor'</span></span>
- <span data-ttu-id="80be8-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-296">'Identity'</span></span>
- <span data-ttu-id="80be8-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-298">'Razor'</span></span>
- <span data-ttu-id="80be8-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-300">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-301">'Blazor'</span></span>
- <span data-ttu-id="80be8-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-302">'Identity'</span></span>
- <span data-ttu-id="80be8-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-304">'Razor'</span></span>
- <span data-ttu-id="80be8-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-306">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-307">'Blazor'</span></span>
- <span data-ttu-id="80be8-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-308">'Identity'</span></span>
- <span data-ttu-id="80be8-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-310">'Razor'</span></span>
- <span data-ttu-id="80be8-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-312">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-313">'Blazor'</span></span>
- <span data-ttu-id="80be8-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-314">'Identity'</span></span>
- <span data-ttu-id="80be8-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-316">'Razor'</span></span>
- <span data-ttu-id="80be8-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-318">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-319">'Blazor'</span></span>
- <span data-ttu-id="80be8-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-320">'Identity'</span></span>
- <span data-ttu-id="80be8-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-322">'Razor'</span></span>
- <span data-ttu-id="80be8-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-324">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-325">'Blazor'</span></span>
- <span data-ttu-id="80be8-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-326">'Identity'</span></span>
- <span data-ttu-id="80be8-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-328">'Razor'</span></span>
- <span data-ttu-id="80be8-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-330">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-331">'Blazor'</span></span>
- <span data-ttu-id="80be8-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-332">'Identity'</span></span>
- <span data-ttu-id="80be8-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-334">'Razor'</span></span>
- <span data-ttu-id="80be8-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-336">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-337">'Blazor'</span></span>
- <span data-ttu-id="80be8-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-338">'Identity'</span></span>
- <span data-ttu-id="80be8-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-340">'Razor'</span></span>
- <span data-ttu-id="80be8-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-342">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-343">'Blazor'</span></span>
- <span data-ttu-id="80be8-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-344">'Identity'</span></span>
- <span data-ttu-id="80be8-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-346">'Razor'</span></span>
- <span data-ttu-id="80be8-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-348">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-349">'Blazor'</span></span>
- <span data-ttu-id="80be8-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-350">'Identity'</span></span>
- <span data-ttu-id="80be8-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-352">'Razor'</span></span>
- <span data-ttu-id="80be8-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-354">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-355">'Blazor'</span></span>
- <span data-ttu-id="80be8-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-356">'Identity'</span></span>
- <span data-ttu-id="80be8-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-358">'Razor'</span></span>
- <span data-ttu-id="80be8-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-360">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-361">'Blazor'</span></span>
- <span data-ttu-id="80be8-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-362">'Identity'</span></span>
- <span data-ttu-id="80be8-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-364">'Razor'</span></span>
- <span data-ttu-id="80be8-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-366">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-367">'Blazor'</span></span>
- <span data-ttu-id="80be8-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-368">'Identity'</span></span>
- <span data-ttu-id="80be8-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-370">'Razor'</span></span>
- <span data-ttu-id="80be8-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-372">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-373">'Blazor'</span></span>
- <span data-ttu-id="80be8-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-374">'Identity'</span></span>
- <span data-ttu-id="80be8-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-376">'Razor'</span></span>
- <span data-ttu-id="80be8-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-378">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-379">'Blazor'</span></span>
- <span data-ttu-id="80be8-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-380">'Identity'</span></span>
- <span data-ttu-id="80be8-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-382">'Razor'</span></span>
- <span data-ttu-id="80be8-383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-384">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-385">'Blazor'</span></span>
- <span data-ttu-id="80be8-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-386">'Identity'</span></span>
- <span data-ttu-id="80be8-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-388">'Razor'</span></span>
- <span data-ttu-id="80be8-389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-390">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-391">'Blazor'</span></span>
- <span data-ttu-id="80be8-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-392">'Identity'</span></span>
- <span data-ttu-id="80be8-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-394">'Razor'</span></span>
- <span data-ttu-id="80be8-395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-396">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-397">'Blazor'</span></span>
- <span data-ttu-id="80be8-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-398">'Identity'</span></span>
- <span data-ttu-id="80be8-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-400">'Razor'</span></span>
- <span data-ttu-id="80be8-401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-402">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-403">'Blazor'</span></span>
- <span data-ttu-id="80be8-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-404">'Identity'</span></span>
- <span data-ttu-id="80be8-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-406">'Razor'</span></span>
- <span data-ttu-id="80be8-407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-408">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-409">'Blazor'</span></span>
- <span data-ttu-id="80be8-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-410">'Identity'</span></span>
- <span data-ttu-id="80be8-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-412">'Razor'</span></span>
- <span data-ttu-id="80be8-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-414">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-415">'Blazor'</span></span>
- <span data-ttu-id="80be8-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-416">'Identity'</span></span>
- <span data-ttu-id="80be8-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-418">'Razor'</span></span>
- <span data-ttu-id="80be8-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-420">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-421">'Blazor'</span></span>
- <span data-ttu-id="80be8-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-422">'Identity'</span></span>
- <span data-ttu-id="80be8-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-424">'Razor'</span></span>
- <span data-ttu-id="80be8-425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-426">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-427">'Blazor'</span></span>
- <span data-ttu-id="80be8-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-428">'Identity'</span></span>
- <span data-ttu-id="80be8-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-430">'Razor'</span></span>
- <span data-ttu-id="80be8-431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-432">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-433">'Blazor'</span></span>
- <span data-ttu-id="80be8-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-434">'Identity'</span></span>
- <span data-ttu-id="80be8-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-436">'Razor'</span></span>
- <span data-ttu-id="80be8-437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-438">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-439">'Blazor'</span></span>
- <span data-ttu-id="80be8-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-440">'Identity'</span></span>
- <span data-ttu-id="80be8-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-442">'Razor'</span></span>
- <span data-ttu-id="80be8-443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-443">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-445">'Blazor'</span></span>
- <span data-ttu-id="80be8-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-446">'Identity'</span></span>
- <span data-ttu-id="80be8-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-448">'Razor'</span></span>
- <span data-ttu-id="80be8-449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-450">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-451">'Blazor'</span></span>
- <span data-ttu-id="80be8-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-452">'Identity'</span></span>
- <span data-ttu-id="80be8-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-454">'Razor'</span></span>
- <span data-ttu-id="80be8-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-456">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-457">'Blazor'</span></span>
- <span data-ttu-id="80be8-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-458">'Identity'</span></span>
- <span data-ttu-id="80be8-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-460">'Razor'</span></span>
- <span data-ttu-id="80be8-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-462">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-463">'Blazor'</span></span>
- <span data-ttu-id="80be8-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-464">'Identity'</span></span>
- <span data-ttu-id="80be8-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-466">'Razor'</span></span>
- <span data-ttu-id="80be8-467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-468">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-469">'Blazor'</span></span>
- <span data-ttu-id="80be8-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-470">'Identity'</span></span>
- <span data-ttu-id="80be8-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-472">'Razor'</span></span>
- <span data-ttu-id="80be8-473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-474">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-475">'Blazor'</span></span>
- <span data-ttu-id="80be8-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-476">'Identity'</span></span>
- <span data-ttu-id="80be8-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-478">'Razor'</span></span>
- <span data-ttu-id="80be8-479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-480">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-481">'Blazor'</span></span>
- <span data-ttu-id="80be8-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-482">'Identity'</span></span>
- <span data-ttu-id="80be8-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-484">'Razor'</span></span>
- <span data-ttu-id="80be8-485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-486">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-487">'Blazor'</span></span>
- <span data-ttu-id="80be8-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-488">'Identity'</span></span>
- <span data-ttu-id="80be8-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-490">'Razor'</span></span>
- <span data-ttu-id="80be8-491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-492">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-493">'Blazor'</span></span>
- <span data-ttu-id="80be8-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-494">'Identity'</span></span>
- <span data-ttu-id="80be8-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-496">'Razor'</span></span>
- <span data-ttu-id="80be8-497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-498">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-499">'Blazor'</span></span>
- <span data-ttu-id="80be8-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-500">'Identity'</span></span>
- <span data-ttu-id="80be8-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-502">'Razor'</span></span>
- <span data-ttu-id="80be8-503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-504">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-505">'Blazor'</span></span>
- <span data-ttu-id="80be8-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-506">'Identity'</span></span>
- <span data-ttu-id="80be8-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-508">'Razor'</span></span>
- <span data-ttu-id="80be8-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-510">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-511">'Blazor'</span></span>
- <span data-ttu-id="80be8-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-512">'Identity'</span></span>
- <span data-ttu-id="80be8-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-514">'Razor'</span></span>
- <span data-ttu-id="80be8-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-516">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-517">'Blazor'</span></span>
- <span data-ttu-id="80be8-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-518">'Identity'</span></span>
- <span data-ttu-id="80be8-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-520">'Razor'</span></span>
- <span data-ttu-id="80be8-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-521">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nein                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ja                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ja                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ja                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ja                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nein                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ja                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ja                              |</span><span class="sxs-lookup"><span data-stu-id="80be8-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="80be8-523">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="80be8-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="80be8-524">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="80be8-525">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="80be8-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="80be8-526">In Blazor Server-Apps ist die Standardroute in *_Host.cshtml* `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="80be8-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="80be8-527">Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="80be8-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="80be8-528">Eine Blazor-App gibt eine Antwort *404 – Nicht gefunden* für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="80be8-529">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host.cshtml* mit der folgenden Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="80be8-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="80be8-530">Die `"/{**path}"`-Vorlage enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="80be8-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="80be8-531">*catch-all*-Syntax mit doppelten Sternchen (`**`) zur Erfassung des Pfades über mehrere Ordnergrenzen hinweg ohne Codierung von Schrägstrichen (`/`).</span><span class="sxs-lookup"><span data-stu-id="80be8-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="80be8-532">Name des `path`-Routenparameters.</span><span class="sxs-lookup"><span data-stu-id="80be8-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="80be8-533">Die *catch-all*-Parametersyntax (`*`/`**`) wird **nicht** in Razor-Komponenten ( *.razor*) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="80be8-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="80be8-534">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="80be8-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="80be8-535">NavLink-Komponente</span><span class="sxs-lookup"><span data-stu-id="80be8-535">NavLink component</span></span>

<span data-ttu-id="80be8-536">Verwenden Sie bei der Erstellung von Navigationslinks eine `NavLink`-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="80be8-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="80be8-537">Eine `NavLink`-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="80be8-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="80be8-538">Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="80be8-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="80be8-539">Die folgende `NavMenu`-Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie `NavLink`-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="80be8-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="80be8-540">Es gibt zwei `NavLinkMatch`-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="80be8-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="80be8-541">`NavLinkMatch.All` &ndash; `NavLink` ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="80be8-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="80be8-542">`NavLinkMatch.Prefix` (*Standard*) &ndash; `NavLink` ist aktiv, wenn ein Präfix der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="80be8-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="80be8-543">Im vorherigen Beispiel stimmt die Startseite `NavLink` `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="80be8-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="80be8-544">Die zweite `NavLink`-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `MyComponent`-Präfix aufruft (z. B. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="80be8-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="80be8-545">Zusätzliche `NavLink`-Komponentenattribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="80be8-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="80be8-546">Im folgenden Beispiel schließt die `NavLink`-Komponente das `target`-Attribut ein:</span><span class="sxs-lookup"><span data-stu-id="80be8-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="80be8-547">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="80be8-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="80be8-548">Hilfsprogramme für URI und Navigationszustand</span><span class="sxs-lookup"><span data-stu-id="80be8-548">URI and navigation state helpers</span></span>

<span data-ttu-id="80be8-549">Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um mit URIs und Navigationselementen in C#-Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="80be8-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="80be8-550">`NavigationManager` stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="80be8-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="80be8-551">Member</span><span class="sxs-lookup"><span data-stu-id="80be8-551">Member</span></span> | <span data-ttu-id="80be8-552">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="80be8-552">Description</span></span> |
| ---
<span data-ttu-id="80be8-553">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-554">'Blazor'</span></span>
- <span data-ttu-id="80be8-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-555">'Identity'</span></span>
- <span data-ttu-id="80be8-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-557">'Razor'</span></span>
- <span data-ttu-id="80be8-558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-558">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-559">--- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-560">'Blazor'</span></span>
- <span data-ttu-id="80be8-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-561">'Identity'</span></span>
- <span data-ttu-id="80be8-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-563">'Razor'</span></span>
- <span data-ttu-id="80be8-564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-565">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-566">'Blazor'</span></span>
- <span data-ttu-id="80be8-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-567">'Identity'</span></span>
- <span data-ttu-id="80be8-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-569">'Razor'</span></span>
- <span data-ttu-id="80be8-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80be8-571">title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80be8-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80be8-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80be8-572">'Blazor'</span></span>
- <span data-ttu-id="80be8-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80be8-573">'Identity'</span></span>
- <span data-ttu-id="80be8-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80be8-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="80be8-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80be8-575">'Razor'</span></span>
- <span data-ttu-id="80be8-576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="80be8-576">'SignalR' uid:</span></span> 

<span data-ttu-id="80be8-577">------ | | Uri | Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="80be8-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="80be8-578">| | BaseUri | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="80be8-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="80be8-579">In der Regel entspricht `BaseUri` dem `href`-Attribut im `<base>`-Element des Dokuments in *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="80be8-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="80be8-580">| | NavigateTo | Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="80be8-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="80be8-581">Bei `forceLoad` lautet der Wert `true`:</span><span class="sxs-lookup"><span data-stu-id="80be8-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="80be8-582">Clientseitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="80be8-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="80be8-583">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</span><span class="sxs-lookup"><span data-stu-id="80be8-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="80be8-584">| | LocationChanged | Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat.</span><span class="sxs-lookup"><span data-stu-id="80be8-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="80be8-585">| | ToAbsoluteUri | Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="80be8-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="80be8-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Wenn ein Basis-URI (z. B. ein URI, der zuvor von `GetBaseUri` zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert.</span><span class="sxs-lookup"><span data-stu-id="80be8-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="80be8-587">Die folgende Komponente navigiert zu der `Counter`-Komponente der App, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="80be8-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="80be8-588">Die folgende Komponente verarbeitet ein Speicherortwechselereignis.</span><span class="sxs-lookup"><span data-stu-id="80be8-588">The following component handles a location changed event.</span></span> <span data-ttu-id="80be8-589">Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="80be8-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="80be8-590">Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="80be8-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="80be8-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> stellt die folgenden Informationen zum Ereignis bereit:</span><span class="sxs-lookup"><span data-stu-id="80be8-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="80be8-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> entspricht der URL des neuen Speicherorts.</span><span class="sxs-lookup"><span data-stu-id="80be8-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="80be8-593">Wenn <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> den Wert `true` aufweist, wird die Navigation des Browsers von Blazor abgefangen.</span><span class="sxs-lookup"><span data-stu-id="80be8-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="80be8-594">Wenn der Wert `false` vorliegt, wurde die Navigation von [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="80be8-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="80be8-595">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="80be8-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
