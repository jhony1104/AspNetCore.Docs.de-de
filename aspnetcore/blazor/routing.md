---
title: ASP.net Core Blazor Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/routing
ms.openlocfilehash: d4b76c00f79f333884fa7e30b27eadc6e36de287
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589938"
---
# <a name="aspnet-core-opno-locblazor-routing"></a><span data-ttu-id="6f871-103">ASP.net Core Blazor Routing</span><span class="sxs-lookup"><span data-stu-id="6f871-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="6f871-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f871-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6f871-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und wie Sie die `NavLink` Komponente verwenden, um Navigations Verknüpfungen in Blazor-apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6f871-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="6f871-106">ASP.net Core Endpunkt-Routing Integration</span><span class="sxs-lookup"><span data-stu-id="6f871-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="6f871-107"> Server ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert.</span><span class="sxs-lookup"><span data-stu-id="6f871-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6f871-108">Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für interaktive Komponenten mit `MapBlazorHub` in `Startup.Configure` akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="6f871-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6f871-109">Die typische Konfiguration besteht darin, alle Anforderungen an eine Razor Page weiterzuleiten, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="6f871-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="6f871-110">Gemäß der Konvention heißt die *Hostseite* in der Regel *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6f871-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="6f871-111">Die in der Hostdatei angegebene Route wird als *Fall Back Route* bezeichnet, da Sie bei der Routen Übereinstimmung mit niedriger Priorität arbeitet.</span><span class="sxs-lookup"><span data-stu-id="6f871-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="6f871-112">Die Fall Back Route wird berücksichtigt, wenn andere Routen nicht gleich sind.</span><span class="sxs-lookup"><span data-stu-id="6f871-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="6f871-113">Dies ermöglicht der APP, andere Controller und Seiten zu verwenden, ohne die Blazor Server-APP zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="6f871-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="6f871-114">Routen Vorlagen</span><span class="sxs-lookup"><span data-stu-id="6f871-114">Route templates</span></span>

<span data-ttu-id="6f871-115">Die Komponente "`Router`" ermöglicht das Routing an jede Komponente mit einer angegebenen Route.</span><span class="sxs-lookup"><span data-stu-id="6f871-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="6f871-116">Die Komponente "`Router`" wird in der Datei " *app. Razor* " angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6f871-116">The `Router` component appears in the *App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="6f871-117">Wenn eine *Razor* -Datei mit einer `@page`-Direktive kompiliert wird, wird der generierten Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> bereitgestellt, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="6f871-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="6f871-118">Zur Laufzeit wird die `RouteView`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="6f871-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="6f871-119">Empfängt die `RouteData` von der `Router` zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="6f871-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="6f871-120">Rendert die angegebene Komponente mit dem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="6f871-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="6f871-121">Optional können Sie einen `DefaultLayout`-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout angeben.</span><span class="sxs-lookup"><span data-stu-id="6f871-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="6f871-122">In den standardmäßigen Blazor Vorlagen wird die `MainLayout` Komponente angegeben.</span><span class="sxs-lookup"><span data-stu-id="6f871-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="6f871-123">*MainLayout. Razor* befindet sich im frei *gegebenen* Ordner des Vorlagen Projekts.</span><span class="sxs-lookup"><span data-stu-id="6f871-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="6f871-124">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6f871-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="6f871-125">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6f871-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6f871-126">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="6f871-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="6f871-127">Damit URLs ordnungsgemäß aufgelöst werden, muss die APP ein `<base>`-Tag in der *wwwroot/Index.html* -Datei (Blazor Webassembly) oder der Datei *pages/_Host. cshtml* (Blazor Server) mit dem App-Basispfad enthalten, der im `href`-Attribut (`<base href="/">`) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="6f871-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="6f871-128">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6f871-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="6f871-129">Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird</span><span class="sxs-lookup"><span data-stu-id="6f871-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="6f871-130">Die Komponente "`Router`" ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="6f871-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="6f871-131">Legen Sie in der Datei " *app. Razor* " benutzerdefinierten Inhalt im `NotFound`-Vorlagen Parameter der Komponente "`Router`" fest:</span><span class="sxs-lookup"><span data-stu-id="6f871-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```cshtml
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

