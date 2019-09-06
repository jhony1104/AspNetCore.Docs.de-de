---
title: ASP.net Core blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: ae3d7ab01185dd6f2e8e0f59b78c2e693fe464b0
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310344"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="9da1c-103">ASP.net Core blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="9da1c-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="9da1c-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9da1c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9da1c-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und wie `NavLink` Sie die-Komponente verwenden, um Navigations Verknüpfungen in blazor-apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="9da1c-106">ASP.net Core Endpunkt-Routing Integration</span><span class="sxs-lookup"><span data-stu-id="9da1c-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="9da1c-107">Der serverseitige blazor ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert.</span><span class="sxs-lookup"><span data-stu-id="9da1c-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="9da1c-108">Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für `MapBlazorHub` interaktive `Startup.Configure`Komponenten mit in akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="9da1c-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="9da1c-109">Routen Vorlagen</span><span class="sxs-lookup"><span data-stu-id="9da1c-109">Route templates</span></span>

<span data-ttu-id="9da1c-110">Die `Router` Komponente aktiviert das Routing, und jeder zugänglichen Komponente wird eine Routen Vorlage bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="9da1c-111">Die `Router` Komponente wird in der Datei " *app. Razor* " angezeigt:</span><span class="sxs-lookup"><span data-stu-id="9da1c-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="9da1c-112">In einer serverseitigen oder Client seitigen blazor-App:</span><span class="sxs-lookup"><span data-stu-id="9da1c-112">In a Blazor server-side or client-side app:</span></span>

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

<span data-ttu-id="9da1c-113">Wenn eine *Razor* -Datei mit einer `@page` -Direktive kompiliert wird, wird der generierten Klasse <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> eine bereitgestellt, die die Routen Vorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-113">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="9da1c-114">Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert die Komponente mit einer Routen Vorlage, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="9da1c-115">Mehrere Routen Vorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9da1c-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="9da1c-116">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="9da1c-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="9da1c-117">Damit URLs ordnungsgemäß aufgelöst werden können, muss die APP `<base>` ein Tag in der *wwwroot/Index.html* -Datei (Client seitige blazor) oder *pages/_Host. cshtml* -Datei (blazor serverseitig) mit dem im- `href` Attribut angegebenen app-Basispfad enthalten ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="9da1c-117">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="9da1c-118">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9da1c-118">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="9da1c-119">Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird</span><span class="sxs-lookup"><span data-stu-id="9da1c-119">Provide custom content when content isn't found</span></span>

