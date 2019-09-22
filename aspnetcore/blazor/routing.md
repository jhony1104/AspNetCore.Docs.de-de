---
title: ASP.net Core blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2019
uid: blazor/routing
ms.openlocfilehash: d6fb3f03be94ff99ac3ed434265e6cd6b752c625
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176405"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="bb264-103">ASP.net Core blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="bb264-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="bb264-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb264-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bb264-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und wie `NavLink` Sie die-Komponente verwenden, um Navigations Verknüpfungen in blazor-apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bb264-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="bb264-106">ASP.net Core Endpunkt-Routing Integration</span><span class="sxs-lookup"><span data-stu-id="bb264-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="bb264-107">Der blazor-Server ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert.</span><span class="sxs-lookup"><span data-stu-id="bb264-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="bb264-108">Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für `MapBlazorHub` interaktive `Startup.Configure`Komponenten mit in akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="bb264-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="bb264-109">Die typische Konfiguration besteht darin, alle Anforderungen an eine Razor Page weiterzuleiten, die als Host für den serverseitigen Teil der blazor-Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="bb264-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="bb264-110">Gemäß der Konvention heißt die *Hostseite* in der Regel *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb264-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="bb264-111">Die in der Hostdatei angegebene Route wird als *Fall Back Route* bezeichnet, da Sie bei der Routen Übereinstimmung mit niedriger Priorität arbeitet.</span><span class="sxs-lookup"><span data-stu-id="bb264-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="bb264-112">Die Fall Back Route wird berücksichtigt, wenn andere Routen nicht gleich sind.</span><span class="sxs-lookup"><span data-stu-id="bb264-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="bb264-113">Dies ermöglicht der APP, andere Controller und Seiten zu verwenden, ohne die blazor-Server-APP zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="bb264-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="bb264-114">Routen Vorlagen</span><span class="sxs-lookup"><span data-stu-id="bb264-114">Route templates</span></span>

<span data-ttu-id="bb264-115">Die `Router` Komponente ermöglicht das Routing an jede Komponente mit einer angegebenen Route.</span><span class="sxs-lookup"><span data-stu-id="bb264-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="bb264-116">Die `Router` Komponente wird in der Datei " *app. Razor* " angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bb264-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="bb264-117">Wenn eine *Razor* -Datei mit einer `@page` -Direktive kompiliert wird, wird der generierten Klasse <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> eine bereitgestellt, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="bb264-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="bb264-118">Zur Laufzeit ist die `RouteView` -Komponente:</span><span class="sxs-lookup"><span data-stu-id="bb264-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="bb264-119">Empfängt den `RouteData` von der `Router` zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="bb264-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="bb264-120">Rendert die angegebene Komponente mit dem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="bb264-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="bb264-121">Optional können Sie einen `DefaultLayout` Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout angeben.</span><span class="sxs-lookup"><span data-stu-id="bb264-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="bb264-122">Die Standardvorlagen für blazor geben `MainLayout` die Komponente an.</span><span class="sxs-lookup"><span data-stu-id="bb264-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="bb264-123">*MainLayout. Razor* befindet sich im frei *gegebenen* Ordner des Vorlagen Projekts.</span><span class="sxs-lookup"><span data-stu-id="bb264-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="bb264-124">Weitere Informationen zu Layouts finden <xref:blazor/layouts>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="bb264-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="bb264-125">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="bb264-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="bb264-126">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="bb264-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="bb264-127">Damit URLs ordnungsgemäß aufgelöst werden, muss die APP ein `<base>` Tag in der *wwwroot/Index.html* -Datei (blazor Webassembly) oder in der Datei *pages/_Host. cshtml* (Blade Server) mit dem `href` App-Basispfad enthalten, der im-Attribut angegeben ist (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="bb264-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="bb264-128">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="bb264-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="bb264-129">Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird</span><span class="sxs-lookup"><span data-stu-id="bb264-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="bb264-130">Die `Router` -Komponente ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="bb264-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="bb264-131">Legen Sie in der Datei " *app. Razor* " benutzerdefinierten `NotFound` Inhalt im `Router` Vorlagen Parameter der Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="bb264-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="bb264-132">Der Inhalt von `<NotFound>` Tags kann beliebige Elemente enthalten, wie z. b. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="bb264-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="bb264-133">Informationen zum Anwenden eines Standard Layouts `NotFound` auf Inhalt finden <xref:blazor/layouts>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="bb264-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="bb264-134">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="bb264-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="bb264-135">Verwenden Sie `AdditionalAssemblies` den-Parameter, um zusätzliche Assemblys anzugeben, die bei der `Router` Suche nach Routing fähigen Komponenten in Erwägung gezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="bb264-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="bb264-136">Angegebene Assemblys werden zusätzlich zur `AppAssembly`-angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="bb264-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="bb264-137">Im folgenden Beispiel ist eine `Component1` Routing fähige Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="bb264-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="bb264-138">Das folgende `AdditionalAssemblies` Beispiel führt zu einer Routing Unterstützung `Component1`für:</span><span class="sxs-lookup"><span data-stu-id="bb264-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="bb264-139">< routerappassembly = "typeof (Programm). Assembly "additionalassemblys =" New [] {typeof (Component1). Assembly} >...</Router></span><span class="sxs-lookup"><span data-stu-id="bb264-139"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="bb264-140">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="bb264-140">Route parameters</span></span>

