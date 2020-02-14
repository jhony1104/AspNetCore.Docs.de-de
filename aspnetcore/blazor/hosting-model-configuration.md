---
title: Konfiguration des ASP.net Core Blazor Hostingmodells
author: guardrex
description: Erfahren Sie mehr über die Konfiguration von Blazor Hostingmodellen, einschließlich der Integration von Razor-Komponenten in Razor Pages und MVC-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214940"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="58658-103">Konfiguration des ASP.net Core blazor-Hostingmodells</span><span class="sxs-lookup"><span data-stu-id="58658-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="58658-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="58658-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="58658-105">In diesem Artikel wird die Konfiguration des Hostingmodells behandelt.</span><span class="sxs-lookup"><span data-stu-id="58658-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="58658-106">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="58658-106">Blazor Server</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="58658-107">Integrieren von Razor-Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="58658-107">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="58658-108">Verwenden von Komponenten in Seiten und Ansichten</span><span class="sxs-lookup"><span data-stu-id="58658-108">Use components in pages and views</span></span>

<span data-ttu-id="58658-109">Eine vorhandene Razor Pages-oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="58658-109">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="58658-110">In der Layoutdatei der APP ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="58658-110">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="58658-111">Fügen Sie dem `<head>`-Element das folgende `<base>`-Tag hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-111">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="58658-112">Beim `href` Wert (dem *App-Basispfad*) im vorhergehenden Beispiel wird davon ausgegangen, dass sich die APP im Stamm-URL-Pfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="58658-112">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="58658-113">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path> Artikels.</span><span class="sxs-lookup"><span data-stu-id="58658-113">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="58658-114">Die Datei " *_Layout. cshtml* " befindet sich im Ordner " *pages/Shared* " in einer Razor Pages-APP oder in *Sichten/freigegebenen* Ordnern in einer MVC-app.</span><span class="sxs-lookup"><span data-stu-id="58658-114">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="58658-115">Fügen Sie direkt vor dem schließenden `</body>`-Tag ein `<script>`-Tag für das Skript " *blazor. Server. js* " hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-115">Add a `<script>` tag for the *blazor.server.js* script right before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="58658-116">Das-Framework fügt der APP das Skript " *blazor. Server. js* " hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-116">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="58658-117">Es ist nicht erforderlich, das Skript manuell der APP hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="58658-117">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="58658-118">Fügen Sie dem Stamm Ordner des Projekts eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu (ändern Sie den letzten Namespace, `MyAppNamespace`, in den Namespace der APP):</span><span class="sxs-lookup"><span data-stu-id="58658-118">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="58658-119">Registrieren Sie in `Startup.ConfigureServices`den blazor-Server Dienst:</span><span class="sxs-lookup"><span data-stu-id="58658-119">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="58658-120">Fügen Sie in `Startup.Configure`den blazor-Hub-Endpunkt `app.UseEndpoints`hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-120">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="58658-121">Integrieren von Komponenten in beliebige Seiten oder Ansichten.</span><span class="sxs-lookup"><span data-stu-id="58658-121">Integrate components into any page or view.</span></span> <span data-ttu-id="58658-122">Weitere Informationen finden Sie im Abschnitt *integrieren von Komponenten in Razor Pages und MVC-apps* des <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> Artikels.</span><span class="sxs-lookup"><span data-stu-id="58658-122">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="58658-123">Verwenden von Routing fähigen Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="58658-123">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="58658-124">So unterstützen Sie Routing fähige Razor-Komponenten in Razor Pages-apps:</span><span class="sxs-lookup"><span data-stu-id="58658-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="58658-125">Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="58658-125">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="58658-126">Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-126">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="58658-127">Fügen Sie dem Ordner *pages* eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="58658-128">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="58658-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="58658-129">Fügen Sie eine Route mit niedriger Priorität für die *_Host. cshtml* -Seite zur Endpunkt Konfiguration in `Startup.Configure`hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="58658-130">Fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-130">Add routable components to the app.</span></span> <span data-ttu-id="58658-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="58658-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="58658-132">Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der Datei *pages/_ViewImports. cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-132">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="58658-133">Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="58658-133">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="58658-134">Verwenden von Routing fähigen Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="58658-134">Use routable components in an MVC app</span></span>

