---
title: ASP.NET Core Blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in Apps und über die NavLink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218894"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="2a179-103">ASP.NET Core Blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="2a179-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="2a179-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a179-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2a179-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und die `NavLink`-Komponente verwenden, um Navigationslinks in Blazor-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2a179-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="2a179-106">Integration von ASP.NET Core-Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="2a179-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="2a179-107">Blazor Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert.</span><span class="sxs-lookup"><span data-stu-id="2a179-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="2a179-108">Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit `MapBlazorHub` in `Startup.Configure` akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="2a179-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="2a179-109">Die typischste Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="2a179-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="2a179-110">Gemäß der Konvention wird die *Hostseite* normalerweise *_Host.cshtml* genannt.</span><span class="sxs-lookup"><span data-stu-id="2a179-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="2a179-111">Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="2a179-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="2a179-112">Die Fallbackroute wird verwendet, wenn andere Routen nicht passen.</span><span class="sxs-lookup"><span data-stu-id="2a179-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="2a179-113">Dadurch kann die App andere Controller und Seiten verwenden, ohne die Blazor Server-App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="2a179-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="2a179-114">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="2a179-114">Route templates</span></span>

<span data-ttu-id="2a179-115">Die `Router`-Komponente ermöglicht das Routing zu jeder Komponente mit einer bestimmten Route.</span><span class="sxs-lookup"><span data-stu-id="2a179-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="2a179-116">Die `Router`-Komponente wird in der Datei *App.razor* angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2a179-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="2a179-117">Wenn eine *.razor*-Datei mit einer `@page`-Anweisung kompiliert wird, erhält die generierte Klasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Element, das die Routenvorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="2a179-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="2a179-118">Zur Laufzeit führt die `RouteView`-Komponente Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="2a179-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="2a179-119">Sie empfängt das `RouteData`-Element aus dem `Router`-Element zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="2a179-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="2a179-120">Sie rendert die angegebene Komponente mit ihrem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="2a179-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="2a179-121">Optional können Sie einen `DefaultLayout`-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout festlegen.</span><span class="sxs-lookup"><span data-stu-id="2a179-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="2a179-122">In den Blazor-Standardvorlagen wird die `MainLayout`-Komponente angegeben.</span><span class="sxs-lookup"><span data-stu-id="2a179-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="2a179-123">*MainLayout.razor* befindet sich im *Freigabeordner* mit den Vorlagen für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="2a179-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="2a179-124">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="2a179-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="2a179-125">Mehrere Routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2a179-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="2a179-126">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="2a179-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="2a179-127">Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer *wwwwroot/index.html*-Datei (Blazor WebAssembly) oder *Pages/_Host.cshtml*-Datei (Blazor Server) mit dem im `href`-Attribut (`<base href="/">`) angegebenen App-Basispfad enthalten.</span><span class="sxs-lookup"><span data-stu-id="2a179-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="2a179-128">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="2a179-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="2a179-129">Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde</span><span class="sxs-lookup"><span data-stu-id="2a179-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="2a179-130">Die `Router`-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="2a179-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="2a179-131">Legen Sie in der Datei *App.razor* den benutzerdefinierten Inhalt im `NotFound`-Vorlagenparameter der `Router`-Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="2a179-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="2a179-132">Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2a179-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="2a179-133">Informationen zum Anwenden eines Standardlayouts auf `NotFound`-Inhalte finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="2a179-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="2a179-134">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="2a179-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="2a179-135">Verwenden Sie den `AdditionalAssemblies`-Parameter, um zusätzliche Assemblys anzugeben, die die `Router`-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll.</span><span class="sxs-lookup"><span data-stu-id="2a179-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="2a179-136">Angegebene Assemblys werden zusätzlich zur mit `AppAssembly` angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="2a179-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="2a179-137">Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="2a179-138">Im folgenden Beispiel zu `AdditionalAssemblies` wird die Routingunterstützung für `Component1` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="2a179-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="2a179-139">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="2a179-139">Route parameters</span></span>

