---
title: Razor-Komponenten, die routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die NavLink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/routing
ms.openlocfilehash: ef82fa7e0d571979a43fd8ce712bf4f22c06f9a7
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515549"
---
# <a name="razor-components-routing"></a><span data-ttu-id="5c76f-103">Razor-Komponenten, die routing</span><span class="sxs-lookup"><span data-stu-id="5c76f-103">Razor Components routing</span></span>

<span data-ttu-id="5c76f-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c76f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5c76f-105">Erfahren Sie, wie Sie Anforderungen in apps und über die NavLink-Komponente weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="5c76f-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="5c76f-106">ASP.NET Core-Endpunkt-routing-integration</span><span class="sxs-lookup"><span data-stu-id="5c76f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="5c76f-107">Razor-Komponenten sind in integriert [routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="5c76f-107">Razor Components are integrated into [ASP.NET Core routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="5c76f-108">Eine ASP.NET Core-app ist so konfiguriert, dass die Annahme eingehender Verbindungen für interaktive Razor-Komponenten mit `MapComponentHub<TComponent>` in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5c76f-108">An ASP.NET Core app is configured to accept incoming connections for interactive Razor Components with `MapComponentHub<TComponent>` in `Startup.Configure`.</span></span> `MapComponentHub` <span data-ttu-id="5c76f-109">Gibt an, dass die Stammkomponente `App` gerendert werden soll, in ein DOM-Element, das den Selektor übereinstimmende `app`:</span><span class="sxs-lookup"><span data-stu-id="5c76f-109">specifies that the root component `App` should be rendered within a DOM element matching the selector `app`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a><span data-ttu-id="5c76f-110">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="5c76f-110">Route templates</span></span>

<span data-ttu-id="5c76f-111">Die `<Router>` Komponente ermöglicht die Weiterleitung, und eine routenvorlage wird bereitgestellt, um jede Komponente zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="5c76f-111">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="5c76f-112">Die `<Router>` Komponente angezeigt wird, der *Components/App.razor* Datei:</span><span class="sxs-lookup"><span data-stu-id="5c76f-112">The `<Router>` component appears in the *Components/App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="5c76f-113">Wenn eine *.razor* oder *cshtml* -Datei mit ein `@page` Richtlinie wird kompiliert, wird die generierte Klasse wird eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> die routenvorlage angeben.</span><span class="sxs-lookup"><span data-stu-id="5c76f-113">When a *.razor* or *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="5c76f-114">Zur Laufzeit sucht der Router Komponentenklassen mit einem `RouteAttribute` und rendert, welche Komponente eine routenvorlage verfügt, die mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5c76f-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="5c76f-115">Mehrere routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c76f-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="5c76f-116">Die folgende Komponente antwortet auf Anfragen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="5c76f-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` <span data-ttu-id="5c76f-117">unterstützt das Festlegen einer fallback Komponente für das Rendering, wenn eine angeforderte Route nicht aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="5c76f-117">supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="5c76f-118">Aktivieren Sie dieses Szenario durch Festlegen der `FallbackComponent` Parameter, um den Typ der fallback Komponentenklasse.</span><span class="sxs-lookup"><span data-stu-id="5c76f-118">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="5c76f-119">Im folgenden Beispiel wird eine Komponente, die in definierten *Pages/MyFallbackRazorComponent.cshtml* als fallback für die Komponente eine `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="5c76f-119">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="5c76f-120">Um Routen ordnungsgemäß generieren zu können, muss die app enthalten eine `<base>` -Tag in die *wwwroot/Index.HTML* -Datei mit den app-Basispfad angegeben, der `href` Attribut (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="5c76f-120">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="5c76f-121">Weitere Informationen finden Sie unter <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="5c76f-121">For more information, see <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="5c76f-122">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="5c76f-122">Route parameters</span></span>

<span data-ttu-id="5c76f-123">Der Router verwendet Routenparameter zum Auffüllen der entsprechenden Komponentenparameter mit dem gleichen Namen (Groß-/Kleinschreibung):</span><span class="sxs-lookup"><span data-stu-id="5c76f-123">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="5c76f-124">Optionale Parameter werden nicht unterstützt, daher werden zwei `@page` Anweisungen sind im obigen Beispiel angewendet.</span><span class="sxs-lookup"><span data-stu-id="5c76f-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="5c76f-125">Die erste ermöglicht die Navigation zu der Komponente ohne Parameter.</span><span class="sxs-lookup"><span data-stu-id="5c76f-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="5c76f-126">Die zweite `@page` Direktive nimmt die `{text}` Routenparameter und weist den Wert der `Text` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5c76f-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="5c76f-127">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="5c76f-127">Route constraints</span></span>

<span data-ttu-id="5c76f-128">Eine routeneinschränkung erzwingt typübereinstimmung auf einem routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="5c76f-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="5c76f-129">Im folgenden Beispiel entspricht die Route für die Benutzer-Komponente nur, wenn:</span><span class="sxs-lookup"><span data-stu-id="5c76f-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="5c76f-130">Ein `Id` routensegment auf die Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5c76f-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="5c76f-131">Die `Id` -Segment ist eine ganze Zahl (`int`).</span><span class="sxs-lookup"><span data-stu-id="5c76f-131">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

<span data-ttu-id="5c76f-132">Die routeneinschränkungen, die in der folgenden Tabelle dargestellt sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5c76f-132">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="5c76f-133">Die routeneinschränkungen, die mit der invarianten Kultur entsprechen, finden Sie in der Warnung unterhalb der Tabelle Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="5c76f-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="5c76f-134">Constraint</span><span class="sxs-lookup"><span data-stu-id="5c76f-134">Constraint</span></span> | <span data-ttu-id="5c76f-135">Beispiel</span><span class="sxs-lookup"><span data-stu-id="5c76f-135">Example</span></span>           | <span data-ttu-id="5c76f-136">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="5c76f-136">Example Matches</span></span>                                                                  | <span data-ttu-id="5c76f-137">Invariante</span><span class="sxs-lookup"><span data-stu-id="5c76f-137">Invariant</span></span><br><span data-ttu-id="5c76f-138">Kultur</span><span class="sxs-lookup"><span data-stu-id="5c76f-138">culture</span></span><br><span data-ttu-id="5c76f-139">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="5c76f-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`<span data-ttu-id="5c76f-140">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-140">,</span></span> `FALSE`                                                                  | <span data-ttu-id="5c76f-141">Nein</span><span class="sxs-lookup"><span data-stu-id="5c76f-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`<span data-ttu-id="5c76f-142">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-142">,</span></span> `2016-12-31 7:32pm`                                                | <span data-ttu-id="5c76f-143">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | `49.99`<span data-ttu-id="5c76f-144">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-144">,</span></span> `-1,000.01`                                                             | <span data-ttu-id="5c76f-145">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | `1.234`<span data-ttu-id="5c76f-146">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-146">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="5c76f-147">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | `1.234`<span data-ttu-id="5c76f-148">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-148">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="5c76f-149">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`<span data-ttu-id="5c76f-150">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-150">,</span></span> `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | <span data-ttu-id="5c76f-151">Nein</span><span class="sxs-lookup"><span data-stu-id="5c76f-151">No</span></span>                               |
| `int`      | `{id:int}`        | `123456789`<span data-ttu-id="5c76f-152">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-152">,</span></span> `-123456789`                                                        | <span data-ttu-id="5c76f-153">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | `123456789`<span data-ttu-id="5c76f-154">,</span><span class="sxs-lookup"><span data-stu-id="5c76f-154">,</span></span> `-123456789`                                                        | <span data-ttu-id="5c76f-155">Ja</span><span class="sxs-lookup"><span data-stu-id="5c76f-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="5c76f-156">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c76f-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="5c76f-157">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="5c76f-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="5c76f-158">NavLink-Komponente</span><span class="sxs-lookup"><span data-stu-id="5c76f-158">NavLink component</span></span>

<span data-ttu-id="5c76f-159">Verwenden Sie eine Komponente NavLink anstelle von HTML- `<a>` Elemente beim Erstellen von Navigationslinks.</span><span class="sxs-lookup"><span data-stu-id="5c76f-159">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="5c76f-160">Eine Komponente NavLink verhält sich wie ein `<a>` -Element, mit der Ausnahme umgeschaltet ein `active` CSS-Klasse abhängig davon, ob die `href` mit der aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5c76f-160">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="5c76f-161">Die `active` Klasse hilft, einen Benutzer zu verstehen, welche Seite zur aktiven Seite für die Navigationslinks angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5c76f-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="5c76f-162">Die folgende NavMenu-Komponente erstellt ein [Bootstrap](https://getbootstrap.com/docs/) Navigationsleiste, die zeigt, wie NavLink Komponenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="5c76f-162">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="5c76f-163">Es gibt zwei `NavLinkMatch` Optionen:</span><span class="sxs-lookup"><span data-stu-id="5c76f-163">There are two `NavLinkMatch` options:</span></span>

* `NavLinkMatch.All` <span data-ttu-id="5c76f-164">&ndash; Gibt an, dass die NavLink aktiv sein sollte, wenn es sich um die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5c76f-164">&ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* `NavLinkMatch.Prefix` <span data-ttu-id="5c76f-165">&ndash; Gibt an, dass die NavLink aktiv sein sollte, wenn es sich um Präfix des aktuellen URLs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5c76f-165">&ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="5c76f-166">Im vorherigen Beispiel ist die Startseite NavLink (`href=""`) entspricht allen URLs und erhält immer das `active` CSS-Klasse.</span><span class="sxs-lookup"><span data-stu-id="5c76f-166">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="5c76f-167">Die zweite NavLink erhält nur die `active` Klasse, wenn der Benutzer die Komponente Blazor Route besucht (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="5c76f-167">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>