<span data-ttu-id="9da1c-120">Die `Router` -Komponente ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="9da1c-120">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="9da1c-121">Legen Sie in der Datei " *app. Razor* " benutzerdefinierten `<NotFound>` Inhalt im `Router` Vorlagen Parameter der Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="9da1c-121">In the *App.razor* file, set custom content in the `<NotFound>` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="9da1c-122">Der Inhalt von `<NotFound>` kann beliebige Elemente enthalten, wie z. b. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="9da1c-122">The content of `<NotFound>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="9da1c-123">Routen Parameter</span><span class="sxs-lookup"><span data-stu-id="9da1c-123">Route parameters</span></span>

<span data-ttu-id="9da1c-124">Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter mit dem gleichen Namen (ohne Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="9da1c-124">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="9da1c-125">Optionale Parameter werden für blazor-apps in ASP.net Core 3,0 Preview nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-125">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="9da1c-126">Im `@page` vorherigen Beispiel werden zwei-Direktiven angewendet.</span><span class="sxs-lookup"><span data-stu-id="9da1c-126">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="9da1c-127">Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter.</span><span class="sxs-lookup"><span data-stu-id="9da1c-127">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9da1c-128">Die zweite `@page` Anweisung übernimmt den `{text}` Routen Parameter und `Text` weist den Wert der-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="9da1c-128">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="9da1c-129">Routen Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="9da1c-129">Route constraints</span></span>

<span data-ttu-id="9da1c-130">Eine Routen Einschränkung erzwingt die Typübereinstimmung in einem Routen Segment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="9da1c-130">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="9da1c-131">Im folgenden Beispiel entspricht die Route an die `Users` -Komponente nur, wenn:</span><span class="sxs-lookup"><span data-stu-id="9da1c-131">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="9da1c-132">Ein `Id` Routen Segment ist in der Anforderungs-URL vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9da1c-132">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="9da1c-133">Das `Id` Segment ist eine ganze Zahl`int`().</span><span class="sxs-lookup"><span data-stu-id="9da1c-133">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="9da1c-134">Die in der folgenden Tabelle aufgeführten Routen Einschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9da1c-134">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="9da1c-135">Informationen zu den Routen Einschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9da1c-135">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="9da1c-136">Constraint</span><span class="sxs-lookup"><span data-stu-id="9da1c-136">Constraint</span></span> | <span data-ttu-id="9da1c-137">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9da1c-137">Example</span></span>           | <span data-ttu-id="9da1c-138">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="9da1c-138">Example Matches</span></span>                                                                  | <span data-ttu-id="9da1c-139">Invariante</span><span class="sxs-lookup"><span data-stu-id="9da1c-139">Invariant</span></span><br><span data-ttu-id="9da1c-140">culture</span><span class="sxs-lookup"><span data-stu-id="9da1c-140">culture</span></span><br><span data-ttu-id="9da1c-141">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="9da1c-141">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="9da1c-142">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9da1c-142">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="9da1c-143">Nein</span><span class="sxs-lookup"><span data-stu-id="9da1c-143">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="9da1c-144">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9da1c-144">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="9da1c-145">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-145">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="9da1c-146">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9da1c-146">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="9da1c-147">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-147">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="9da1c-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9da1c-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9da1c-149">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-149">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="9da1c-150">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9da1c-150">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9da1c-151">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-151">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="9da1c-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9da1c-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9da1c-153">Nein</span><span class="sxs-lookup"><span data-stu-id="9da1c-153">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="9da1c-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9da1c-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9da1c-155">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-155">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="9da1c-156">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9da1c-156">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9da1c-157">Ja</span><span class="sxs-lookup"><span data-stu-id="9da1c-157">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="9da1c-158">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="9da1c-158">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9da1c-159">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="9da1c-159">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="9da1c-160">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="9da1c-160">Routing with URLs that contain dots</span></span>

<span data-ttu-id="9da1c-161">In serverseitigen blazor-Apps ist `/` die Standardroute in *_Host. cshtml* (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="9da1c-161">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="9da1c-162">Eine Anforderungs-URL, die einen Punkt`.`() enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei erscheint.</span><span class="sxs-lookup"><span data-stu-id="9da1c-162">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="9da1c-163">Eine blazor-App gibt eine " *404-nicht gefunden"-* Antwort für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="9da1c-163">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="9da1c-164">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host. cshtml* mit der folgenden Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="9da1c-164">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="9da1c-165">Die `"/{**path}"` Vorlage umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9da1c-165">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="9da1c-166">Doppelte Sternchen *-catch-all-* Syntax`**`() zum Erfassen des Pfads über mehrere Ordner Grenzen ohne Codierungs schräg`/`Striche ().</span><span class="sxs-lookup"><span data-stu-id="9da1c-166">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="9da1c-167">Ein `path` Routen Parameter Name.</span><span class="sxs-lookup"><span data-stu-id="9da1c-167">A `path` route parameter name.</span></span>

<span data-ttu-id="9da1c-168">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="9da1c-168">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="9da1c-169">Navlink-Komponente</span><span class="sxs-lookup"><span data-stu-id="9da1c-169">NavLink component</span></span>

<span data-ttu-id="9da1c-170">Verwenden Sie `NavLink` beim Erstellen von Navigationslinks eine Komponente anstelle`<a>`von HTML-Hyperlink-Elementen ().</span><span class="sxs-lookup"><span data-stu-id="9da1c-170">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="9da1c-171">Eine `NavLink` -Komponente verhält sich `<a>` wie ein-Element, mit der Ausnahme `active` , dass Sie eine CSS- `href` Klasse umschaltet, je nachdem, ob Ihre der aktuellen URL entspricht.</span><span class="sxs-lookup"><span data-stu-id="9da1c-171">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="9da1c-172">Mithilfe `active` der-Klasse kann ein Benutzer verstehen, welche Seite die aktive Seite unter den angezeigten Navigationslinks ist.</span><span class="sxs-lookup"><span data-stu-id="9da1c-172">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="9da1c-173">Die folgende `NavMenu` Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/) -Navigationsleiste, die die Verwendung `NavLink` von-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="9da1c-173">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="9da1c-174">Es gibt zwei `NavLinkMatch` Optionen, die Sie dem `Match` -Attribut des `<NavLink>` -Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="9da1c-174">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="9da1c-175">`NavLinkMatch.All`&ndash; Der`NavLink` ist aktiv, wenn er mit der gesamten aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-175">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="9da1c-176">`NavLinkMatch.Prefix`(*Standard*) &ndash; Der`NavLink` ist aktiv, wenn er mit einem beliebigen Präfix der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-176">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="9da1c-177">Im vorherigen `NavLink` Beispiel stimmt die Start `href=""` -URL mit der Home-URL überein und `active` empfängt nur die CSS-Klasse an der `https://localhost:5001/`Standard-Basispfad-URL der APP (z. b.).</span><span class="sxs-lookup"><span data-stu-id="9da1c-177">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="9da1c-178">Die zweite `NavLink` empfängt die `active` -Klasse, wenn der Benutzer eine URL mit `MyComponent` einem Präfix (z `https://localhost:5001/MyComponent` . `https://localhost:5001/MyComponent/AnotherSegment`b. und) besucht.</span><span class="sxs-lookup"><span data-stu-id="9da1c-178">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="9da1c-179">Zusätzliche `NavLink` Komponenten Attribute werden an das gerenderte Ankertag weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="9da1c-179">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="9da1c-180">Im folgenden Beispiel enthält die `NavLink` -Komponente das `target` -Attribut:</span><span class="sxs-lookup"><span data-stu-id="9da1c-180">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="9da1c-181">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="9da1c-181">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="9da1c-182">URI-und Navigations Zustands Hilfen</span><span class="sxs-lookup"><span data-stu-id="9da1c-182">URI and navigation state helpers</span></span>

