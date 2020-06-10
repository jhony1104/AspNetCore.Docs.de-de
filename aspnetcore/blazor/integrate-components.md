---
title: "title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 97515ec519c4bedb0478f510ec9ed739b5d76e4f
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84105245"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="16b7f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-103">'Blazor'</span></span>

<span data-ttu-id="16b7f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-104">'Identity'</span></span>

<span data-ttu-id="16b7f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-105">'Let's Encrypt'</span></span> <span data-ttu-id="16b7f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-106">'Razor'</span></span>

<span data-ttu-id="16b7f-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-107">'SignalR' uid:</span></span>

* <span data-ttu-id="16b7f-108">Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps</span><span class="sxs-lookup"><span data-stu-id="16b7f-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span> <span data-ttu-id="16b7f-109">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="16b7f-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>
  * Razor<span data-ttu-id="16b7f-110">-Komponenten können in Razor Pages- und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="16b7f-110"> components can be integrated into Razor Pages and MVC apps.</span></span>
  * <span data-ttu-id="16b7f-111">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="16b7f-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>
* <span data-ttu-id="16b7f-112">Nachdem Sie [die App vorbereitet haben](#prepare-the-app), verwenden Sie die Anleitungen in den folgenden Abschnitten, abhängig von den Anforderungen der App:</span><span class="sxs-lookup"><span data-stu-id="16b7f-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span> <span data-ttu-id="16b7f-113">Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="16b7f-113">Routable components: For components that are directly routable from user requests.</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="16b7f-114">Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="16b7f-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

<span data-ttu-id="16b7f-115">[Verwenden routingfähiger Komponenten in einer Razor Pages-App](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="16b7f-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>

1. [<span data-ttu-id="16b7f-116">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="16b7f-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)

   * <span data-ttu-id="16b7f-117">[Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="16b7f-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="16b7f-118">Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.</span><span class="sxs-lookup"><span data-stu-id="16b7f-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span> <span data-ttu-id="16b7f-119">Vorbereiten der App</span><span class="sxs-lookup"><span data-stu-id="16b7f-119">Prepare the app</span></span>

     <span data-ttu-id="16b7f-120">Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="16b7f-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

   * <span data-ttu-id="16b7f-121">In der Layoutdatei der App ( *_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="16b7f-121">In the app's layout file (*_Layout.cshtml*):</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="16b7f-122">Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="16b7f-122">Add the following `<base>` tag to the `<head>` element:</span></span> <span data-ttu-id="16b7f-123">Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="16b7f-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span>

1. <span data-ttu-id="16b7f-124">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path>-Artikels.</span><span class="sxs-lookup"><span data-stu-id="16b7f-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

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

1. <span data-ttu-id="16b7f-125">Die Datei *_Layout.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages/Shared* und bei MVC-Apps im Ordner *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="16b7f-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="16b7f-126">Fügen Sie ein `<script>`-Tag für das Skript *blazor.server.js* direkt vor dem schließenden `</body>`-Tag ein:</span><span class="sxs-lookup"><span data-stu-id="16b7f-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="16b7f-127">Das Framework fügt das Skript *blazor.server.js* zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="16b7f-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="16b7f-128">Das Skript muss nicht manuell zur App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="16b7f-128">There's no need to manually add the script to the app.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="16b7f-129">Fügen Sie eine *_Imports.razor*-Datei zum Stammordner des Projekts mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):</span><span class="sxs-lookup"><span data-stu-id="16b7f-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

<span data-ttu-id="16b7f-130">Registrieren Sie den Blazor Server-Dienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="16b7f-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

<span data-ttu-id="16b7f-131">Fügen Sie in `Startup.Configure` den Blazor Hub-Endpunkt zu `app.UseEndpoints` hinzu:</span><span class="sxs-lookup"><span data-stu-id="16b7f-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

1. <span data-ttu-id="16b7f-132">Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="16b7f-132">Integrate components into any page or view.</span></span>

1. <span data-ttu-id="16b7f-133">Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="16b7f-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

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

1. <span data-ttu-id="16b7f-134">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="16b7f-134">Use routable components in a Razor Pages app</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="16b7f-135">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="16b7f-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

   <span data-ttu-id="16b7f-136">So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in Razor Pages-Apps ein:</span><span class="sxs-lookup"><span data-stu-id="16b7f-136">To support routable Razor components in Razor Pages apps:</span></span>

   * <span data-ttu-id="16b7f-137">Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="16b7f-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>
   * <span data-ttu-id="16b7f-138">Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="16b7f-138">Add an *App.razor* file to the project root with the following content:</span></span>

   | <span data-ttu-id="16b7f-139">Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Pages* hinzu:</span><span class="sxs-lookup"><span data-stu-id="16b7f-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span> | <span data-ttu-id="16b7f-140">Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="16b7f-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="16b7f-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="16b7f-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span> <span data-ttu-id="16b7f-142">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="16b7f-142">Is prerendered into the page.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="16b7f-143">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="16b7f-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span> <span data-ttu-id="16b7f-144">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="16b7f-144">Render Mode</span></span> <span data-ttu-id="16b7f-145">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="16b7f-145">Description</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="16b7f-146">title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span> |

   <span data-ttu-id="16b7f-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

1. <span data-ttu-id="16b7f-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-148">'Blazor'</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="16b7f-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-149">'Identity'</span></span> <span data-ttu-id="16b7f-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-150">'Let's Encrypt'</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="16b7f-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-151">'Razor'</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="16b7f-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-152">'SignalR' uid:</span></span>

<span data-ttu-id="16b7f-153">title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>

<span data-ttu-id="16b7f-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

1. <span data-ttu-id="16b7f-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-155">'Blazor'</span></span>

1. <span data-ttu-id="16b7f-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-156">'Identity'</span></span>

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

1. <span data-ttu-id="16b7f-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-157">'Let's Encrypt'</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="16b7f-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-158">'Razor'</span></span>
   
   <span data-ttu-id="16b7f-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-159">'SignalR' uid:</span></span>

   * <span data-ttu-id="16b7f-160">title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
   * <span data-ttu-id="16b7f-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

   | <span data-ttu-id="16b7f-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-162">'Blazor'</span></span> | <span data-ttu-id="16b7f-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-163">'Identity'</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="16b7f-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-164">'Let's Encrypt'</span></span> <span data-ttu-id="16b7f-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-165">'Razor'</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="16b7f-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-166">'SignalR' uid:</span></span> <span data-ttu-id="16b7f-167">------ | --- title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span> <span data-ttu-id="16b7f-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="16b7f-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-169">'Blazor'</span></span> |

   <span data-ttu-id="16b7f-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-170">'Identity'</span></span>

1. <span data-ttu-id="16b7f-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-171">'Let's Encrypt'</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="16b7f-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-172">'Razor'</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="16b7f-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-173">'SignalR' uid:</span></span> <span data-ttu-id="16b7f-174">title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="16b7f-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="16b7f-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-176">'Blazor'</span></span>

<span data-ttu-id="16b7f-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-177">'Identity'</span></span>

<span data-ttu-id="16b7f-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-178">'Let's Encrypt'</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="16b7f-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-179">'Razor'</span></span>

<span data-ttu-id="16b7f-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-180">'SignalR' uid:</span></span>

<span data-ttu-id="16b7f-181">title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'</span><span class="sxs-lookup"><span data-stu-id="16b7f-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>

* <span data-ttu-id="16b7f-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="16b7f-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="16b7f-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-183">'Blazor'</span></span>
* <span data-ttu-id="16b7f-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16b7f-184">'Identity'</span></span>

<span data-ttu-id="16b7f-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16b7f-185">'Let's Encrypt'</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="16b7f-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16b7f-186">'Razor'</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="16b7f-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="16b7f-187">'SignalR' uid:</span></span>

<span data-ttu-id="16b7f-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine Blazor-Server-App hinzu</span><span class="sxs-lookup"><span data-stu-id="16b7f-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="16b7f-189">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="16b7f-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="16b7f-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor-Server-App</span><span class="sxs-lookup"><span data-stu-id="16b7f-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="16b7f-191">Die Ausgabe der `App`-Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="16b7f-191">Output from the `App` component isn't included.</span></span>

<span data-ttu-id="16b7f-192">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="16b7f-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="16b7f-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die `App`-Komponente in statisches HTML</span><span class="sxs-lookup"><span data-stu-id="16b7f-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span>

* <span data-ttu-id="16b7f-194">Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="16b7f-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>
* <span data-ttu-id="16b7f-195">Fügen Sie eine Route mit niedriger Priorität für die Seite *_Host.cshtml* zur Endpunktkonfiguration in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="16b7f-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="16b7f-196">Fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="16b7f-196">Add routable components to the app.</span></span>

<span data-ttu-id="16b7f-197">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16b7f-197">For example:</span></span>
