---
title: Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383972"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="4c082-103">Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps</span><span class="sxs-lookup"><span data-stu-id="4c082-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="4c082-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4c082-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4c082-105">Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="4c082-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="4c082-106">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="4c082-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="4c082-107">Nachdem Sie [die App vorbereitet haben](#prepare-the-app), verwenden Sie die Anleitungen in den folgenden Abschnitten, abhängig von den Anforderungen der App:</span><span class="sxs-lookup"><span data-stu-id="4c082-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="4c082-108">Routingfähige Komponenten &ndash; Für Komponenten, die über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="4c082-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="4c082-109">Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4c082-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="4c082-110">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="4c082-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="4c082-111">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="4c082-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="4c082-112">[Rendern von Komponenten aus einer Seite oder Ansicht](#render-components-from-a-page-or-view) &ndash; Für Komponenten, die über Benutzeranforderungen nicht direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="4c082-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="4c082-113">Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.</span><span class="sxs-lookup"><span data-stu-id="4c082-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="4c082-114">Vorbereiten der App</span><span class="sxs-lookup"><span data-stu-id="4c082-114">Prepare the app</span></span>

<span data-ttu-id="4c082-115">Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="4c082-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="4c082-116">In der Layoutdatei der App ( *_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="4c082-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="4c082-117">Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="4c082-118">Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="4c082-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="4c082-119">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path>-Artikels.</span><span class="sxs-lookup"><span data-stu-id="4c082-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="4c082-120">Die Datei *_Layout.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages/Shared* oder bei MVC-Apps im Ordner *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4c082-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="4c082-121">Fügen Sie ein `<script>`-Tag für das Skript *blazor.server.js* direkt vor dem schließenden `</body>`-Tag ein:</span><span class="sxs-lookup"><span data-stu-id="4c082-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="4c082-122">Das Framework fügt das Skript *blazor.server.js* zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="4c082-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="4c082-123">Das Skript muss nicht manuell zur App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="4c082-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="4c082-124">Fügen Sie eine *_Imports.razor*-Datei zum Stammordner des Projekts mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):</span><span class="sxs-lookup"><span data-stu-id="4c082-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="4c082-125">Registrieren Sie den Blazor Server-Dienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4c082-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="4c082-126">Fügen Sie den Blazor Hub-Endpunkt in `Startup.Configure` zu `app.UseEndpoints` hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="4c082-127">Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="4c082-127">Integrate components into any page or view.</span></span> <span data-ttu-id="4c082-128">Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="4c082-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="4c082-129">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="4c082-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="4c082-130">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="4c082-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4c082-131">So unterstützen Sie routingfähige Razor-Komponenten in Razor Pages-Apps:</span><span class="sxs-lookup"><span data-stu-id="4c082-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="4c082-132">Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="4c082-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="4c082-133">Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="4c082-134">Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Pages* hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4c082-135">Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="4c082-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="4c082-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="4c082-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4c082-137">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="4c082-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4c082-138">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="4c082-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="4c082-139">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="4c082-139">Render Mode</span></span> | <span data-ttu-id="4c082-140">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4c082-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4c082-141">Rendert die `App`-Komponente in statischem HTML und schließt einen Marker für eine Blazor Server-App ein.</span><span class="sxs-lookup"><span data-stu-id="4c082-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c082-142">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4c082-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4c082-143">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="4c082-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c082-144">Die Ausgabe der `App`-Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="4c082-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="4c082-145">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4c082-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4c082-146">Rendert die `App`-Komponente in statischem HTML.</span><span class="sxs-lookup"><span data-stu-id="4c082-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="4c082-147">Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4c082-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4c082-148">Fügen Sie eine Route mit niedriger Priorität für die Seite *_Host.cshtml* zur Endpunktkonfiguration in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="4c082-149">Fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="4c082-149">Add routable components to the app.</span></span> <span data-ttu-id="4c082-150">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4c082-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4c082-151">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="4c082-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="4c082-152">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="4c082-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="4c082-153">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="4c082-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4c082-154">So unterstützen Sie routingfähige Razor-Komponenten in MVC-Apps:</span><span class="sxs-lookup"><span data-stu-id="4c082-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="4c082-155">Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="4c082-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="4c082-156">Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="4c082-157">Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Views/Home* hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4c082-158">Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="4c082-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="4c082-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="4c082-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4c082-160">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="4c082-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4c082-161">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="4c082-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="4c082-162">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="4c082-162">Render Mode</span></span> | <span data-ttu-id="4c082-163">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4c082-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4c082-164">Rendert die `App`-Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="4c082-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c082-165">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4c082-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4c082-166">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="4c082-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c082-167">Die Ausgabe der `App`-Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="4c082-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="4c082-168">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4c082-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4c082-169">Rendert die `App`-Komponente in statischem HTML.</span><span class="sxs-lookup"><span data-stu-id="4c082-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="4c082-170">Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4c082-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4c082-171">Fügen Sie eine Aktion zum Home-Controller hinzu:</span><span class="sxs-lookup"><span data-stu-id="4c082-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="4c082-172">Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht *_Host.cshtml* an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="4c082-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="4c082-173">Erstellen Sie einen *Pages*-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="4c082-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="4c082-174">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4c082-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4c082-175">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="4c082-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="4c082-176">Rendern von Komponenten einer Seite oder Ansicht</span><span class="sxs-lookup"><span data-stu-id="4c082-176">Render components from a page or view</span></span>

<span data-ttu-id="4c082-177">*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="4c082-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="4c082-178">Verwenden Sie das [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) zum Rendern einer Komponente einer Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="4c082-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="4c082-179">Weitere Informationen über das Rendern von Komponenten, den Komponentenzustand und das `Component`-Taghilfsprogramm finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="4c082-179">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a><span data-ttu-id="4c082-180">Komponentennamespaces</span><span class="sxs-lookup"><span data-stu-id="4c082-180">Component namespaces</span></span>

<span data-ttu-id="4c082-181">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace zur Seite/Ansicht oder zur Datei *_ViewImports.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4c082-181">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4c082-182">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4c082-182">In the following example:</span></span>

* <span data-ttu-id="4c082-183">Ändern Sie `MyAppNamespace` in den Namespace der App.</span><span class="sxs-lookup"><span data-stu-id="4c082-183">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="4c082-184">Wenn die Komponenten nicht in einem Ordner namens *Components* enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="4c082-184">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="4c082-185">Die Datei *_ViewImports.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages* oder bei MVC-Apps im Ordner *Views*.</span><span class="sxs-lookup"><span data-stu-id="4c082-185">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="4c082-186">Weitere Informationen finden Sie unter <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="4c082-186">For more information, see <xref:blazor/components#import-components>.</span></span>
