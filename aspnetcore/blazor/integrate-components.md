---
title: Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218933"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="07892-103">Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps</span><span class="sxs-lookup"><span data-stu-id="07892-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="07892-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="07892-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="07892-105">Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="07892-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="07892-106">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="07892-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="07892-107">Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten</span><span class="sxs-lookup"><span data-stu-id="07892-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="07892-108">Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="07892-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="07892-109">In der Layoutdatei der App ( *_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="07892-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="07892-110">Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="07892-111">Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="07892-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="07892-112">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path>-Artikels.</span><span class="sxs-lookup"><span data-stu-id="07892-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="07892-113">Die Datei *_Layout.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages/Shared* oder bei MVC-Apps im Ordner *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="07892-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="07892-114">Fügen Sie ein `<script>`-Tag für das Skript *blazor.server.js* direkt vor dem schließenden `</body>`-Tag ein:</span><span class="sxs-lookup"><span data-stu-id="07892-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="07892-115">Das Framework fügt das Skript *blazor.server.js* zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="07892-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="07892-116">Das Skript muss nicht manuell zur App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="07892-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="07892-117">Fügen Sie eine *_Imports.razor*-Datei zum Stammordner des Projekts mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):</span><span class="sxs-lookup"><span data-stu-id="07892-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="07892-118">Registrieren Sie den Blazor Server-Dienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07892-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="07892-119">Fügen Sie in `Startup.Configure` den Blazor Hub-Endpunkt zu `app.UseEndpoints` hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="07892-120">Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="07892-120">Integrate components into any page or view.</span></span> <span data-ttu-id="07892-121">Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="07892-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="07892-122">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="07892-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="07892-123">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="07892-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="07892-124">So unterstützen Sie routingfähige Razor-Komponenten in Razor Pages-Apps:</span><span class="sxs-lookup"><span data-stu-id="07892-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="07892-125">Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views).</span><span class="sxs-lookup"><span data-stu-id="07892-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="07892-126">Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="07892-127">Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Pages* hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="07892-128">Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="07892-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="07892-129">Fügen Sie eine Route mit niedriger Priorität für die Seite *_Host.cshtml* zur Endpunktkonfiguration in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="07892-130">Fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="07892-130">Add routable components to the app.</span></span> <span data-ttu-id="07892-131">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="07892-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="07892-132">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="07892-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="07892-133">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="07892-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="07892-134">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="07892-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="07892-135">So unterstützen Sie routingfähige Razor-Komponenten in MVC-Apps:</span><span class="sxs-lookup"><span data-stu-id="07892-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="07892-136">Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views).</span><span class="sxs-lookup"><span data-stu-id="07892-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="07892-137">Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="07892-138">Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Views/Home* hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="07892-139">Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="07892-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="07892-140">Fügen Sie eine Aktion zum Home-Controller hinzu:</span><span class="sxs-lookup"><span data-stu-id="07892-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="07892-141">Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht *_Host.cshtml* an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="07892-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="07892-142">Erstellen Sie einen *Pages*-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="07892-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="07892-143">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="07892-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="07892-144">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="07892-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="07892-145">Komponentennamespaces</span><span class="sxs-lookup"><span data-stu-id="07892-145">Component namespaces</span></span>

<span data-ttu-id="07892-146">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace zur Seite/Ansicht oder zur Datei *_ViewImports.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="07892-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="07892-147">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="07892-147">In the following example:</span></span>

* <span data-ttu-id="07892-148">Ändern Sie `MyAppNamespace` in den Namespace der App.</span><span class="sxs-lookup"><span data-stu-id="07892-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="07892-149">Wenn die Komponenten nicht in einem Ordner namens *Components* enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="07892-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="07892-150">Die Datei *_ViewImports.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages* oder bei MVC-Apps im Ordner *Views*.</span><span class="sxs-lookup"><span data-stu-id="07892-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="07892-151">Weitere Informationen finden Sie unter <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="07892-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="07892-152">Rendern von Komponenten einer Seite oder Ansicht</span><span class="sxs-lookup"><span data-stu-id="07892-152">Render components from a page or view</span></span>

<span data-ttu-id="07892-153">*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="07892-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="07892-154">Verwenden Sie das [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) zum Rendern einer Komponente einer Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="07892-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="07892-155">Weitere Informationen über das Rendern von Komponenten, den Komponentenzustand und das `Component`-Taghilfsprogramm finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="07892-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