<span data-ttu-id="6f871-132">Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente (z. b. andere interaktive Komponenten) enthalten.</span><span class="sxs-lookup"><span data-stu-id="6f871-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="6f871-133">Informationen zum Anwenden eines Standard Layouts auf `NotFound`-Inhalt finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6f871-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="6f871-134">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="6f871-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="6f871-135">Verwenden Sie den `AdditionalAssemblies`-Parameter, um zusätzliche Assemblys für die `Router`-Komponente anzugeben, die bei der Suche nach Routing fähigen Komponenten berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="6f871-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="6f871-136">Angegebene Assemblys werden zusätzlich zur `AppAssembly` angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="6f871-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="6f871-137">Im folgenden Beispiel ist `Component1` eine Routing fähige Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="6f871-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="6f871-138">Das folgende Beispiel `AdditionalAssemblies` führt zu einer Routing Unterstützung für `Component1`:</span><span class="sxs-lookup"><span data-stu-id="6f871-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="6f871-139">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="6f871-139">Route parameters</span></span>

<span data-ttu-id="6f871-140">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter mit dem gleichen Namen (ohne Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="6f871-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="6f871-141">Optionale Parameter werden für Blazor-apps in ASP.net Core 3,0 nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6f871-141">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6f871-142">Im vorherigen Beispiel werden zwei `@page`-Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="6f871-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="6f871-143">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="6f871-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6f871-144">Die zweite `@page`-Direktive übernimmt den `{text}`-Routen Parameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="6f871-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="6f871-145">Routen Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="6f871-145">Route constraints</span></span>

<span data-ttu-id="6f871-146">Eine Routen Einschränkung erzwingt die Typübereinstimmung in einem Routen Segment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="6f871-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="6f871-147">Im folgenden Beispiel entspricht die Route an die Komponente "`Users`" nur, wenn:</span><span class="sxs-lookup"><span data-stu-id="6f871-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="6f871-148">Ein `Id`-Routen Segment ist in der Anforderungs-URL vorhanden.</span><span class="sxs-lookup"><span data-stu-id="6f871-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="6f871-149">Das `Id`-Segment ist eine Ganzzahl (`int`).</span><span class="sxs-lookup"><span data-stu-id="6f871-149">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="6f871-150">Die in der folgenden Tabelle aufgeführten Routen Einschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6f871-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="6f871-151">Informationen zu den Routen Einschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="6f871-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="6f871-152">Constraint</span><span class="sxs-lookup"><span data-stu-id="6f871-152">Constraint</span></span> | <span data-ttu-id="6f871-153">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6f871-153">Example</span></span>           | <span data-ttu-id="6f871-154">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="6f871-154">Example Matches</span></span>                                                                  | <span data-ttu-id="6f871-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="6f871-155">Invariant</span></span><br><span data-ttu-id="6f871-156">Kultur</span><span class="sxs-lookup"><span data-stu-id="6f871-156">culture</span></span><br><span data-ttu-id="6f871-157">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="6f871-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="6f871-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="6f871-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="6f871-159">Nein</span><span class="sxs-lookup"><span data-stu-id="6f871-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="6f871-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="6f871-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="6f871-161">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="6f871-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="6f871-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="6f871-163">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="6f871-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6f871-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6f871-165">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="6f871-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6f871-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6f871-167">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="6f871-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="6f871-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="6f871-169">Nein</span><span class="sxs-lookup"><span data-stu-id="6f871-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="6f871-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6f871-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6f871-171">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="6f871-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6f871-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6f871-173">Ja</span><span class="sxs-lookup"><span data-stu-id="6f871-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="6f871-174">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="6f871-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="6f871-175">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="6f871-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="6f871-176">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="6f871-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="6f871-177">In Blazor Server-Apps ist die Standardroute in *_Host. cshtml* `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="6f871-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="6f871-178">Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei erscheint.</span><span class="sxs-lookup"><span data-stu-id="6f871-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="6f871-179">Eine Blazor-App gibt eine *404-nicht gefundene* Antwort für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6f871-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="6f871-180">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host. cshtml* mit der folgenden Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="6f871-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="6f871-181">Die Vorlage "`"/{**path}"`" umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6f871-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="6f871-182">Doppelte Sternchen- *catch-all* -Syntax (`**`), um den Pfad über mehrere Ordner Grenzen ohne Codierungs Schrägstriche (`/`) zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="6f871-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="6f871-183">Ein `path`-Routen Parameter Name.</span><span class="sxs-lookup"><span data-stu-id="6f871-183">A `path` route parameter name.</span></span>

<span data-ttu-id="6f871-184">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="6f871-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="6f871-185">Navlink-Komponente</span><span class="sxs-lookup"><span data-stu-id="6f871-185">NavLink component</span></span>

<span data-ttu-id="6f871-186">Verwenden Sie beim Erstellen von Navigationslinks eine `NavLink`-Komponente anstelle von HTML-Hyperlink-Elementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="6f871-186">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="6f871-187">Eine `NavLink`-Komponente verhält sich wie ein `<a>`-Element, mit dem Unterschied, dass eine `active`-CSS-Klasse basierend darauf umgeschaltet wird, ob Ihre `href` der aktuellen URL entspricht.</span><span class="sxs-lookup"><span data-stu-id="6f871-187">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="6f871-188">Die `active`-Klasse hilft einem Benutzer zu verstehen, welche Seite die aktive Seite unter den angezeigten Navigationslinks ist.</span><span class="sxs-lookup"><span data-stu-id="6f871-188">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="6f871-189">Die folgende `NavMenu`-Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/) -Navigationsleiste, die die Verwendung von `NavLink`-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6f871-189">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="6f871-190">Es gibt zwei `NavLinkMatch`-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="6f871-190">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="6f871-191">`NavLinkMatch.All` &ndash; der `NavLink` ist aktiv, wenn er mit der gesamten aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6f871-191">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="6f871-192">`NavLinkMatch.Prefix` (*Standard*) &ndash; ist der `NavLink` aktiv, wenn er mit einem beliebigen Präfix der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="6f871-192">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="6f871-193">Im vorhergehenden Beispiel entspricht der Home-`NavLink` `href=""` der Home-URL und empfängt nur die `active`-CSS-Klasse an der Standard-Basispfad-URL der APP (z. b. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="6f871-193">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="6f871-194">Der zweite `NavLink` empfängt die `active`-Klasse, wenn der Benutzer eine URL mit einem `MyComponent`-Präfix (z. b. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`) besucht.</span><span class="sxs-lookup"><span data-stu-id="6f871-194">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="6f871-195">Zusätzliche `NavLink`-Komponenten Attribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="6f871-195">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="6f871-196">Im folgenden Beispiel enthält die Komponente "`NavLink`" das Attribut "`target`":</span><span class="sxs-lookup"><span data-stu-id="6f871-196">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="6f871-197">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="6f871-197">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="6f871-198">URI-und Navigations Zustands Hilfen</span><span class="sxs-lookup"><span data-stu-id="6f871-198">URI and navigation state helpers</span></span>

<span data-ttu-id="6f871-199">Verwenden Sie `Microsoft.AspNetCore.Components.NavigationManager`, um mit URIs und der C# Navigation in Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6f871-199">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="6f871-200">`NavigationManager` stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="6f871-200">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="6f871-201">Member</span><span class="sxs-lookup"><span data-stu-id="6f871-201">Member</span></span> | <span data-ttu-id="6f871-202">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="6f871-202">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="6f871-203">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="6f871-203">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="6f871-204">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6f871-204">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="6f871-205">In der Regel entspricht `BaseUri` dem `href`-Attribut im `<base>`-Element des Dokuments in *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="6f871-205">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="6f871-206">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="6f871-206">Navigates to the specified URI.</span></span> <span data-ttu-id="6f871-207">Wenn `forceLoad` `true` ist:</span><span class="sxs-lookup"><span data-stu-id="6f871-207">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="6f871-208">Client seitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="6f871-208">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="6f871-209">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="6f871-209">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="6f871-210">Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat.</span><span class="sxs-lookup"><span data-stu-id="6f871-210">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="6f871-211">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="6f871-211">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="6f871-212">Wenn ein Basis-URI (z. b. ein URI, der zuvor von `GetBaseUri` zurückgegeben wurde) angegeben wurde, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix.</span><span class="sxs-lookup"><span data-stu-id="6f871-212">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="6f871-213">Wenn die Schaltfläche ausgewählt wird, navigiert die folgende Komponente zur `Counter`-Komponente der APP:</span><span class="sxs-lookup"><span data-stu-id="6f871-213">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
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