<span data-ttu-id="bb264-141">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter mit dem gleichen Namen (ohne Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="bb264-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="bb264-142">Optionale Parameter werden für blazor-apps in ASP.net Core 3,0 Preview nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bb264-142">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="bb264-143">Im `@page` vorherigen Beispiel werden zwei-Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="bb264-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="bb264-144">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="bb264-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="bb264-145">Die zweite `@page` Anweisung übernimmt den `{text}` Routen Parameter und `Text` weist den Wert der-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="bb264-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="bb264-146">Routen Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="bb264-146">Route constraints</span></span>

<span data-ttu-id="bb264-147">Eine Routen Einschränkung erzwingt die Typübereinstimmung in einem Routen Segment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="bb264-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="bb264-148">Im folgenden Beispiel entspricht die Route an die `Users` -Komponente nur, wenn:</span><span class="sxs-lookup"><span data-stu-id="bb264-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="bb264-149">Ein `Id` Routen Segment ist in der Anforderungs-URL vorhanden.</span><span class="sxs-lookup"><span data-stu-id="bb264-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="bb264-150">Das `Id` Segment ist eine ganze Zahl`int`().</span><span class="sxs-lookup"><span data-stu-id="bb264-150">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="bb264-151">Die in der folgenden Tabelle aufgeführten Routen Einschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bb264-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="bb264-152">Informationen zu den Routen Einschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="bb264-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="bb264-153">Constraint</span><span class="sxs-lookup"><span data-stu-id="bb264-153">Constraint</span></span> | <span data-ttu-id="bb264-154">Beispiel</span><span class="sxs-lookup"><span data-stu-id="bb264-154">Example</span></span>           | <span data-ttu-id="bb264-155">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="bb264-155">Example Matches</span></span>                                                                  | <span data-ttu-id="bb264-156">Invariante</span><span class="sxs-lookup"><span data-stu-id="bb264-156">Invariant</span></span><br><span data-ttu-id="bb264-157">Kultur</span><span class="sxs-lookup"><span data-stu-id="bb264-157">culture</span></span><br><span data-ttu-id="bb264-158">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="bb264-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="bb264-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="bb264-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="bb264-160">Nein</span><span class="sxs-lookup"><span data-stu-id="bb264-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="bb264-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="bb264-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="bb264-162">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="bb264-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="bb264-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="bb264-164">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="bb264-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bb264-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bb264-166">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="bb264-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bb264-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bb264-168">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="bb264-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="bb264-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="bb264-170">Nein</span><span class="sxs-lookup"><span data-stu-id="bb264-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="bb264-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bb264-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bb264-172">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="bb264-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bb264-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bb264-174">Ja</span><span class="sxs-lookup"><span data-stu-id="bb264-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="bb264-175">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="bb264-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="bb264-176">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="bb264-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="bb264-177">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="bb264-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="bb264-178">In blazor-Server-Apps ist `/` die Standardroute in *_Host. cshtml* (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="bb264-178">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="bb264-179">Eine Anforderungs-URL, die einen Punkt`.`() enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei erscheint.</span><span class="sxs-lookup"><span data-stu-id="bb264-179">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="bb264-180">Eine blazor-App gibt eine " *404-nicht gefunden"-* Antwort für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bb264-180">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="bb264-181">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host. cshtml* mit der folgenden Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="bb264-181">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="bb264-182">Die `"/{**path}"` Vorlage umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bb264-182">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="bb264-183">Doppelte Sternchen *-catch-all-* Syntax`**`() zum Erfassen des Pfads über mehrere Ordner Grenzen ohne Codierungs schräg`/`Striche ().</span><span class="sxs-lookup"><span data-stu-id="bb264-183">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="bb264-184">Ein `path` Routen Parameter Name.</span><span class="sxs-lookup"><span data-stu-id="bb264-184">A `path` route parameter name.</span></span>

<span data-ttu-id="bb264-185">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="bb264-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="bb264-186">Navlink-Komponente</span><span class="sxs-lookup"><span data-stu-id="bb264-186">NavLink component</span></span>

<span data-ttu-id="bb264-187">Verwenden Sie `NavLink` beim Erstellen von Navigationslinks eine Komponente anstelle`<a>`von HTML-Hyperlink-Elementen ().</span><span class="sxs-lookup"><span data-stu-id="bb264-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="bb264-188">Eine `NavLink` -Komponente verhält sich `<a>` wie ein-Element, mit der Ausnahme `active` , dass Sie eine CSS- `href` Klasse umschaltet, je nachdem, ob Ihre der aktuellen URL entspricht.</span><span class="sxs-lookup"><span data-stu-id="bb264-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="bb264-189">Mithilfe `active` der-Klasse kann ein Benutzer verstehen, welche Seite die aktive Seite unter den angezeigten Navigationslinks ist.</span><span class="sxs-lookup"><span data-stu-id="bb264-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="bb264-190">Die folgende `NavMenu` Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/) -Navigationsleiste, die die Verwendung `NavLink` von-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="bb264-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="bb264-191">Es gibt zwei `NavLinkMatch` Optionen, die Sie dem `Match` -Attribut des `<NavLink>` -Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="bb264-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="bb264-192">`NavLinkMatch.All`&ndash; Der`NavLink` ist aktiv, wenn er mit der gesamten aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bb264-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="bb264-193">`NavLinkMatch.Prefix`(*Standard*) &ndash; Der`NavLink` ist aktiv, wenn er mit einem beliebigen Präfix der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bb264-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="bb264-194">Im vorherigen `NavLink` Beispiel stimmt die Start `href=""` -URL mit der Home-URL überein und `active` empfängt nur die CSS-Klasse an der `https://localhost:5001/`Standard-Basispfad-URL der APP (z. b.).</span><span class="sxs-lookup"><span data-stu-id="bb264-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="bb264-195">Die zweite `NavLink` empfängt die `active` -Klasse, wenn der Benutzer eine URL mit `MyComponent` einem Präfix (z `https://localhost:5001/MyComponent` . `https://localhost:5001/MyComponent/AnotherSegment`b. und) besucht.</span><span class="sxs-lookup"><span data-stu-id="bb264-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="bb264-196">Zusätzliche `NavLink` Komponenten Attribute werden an das gerenderte Ankertag weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="bb264-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="bb264-197">Im folgenden Beispiel enthält die `NavLink` -Komponente das `target` -Attribut:</span><span class="sxs-lookup"><span data-stu-id="bb264-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="bb264-198">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="bb264-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="bb264-199">URI-und Navigations Zustands Hilfen</span><span class="sxs-lookup"><span data-stu-id="bb264-199">URI and navigation state helpers</span></span>

<span data-ttu-id="bb264-200">Verwenden `Microsoft.AspNetCore.Components.NavigationManager` Sie, um mit URIs und der C# Navigation in Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="bb264-200">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="bb264-201">`NavigationManager`bietet das Ereignis und die Methoden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="bb264-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="bb264-202">Member</span><span class="sxs-lookup"><span data-stu-id="bb264-202">Member</span></span> | <span data-ttu-id="bb264-203">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bb264-203">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="bb264-204">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="bb264-204">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="bb264-205">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="bb264-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="bb264-206">`<base>` `href` `BaseUri` In der Regel entspricht dem-Attribut im-Element des Dokuments in *wwwroot/Index.html* (blazor Webassembly) oder *pages/_Host. cshtml* (blazor Server).</span><span class="sxs-lookup"><span data-stu-id="bb264-206">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="bb264-207">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="bb264-207">Navigates to the specified URI.</span></span> <span data-ttu-id="bb264-208">Wenn `forceLoad` ist `true`:</span><span class="sxs-lookup"><span data-stu-id="bb264-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="bb264-209">Client seitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="bb264-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="bb264-210">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="bb264-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="bb264-211">Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat.</span><span class="sxs-lookup"><span data-stu-id="bb264-211">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="bb264-212">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="bb264-212">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="bb264-213">Wenn ein Basis-URI (z. b. ein URI, `GetBaseUri`der zuvor von zurückgegeben wurde) angegeben wird, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix.</span><span class="sxs-lookup"><span data-stu-id="bb264-213">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="bb264-214">Wenn die Schaltfläche ausgewählt wird, navigiert `Counter` die folgende Komponente zur Komponente der APP:</span><span class="sxs-lookup"><span data-stu-id="bb264-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
