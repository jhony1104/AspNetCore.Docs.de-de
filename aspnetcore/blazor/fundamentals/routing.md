---
title: ASP.NET Core Blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in Apps und über die NavLink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: fde30109395065014433bebde52a9eb22458c451
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242744"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="6b928-103">ASP.NET Core Blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="6b928-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="6b928-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b928-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b928-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verwenden, um Navigationslinks in Blazor-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6b928-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="6b928-106">Integration von ASP.NET Core-Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="6b928-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="6b928-107"> Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert.</span><span class="sxs-lookup"><span data-stu-id="6b928-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6b928-108">Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="6b928-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6b928-109">Die typischste Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="6b928-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="6b928-110">Gemäß der Konvention wird die *Host*-Seite normalerweise `_Host.cshtml` genannt.</span><span class="sxs-lookup"><span data-stu-id="6b928-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="6b928-111">Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="6b928-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="6b928-112">Die Fallbackroute wird verwendet, wenn andere Routen nicht passen.</span><span class="sxs-lookup"><span data-stu-id="6b928-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="6b928-113">Dadurch kann die App andere Controller und Seiten verwenden, ohne die Blazor Server-App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="6b928-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="6b928-114">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="6b928-114">Route templates</span></span>

<span data-ttu-id="6b928-115">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht das Routing zu jeder Komponente mit einer bestimmten Route.</span><span class="sxs-lookup"><span data-stu-id="6b928-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="6b928-116">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente wird in der `App.razor`-Datei angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6b928-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

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

<span data-ttu-id="6b928-117">Wenn eine `.razor`-Datei mit einer `@page`-Anweisung kompiliert wird, erhält die generierte Klasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Element, das die Routenvorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="6b928-117">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="6b928-118">Zur Laufzeit führt die <xref:Microsoft.AspNetCore.Components.RouteView>-Komponente Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="6b928-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="6b928-119">Sie empfängt das <xref:Microsoft.AspNetCore.Components.RouteData>-Element aus dem <xref:Microsoft.AspNetCore.Components.Routing.Router>-Element zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="6b928-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="6b928-120">Sie rendert die angegebene Komponente mit ihrem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="6b928-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="6b928-121">Optional können Sie einen <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout>-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout festlegen.</span><span class="sxs-lookup"><span data-stu-id="6b928-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="6b928-122">In den Blazor-Standardvorlagen wird die `MainLayout`-Komponente angegeben.</span><span class="sxs-lookup"><span data-stu-id="6b928-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="6b928-123">`MainLayout.razor` befindet sich im Ordner `Shared` des Vorlagenprojekts.</span><span class="sxs-lookup"><span data-stu-id="6b928-123">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="6b928-124">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6b928-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="6b928-125">Mehrere Routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6b928-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6b928-126">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="6b928-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="6b928-127">Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) mit dem im `href`-Attribut (`<base href="/">`) angegebenen App-Basispfad enthalten.</span><span class="sxs-lookup"><span data-stu-id="6b928-127">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="6b928-128">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6b928-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="6b928-129">Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde</span><span class="sxs-lookup"><span data-stu-id="6b928-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="6b928-130">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="6b928-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="6b928-131">Legen Sie in der Datei `App.razor` den benutzerdefinierten Inhalt im <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Vorlagenparameter der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="6b928-131">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="6b928-132">Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="6b928-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="6b928-133">Informationen zum Anwenden eines Standardlayouts auf <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalte finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6b928-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="6b928-134">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="6b928-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="6b928-135">Verwenden Sie den <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Parameter, um zusätzliche Assemblys anzugeben, die die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll.</span><span class="sxs-lookup"><span data-stu-id="6b928-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="6b928-136">Angegebene Assemblys werden zusätzlich zur mit `AppAssembly` angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="6b928-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="6b928-137">Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="6b928-138">Im folgenden Beispiel zu <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> wird die Routingunterstützung für `Component1` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="6b928-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="6b928-139">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="6b928-139">Route parameters</span></span>