<span data-ttu-id="9da1c-183">Verwenden `Microsoft.AspNetCore.Components.NavigationManager` Sie, um mit URIs und der C# Navigation in Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9da1c-183">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="9da1c-184">`NavigationManager`bietet das Ereignis und die Methoden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="9da1c-184">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="9da1c-185">Member</span><span class="sxs-lookup"><span data-stu-id="9da1c-185">Member</span></span> | <span data-ttu-id="9da1c-186">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9da1c-186">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="9da1c-187">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="9da1c-187">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="9da1c-188">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="9da1c-188">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="9da1c-189">`<base>` `href` `BaseUri` In der Regel entspricht dem-Attribut im-Element des Dokuments in *wwwroot/Index.html* (Blade Client-Side) oder *pages/_Host. cshtml* (serverseitiges Blade von blazor).</span><span class="sxs-lookup"><span data-stu-id="9da1c-189">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="9da1c-190">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="9da1c-190">Navigates to the specified URI.</span></span> <span data-ttu-id="9da1c-191">Wenn `forceLoad` ist `true`:</span><span class="sxs-lookup"><span data-stu-id="9da1c-191">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="9da1c-192">Client seitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-192">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="9da1c-193">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="9da1c-193">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="9da1c-194">Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat.</span><span class="sxs-lookup"><span data-stu-id="9da1c-194">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="9da1c-195">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="9da1c-195">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="9da1c-196">Wenn ein Basis-URI (z. b. ein URI, `GetBaseUri`der zuvor von zurückgegeben wurde) angegeben wird, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix.</span><span class="sxs-lookup"><span data-stu-id="9da1c-196">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="9da1c-197">Wenn die Schaltfläche ausgewählt wird, navigiert `Counter` die folgende Komponente zur Komponente der APP:</span><span class="sxs-lookup"><span data-stu-id="9da1c-197">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