<span data-ttu-id="58658-135">So unterstützen Sie Routing fähige Razor-Komponenten in MVC-apps:</span><span class="sxs-lookup"><span data-stu-id="58658-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="58658-136">Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="58658-136">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="58658-137">Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="58658-138">Fügen Sie dem Ordner " *views/Home* " eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="58658-139">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="58658-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="58658-140">Fügen Sie dem Home-Controller eine Aktion hinzu:</span><span class="sxs-lookup"><span data-stu-id="58658-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="58658-141">Fügen Sie eine Route mit niedriger Priorität für die Controller Aktion hinzu, die die Ansicht *_Host. cshtml* an die Endpunkt Konfiguration in `Startup.Configure`zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="58658-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="58658-142">Erstellen Sie einen Ordner *pages* , und fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="58658-143">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="58658-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="58658-144">Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der *views/_ViewImports. cshtml-* Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-144">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="58658-145">Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="58658-145">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="58658-146">Reflektieren des Verbindungs Zustands in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="58658-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="58658-147">Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="58658-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="58658-148">Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="58658-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="58658-149">Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58658-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="58658-150">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="58658-150">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="58658-151">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="58658-151">CSS class</span></span>                       | <span data-ttu-id="58658-152">Gibt&hellip;</span><span class="sxs-lookup"><span data-stu-id="58658-152">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="58658-153">Eine verlorene Verbindung.</span><span class="sxs-lookup"><span data-stu-id="58658-153">A lost connection.</span></span> <span data-ttu-id="58658-154">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="58658-154">The client is attempting to reconnect.</span></span> <span data-ttu-id="58658-155">Zeigen Sie die modale an.</span><span class="sxs-lookup"><span data-stu-id="58658-155">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="58658-156">Eine aktive Verbindung mit dem Server wird wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="58658-156">An active connection is re-established to the server.</span></span> <span data-ttu-id="58658-157">Blenden Sie den modalen aus.</span><span class="sxs-lookup"><span data-stu-id="58658-157">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="58658-158">Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers.</span><span class="sxs-lookup"><span data-stu-id="58658-158">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="58658-159">Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an.</span><span class="sxs-lookup"><span data-stu-id="58658-159">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="58658-160">Erneute Verbindung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="58658-160">Reconnection rejected.</span></span> <span data-ttu-id="58658-161">Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren.</span><span class="sxs-lookup"><span data-stu-id="58658-161">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="58658-162">Um die APP erneut zu laden, wenden Sie `location.reload()`an.</span><span class="sxs-lookup"><span data-stu-id="58658-162">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="58658-163">Der Verbindungsstatus kann folgende Ergebnisse haben:</span><span class="sxs-lookup"><span data-stu-id="58658-163">This connection state may result when:</span></span><ul><li><span data-ttu-id="58658-164">Ein Absturz in der serverseitigen Verbindung tritt auf.</span><span class="sxs-lookup"><span data-stu-id="58658-164">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="58658-165">Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht.</span><span class="sxs-lookup"><span data-stu-id="58658-165">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="58658-166">Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="58658-166">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="58658-167">Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</span><span class="sxs-lookup"><span data-stu-id="58658-167">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="58658-168">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="58658-168">Render mode</span></span>

<span data-ttu-id="58658-169">Blazor-Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="58658-169">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="58658-170">Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="58658-170">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="58658-171">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="58658-171">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="58658-172">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="58658-172">Is prerendered into the page.</span></span>
* <span data-ttu-id="58658-173">Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="58658-173">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="58658-174">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="58658-174">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="58658-175">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="58658-175">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="58658-176">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="58658-176">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="58658-177">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="58658-177">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="58658-178">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="58658-178">Output from the component isn't included.</span></span> <span data-ttu-id="58658-179">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="58658-179">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="58658-180">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="58658-180">Renders the component into static HTML.</span></span> |

<span data-ttu-id="58658-181">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="58658-181">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="58658-182">Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="58658-182">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="58658-183">Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="58658-183">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="58658-184">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="58658-184">When the page or view renders:</span></span>

* <span data-ttu-id="58658-185">Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.</span><span class="sxs-lookup"><span data-stu-id="58658-185">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="58658-186">Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="58658-186">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="58658-187">Der neue Komponenten Status wird erstellt, wenn die SignalR Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="58658-187">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="58658-188">Die folgende Razor Page rendert eine `Counter` Komponente:</span><span class="sxs-lookup"><span data-stu-id="58658-188">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="58658-189">Nicht interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="58658-189">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="58658-190">Auf der folgenden Razor-Seite wird die `Counter` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="58658-190">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="58658-191">Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="58658-191">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="58658-192">Konfigurieren des SignalR Clients für Blazor Server-apps</span><span class="sxs-lookup"><span data-stu-id="58658-192">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="58658-193">Manchmal müssen Sie den SignalR Client konfigurieren, der von Blazor Server-Apps verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="58658-193">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="58658-194">Beispielsweise können Sie die Protokollierung für den SignalR Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="58658-194">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="58658-195">So konfigurieren Sie den SignalR-Client in der Datei *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58658-195">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="58658-196">Fügen Sie dem `<script>`-Tag für das `blazor.server.js` Skript ein `autostart="false"` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="58658-196">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="58658-197">Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den SignalR Generator angibt.</span><span class="sxs-lookup"><span data-stu-id="58658-197">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