<span data-ttu-id="6b928-140">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter mit demselben Namen (mit Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="6b928-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="6b928-141">Optionale Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6b928-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="6b928-142">Im vorherigen Beispiel werden zwei `@page`-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="6b928-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="6b928-143">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="6b928-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6b928-144">Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="6b928-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="6b928-145">Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="6b928-145">Route constraints</span></span>

<span data-ttu-id="6b928-146">Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="6b928-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="6b928-147">Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur überein, wenn:</span><span class="sxs-lookup"><span data-stu-id="6b928-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="6b928-148">Ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="6b928-149">Das `Id`-Segment eine Ganzzahl (`int`) ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="6b928-150">Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6b928-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="6b928-151">Informationen zu den Routeneinschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="6b928-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="6b928-152">Constraint</span><span class="sxs-lookup"><span data-stu-id="6b928-152">Constraint</span></span> | <span data-ttu-id="6b928-153">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6b928-153">Example</span></span>           | <span data-ttu-id="6b928-154">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="6b928-154">Example Matches</span></span>                                                                  | <span data-ttu-id="6b928-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="6b928-155">Invariant</span></span><br><span data-ttu-id="6b928-156">Kultur</span><span class="sxs-lookup"><span data-stu-id="6b928-156">culture</span></span><br><span data-ttu-id="6b928-157">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="6b928-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="6b928-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="6b928-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="6b928-159">Nein</span><span class="sxs-lookup"><span data-stu-id="6b928-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="6b928-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="6b928-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="6b928-161">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="6b928-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="6b928-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="6b928-163">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="6b928-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6b928-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6b928-165">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="6b928-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6b928-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6b928-167">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="6b928-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="6b928-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="6b928-169">Nein</span><span class="sxs-lookup"><span data-stu-id="6b928-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="6b928-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6b928-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6b928-171">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="6b928-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6b928-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6b928-173">Ja</span><span class="sxs-lookup"><span data-stu-id="6b928-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="6b928-174">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder <xref:System.DateTime>), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="6b928-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="6b928-175">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="6b928-176">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="6b928-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="6b928-177">In Blazor Server-Apps ist die Standardroute in `_Host.cshtml` `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="6b928-177">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="6b928-178">Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="6b928-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="6b928-179">Eine Blazor-App gibt eine Antwort *404 – Nicht gefunden* für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="6b928-180">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie `_Host.cshtml` mit der folgenden Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="6b928-180">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="6b928-181">Die `"/{**path}"`-Vorlage enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6b928-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="6b928-182">*catch-all*-Syntax mit doppelten Sternchen (`**`) zur Erfassung des Pfades über mehrere Ordnergrenzen hinweg ohne Codierung von Schrägstrichen (`/`).</span><span class="sxs-lookup"><span data-stu-id="6b928-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="6b928-183">Name des `path`-Routenparameters.</span><span class="sxs-lookup"><span data-stu-id="6b928-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="6b928-184">Die *Catch-all*-Parametersyntax (`*`/`**`) wird **nicht** in Razor-Komponenten (`.razor`) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6b928-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="6b928-185">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="6b928-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="6b928-186">NavLink-Komponente</span><span class="sxs-lookup"><span data-stu-id="6b928-186">NavLink component</span></span>

<span data-ttu-id="6b928-187">Verwenden Sie bei der Erstellung von Navigationslinks eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="6b928-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="6b928-188">Eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6b928-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="6b928-189">Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="6b928-190">Die folgende `NavMenu`-Komponente erstellt eine [`Bootstrap`](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6b928-190">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="6b928-191">Es gibt zwei <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="6b928-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="6b928-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6b928-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="6b928-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*Standardwert*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn ein Präfix mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6b928-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="6b928-194">Im vorherigen Beispiel stimmt die Startseite <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="6b928-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="6b928-195">Die zweite <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `MyComponent`-Präfix aufruft (z. B. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="6b928-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="6b928-196">Zusätzliche <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponentenattribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="6b928-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="6b928-197">Im folgenden Beispiel schließt die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente das `target`-Attribut ein:</span><span class="sxs-lookup"><span data-stu-id="6b928-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="6b928-198">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="6b928-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="6b928-199">Hilfsprogramme für URI und Navigationszustand</span><span class="sxs-lookup"><span data-stu-id="6b928-199">URI and navigation state helpers</span></span>

<span data-ttu-id="6b928-200">Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um mit URIs und Navigationselementen in C#-Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6b928-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="6b928-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="6b928-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="6b928-202">Member</span><span class="sxs-lookup"><span data-stu-id="6b928-202">Member</span></span> | <span data-ttu-id="6b928-203">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="6b928-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="6b928-204">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="6b928-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="6b928-205">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6b928-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="6b928-206">In der Regel entspricht <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> dem `href`-Attribut im `<base>`-Element des Dokuments in `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="6b928-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="6b928-207">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="6b928-207">Navigates to the specified URI.</span></span> <span data-ttu-id="6b928-208">Bei `forceLoad` lautet der Wert `true`:</span><span class="sxs-lookup"><span data-stu-id="6b928-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="6b928-209">Clientseitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="6b928-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="6b928-210">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</span><span class="sxs-lookup"><span data-stu-id="6b928-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="6b928-211">Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat.</span><span class="sxs-lookup"><span data-stu-id="6b928-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="6b928-212">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="6b928-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="6b928-213">Wenn ein Basis-URI (z. B. ein URI, der zuvor von <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6b928-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="6b928-214">Die folgende Komponente navigiert zu der `Counter`-Komponente der App, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6b928-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="6b928-215">Die folgende Komponente verarbeitet ein Speicherortwechselereignis, indem <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="6b928-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6b928-216">Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6b928-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="6b928-217">Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="6b928-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="6b928-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> stellt die folgenden Informationen zum Ereignis bereit:</span><span class="sxs-lookup"><span data-stu-id="6b928-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="6b928-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Die URL des neuen Speicherorts.</span><span class="sxs-lookup"><span data-stu-id="6b928-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="6b928-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Wenn der Wert `true` ist, hat Blazor die Navigation vom Browser abgefangen.</span><span class="sxs-lookup"><span data-stu-id="6b928-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="6b928-221">Wenn der Wert `false` ist, hat <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> bewirkt, dass die Navigation erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="6b928-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="6b928-222">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6b928-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
