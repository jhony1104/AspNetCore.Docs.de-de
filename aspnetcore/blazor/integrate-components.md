---
title: Integrieren von ASP.net Core Razor-Komponenten in Razor Pages und MVC-apps
author: guardrex
description: Erfahren Sie mehr über Daten Bindungs Szenarien für Komponenten und DOM-Elemente in Blazor-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453211"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c0d2d-103">Integrieren von ASP.net Core Razor-Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="c0d2d-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c0d2d-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c0d2d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c0d2d-105">Razor-Komponenten können in Razor Pages-und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="c0d2d-106">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab vorgerendert werden.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="c0d2d-107">Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten</span><span class="sxs-lookup"><span data-stu-id="c0d2d-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="c0d2d-108">Eine vorhandene Razor Pages-oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="c0d2d-109">In der Layoutdatei der APP ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="c0d2d-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="c0d2d-110">Fügen Sie dem `<head>`-Element das folgende `<base>`-Tag hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="c0d2d-111">Beim `href` Wert (dem *App-Basispfad*) im vorhergehenden Beispiel wird davon ausgegangen, dass sich die APP im Stamm-URL-Pfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="c0d2d-112">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path> Artikels.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="c0d2d-113">Die Datei " *_Layout. cshtml* " befindet sich im Ordner " *pages/Shared* " in einer Razor Pages-APP oder in *Sichten/freigegebenen* Ordnern in einer MVC-app.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="c0d2d-114">Fügen Sie unmittelbar vor dem schließenden `</body>` Tag ein `<script>`-Tag für das Skript " *blazor. Server. js* " hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="c0d2d-115">Das-Framework fügt der APP das Skript " *blazor. Server. js* " hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="c0d2d-116">Es ist nicht erforderlich, das Skript manuell der APP hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="c0d2d-117">Fügen Sie dem Stamm Ordner des Projekts eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu (ändern Sie den letzten Namespace, `MyAppNamespace`, in den Namespace der APP):</span><span class="sxs-lookup"><span data-stu-id="c0d2d-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="c0d2d-118">Registrieren Sie in `Startup.ConfigureServices`den blazor-Server Dienst:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="c0d2d-119">Fügen Sie in `Startup.Configure`den blazor-Hub-Endpunkt `app.UseEndpoints`hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="c0d2d-120">Integrieren von Komponenten in beliebige Seiten oder Ansichten.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-120">Integrate components into any page or view.</span></span> <span data-ttu-id="c0d2d-121">Weitere Informationen finden Sie im Abschnitt [Rendering Components from a page or View](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="c0d2d-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="c0d2d-122">Verwenden von Routing fähigen Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="c0d2d-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="c0d2d-123">*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten, die direkt von Benutzer Anforderungen aus Routing fähig sind.*</span><span class="sxs-lookup"><span data-stu-id="c0d2d-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="c0d2d-124">So unterstützen Sie Routing fähige Razor-Komponenten in Razor Pages-apps:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="c0d2d-125">Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="c0d2d-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="c0d2d-126">Fügen Sie dem Projektstamm eine *app. Razor* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-126">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c0d2d-127">Fügen Sie dem Ordner *pages* eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c0d2d-128">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="c0d2d-129">Fügen Sie eine Route mit niedriger Priorität für die *_Host. cshtml* -Seite zur Endpunkt Konfiguration in `Startup.Configure`hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="c0d2d-130">Fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-130">Add routable components to the app.</span></span> <span data-ttu-id="c0d2d-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="c0d2d-132">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="c0d2d-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="c0d2d-133">Verwenden von Routing fähigen Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="c0d2d-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="c0d2d-134">*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten, die direkt von Benutzer Anforderungen aus Routing fähig sind.*</span><span class="sxs-lookup"><span data-stu-id="c0d2d-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="c0d2d-135">So unterstützen Sie Routing fähige Razor-Komponenten in MVC-apps:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="c0d2d-136">Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="c0d2d-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="c0d2d-137">Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c0d2d-138">Fügen Sie dem Ordner " *views/Home* " eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c0d2d-139">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="c0d2d-140">Fügen Sie dem Home-Controller eine Aktion hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="c0d2d-141">Fügen Sie eine Route mit niedriger Priorität für die Controller Aktion hinzu, die die Ansicht *_Host. cshtml* an die Endpunkt Konfiguration in `Startup.Configure`zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="c0d2d-142">Erstellen Sie einen Ordner *pages* , und fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="c0d2d-143">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="c0d2d-144">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="c0d2d-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="c0d2d-145">Komponentennamespaces</span><span class="sxs-lookup"><span data-stu-id="c0d2d-145">Component namespaces</span></span>

<span data-ttu-id="c0d2d-146">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace, der den Ordner darstellt, entweder der Seite/Ansicht oder der Datei " *_ViewImports. cshtml* " hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c0d2d-147">Siehe folgendes Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-147">In the following example:</span></span>

* <span data-ttu-id="c0d2d-148">Ändern Sie `MyAppNamespace` in den Namespace der app.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="c0d2d-149">Wenn ein Ordner mit dem Namen *Components* nicht zum Speichern der Komponenten verwendet wird, ändern Sie `Components` in den Ordner, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="c0d2d-150">Die Datei " *_ViewImports. cshtml* " befindet sich im Ordner " *pages* " einer Razor Pages-APP oder im Ordner " *views* " einer MVC-app.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="c0d2d-151">Weitere Informationen finden Sie unter <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="c0d2d-152">Rendering von Komponenten aus einer Seite oder Ansicht</span><span class="sxs-lookup"><span data-stu-id="c0d2d-152">Render components from a page or view</span></span>

<span data-ttu-id="c0d2d-153">*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten zu Seiten oder Sichten, bei denen die Komponenten nicht direkt von Benutzer Anforderungen aus Routing fähig sind.*</span><span class="sxs-lookup"><span data-stu-id="c0d2d-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="c0d2d-154">Verwenden Sie das `Component`-taghilfsprogramm, um eine Komponente aus einer Seite oder Sicht zu Rendering:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="c0d2d-155">Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="c0d2d-156">Beispielsweise können Sie einen Wert für `IncrementAmount` angeben, da der Typ der `IncrementAmount` ein `int`ist, bei dem es sich um einen primitiven Typ handelt, der vom JSON-Serialisierungsprogramm unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="c0d2d-157">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c0d2d-158">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="c0d2d-159">Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c0d2d-160">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="c0d2d-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="c0d2d-161">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c0d2d-162">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="c0d2d-163">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c0d2d-164">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-164">Output from the component isn't included.</span></span> <span data-ttu-id="c0d2d-165">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="c0d2d-166">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c0d2d-167">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c0d2d-168">Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="c0d2d-169">Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c0d2d-170">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c0d2d-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="c0d2d-171">Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `Component`-taghilfsprogramm finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="c0d2d-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