<span data-ttu-id="2a179-140">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter mit demselben Namen (mit Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="2a179-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="2a179-141">Optionale Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2a179-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="2a179-142">Im vorherigen Beispiel werden zwei `@page`-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="2a179-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="2a179-143">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="2a179-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2a179-144">Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="2a179-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="2a179-145">Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="2a179-145">Route constraints</span></span>

<span data-ttu-id="2a179-146">Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="2a179-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="2a179-147">Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur überein, wenn:</span><span class="sxs-lookup"><span data-stu-id="2a179-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="2a179-148">Ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="2a179-149">Das `Id`-Segment eine Ganzzahl (`int`) ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="2a179-150">Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2a179-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="2a179-151">Informationen zu den Routeneinschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="2a179-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="2a179-152">Constraint</span><span class="sxs-lookup"><span data-stu-id="2a179-152">Constraint</span></span> | <span data-ttu-id="2a179-153">Beispiel</span><span class="sxs-lookup"><span data-stu-id="2a179-153">Example</span></span>           | <span data-ttu-id="2a179-154">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="2a179-154">Example Matches</span></span>                                                                  | <span data-ttu-id="2a179-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="2a179-155">Invariant</span></span><br><span data-ttu-id="2a179-156">Kultur</span><span class="sxs-lookup"><span data-stu-id="2a179-156">culture</span></span><br><span data-ttu-id="2a179-157">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="2a179-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="2a179-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="2a179-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="2a179-159">Nein</span><span class="sxs-lookup"><span data-stu-id="2a179-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="2a179-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2a179-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="2a179-161">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="2a179-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2a179-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="2a179-163">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="2a179-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2a179-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2a179-165">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="2a179-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2a179-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2a179-167">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="2a179-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2a179-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2a179-169">Nein</span><span class="sxs-lookup"><span data-stu-id="2a179-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="2a179-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2a179-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2a179-171">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="2a179-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2a179-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2a179-173">Ja</span><span class="sxs-lookup"><span data-stu-id="2a179-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="2a179-174">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="2a179-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2a179-175">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="2a179-176">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="2a179-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="2a179-177">In Blazor Server-Apps ist die Standardroute in *_Host.cshtml* `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="2a179-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="2a179-178">Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="2a179-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="2a179-179">Eine Blazor-App gibt eine Antwort *404 – Nicht gefunden* für eine statische Datei zurück, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="2a179-180">Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host.cshtml* mit der folgenden Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="2a179-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="2a179-181">Die `"/{**path}"`-Vorlage enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2a179-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="2a179-182">*catch-all*-Syntax mit doppelten Sternchen (`**`) zur Erfassung des Pfades über mehrere Ordnergrenzen hinweg ohne Codierung von Schrägstrichen (`/`).</span><span class="sxs-lookup"><span data-stu-id="2a179-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="2a179-183">Name des `path`-Routenparameters.</span><span class="sxs-lookup"><span data-stu-id="2a179-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="2a179-184">Die *catch-all*-Parametersyntax (`*`/`**`) wird **nicht** in Razor-Komponenten ( *.razor*) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2a179-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="2a179-185">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="2a179-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="2a179-186">NavLink-Komponente</span><span class="sxs-lookup"><span data-stu-id="2a179-186">NavLink component</span></span>

<span data-ttu-id="2a179-187">Verwenden Sie bei der Erstellung von Navigationslinks eine `NavLink`-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="2a179-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="2a179-188">Eine `NavLink`-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2a179-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="2a179-189">Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="2a179-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="2a179-190">Die folgende `NavMenu`-Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie `NavLink`-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2a179-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="2a179-191">Es gibt zwei `NavLinkMatch`-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="2a179-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="2a179-192">`NavLinkMatch.All` &ndash; `NavLink` ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2a179-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="2a179-193">`NavLinkMatch.Prefix` (*Standard*) &ndash; `NavLink` ist aktiv, wenn ein Präfix der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2a179-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="2a179-194">Im vorherigen Beispiel stimmt die Startseite `NavLink` `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="2a179-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="2a179-195">Die zweite `NavLink`-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `MyComponent`-Präfix aufruft (z. B. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="2a179-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="2a179-196">Zusätzliche `NavLink`-Komponentenattribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="2a179-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="2a179-197">Im folgenden Beispiel schließt die `NavLink`-Komponente das `target`-Attribut ein:</span><span class="sxs-lookup"><span data-stu-id="2a179-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="2a179-198">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="2a179-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="2a179-199">Hilfsprogramme für URI und Navigationszustand</span><span class="sxs-lookup"><span data-stu-id="2a179-199">URI and navigation state helpers</span></span>

<span data-ttu-id="2a179-200">Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um mit URIs und Navigationselementen in C#-Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="2a179-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="2a179-201">`NavigationManager` stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="2a179-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="2a179-202">Member</span><span class="sxs-lookup"><span data-stu-id="2a179-202">Member</span></span> | <span data-ttu-id="2a179-203">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="2a179-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2a179-204">URI</span><span class="sxs-lookup"><span data-stu-id="2a179-204">Uri</span></span> | <span data-ttu-id="2a179-205">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="2a179-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="2a179-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="2a179-206">BaseUri</span></span> | <span data-ttu-id="2a179-207">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2a179-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="2a179-208">In der Regel entspricht `BaseUri` dem `href`-Attribut im `<base>`-Element des Dokuments in *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="2a179-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="2a179-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="2a179-209">NavigateTo</span></span> | <span data-ttu-id="2a179-210">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="2a179-210">Navigates to the specified URI.</span></span> <span data-ttu-id="2a179-211">Bei `forceLoad` lautet der Wert `true`:</span><span class="sxs-lookup"><span data-stu-id="2a179-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="2a179-212">Clientseitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="2a179-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="2a179-213">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</span><span class="sxs-lookup"><span data-stu-id="2a179-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="2a179-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="2a179-214">LocationChanged</span></span> | <span data-ttu-id="2a179-215">Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat.</span><span class="sxs-lookup"><span data-stu-id="2a179-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="2a179-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="2a179-216">ToAbsoluteUri</span></span> | <span data-ttu-id="2a179-217">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="2a179-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="2a179-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="2a179-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="2a179-219">Wenn ein Basis-URI (z. B. ein URI, der zuvor von `GetBaseUri` zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert.</span><span class="sxs-lookup"><span data-stu-id="2a179-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="2a179-220">Die folgende Komponente navigiert zu der `Counter`-Komponente der App, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="2a179-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="2a179-221">Die folgende Komponente verarbeitet ein Speicherortwechselereignis.</span><span class="sxs-lookup"><span data-stu-id="2a179-221">The following component handles a location changed event.</span></span> <span data-ttu-id="2a179-222">Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2a179-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="2a179-223">Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2a179-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="2a179-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> stellt die folgenden Informationen zum Ereignis bereit:</span><span class="sxs-lookup"><span data-stu-id="2a179-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="2a179-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> entspricht der URL des neuen Speicherorts.</span><span class="sxs-lookup"><span data-stu-id="2a179-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="2a179-226">Wenn <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> den Wert `true` aufweist, wird die Navigation des Browsers von Blazor abgefangen.</span><span class="sxs-lookup"><span data-stu-id="2a179-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="2a179-227">Wenn der Wert `false` vorliegt, wurde die Navigation von [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="2a179-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="2a179-228">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="2a179-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
