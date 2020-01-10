---
title: Browser Link in ASP.net Core
author: ncarandini
description: Erläutert, wie Browser Link eine Visual Studio-Funktion ist, die die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpft.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828269"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="78e1a-103">Browser Link in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="78e1a-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="78e1a-104">Von [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="78e1a-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="78e1a-105">Browser Link ist ein Visual Studio-Feature.</span><span class="sxs-lookup"><span data-stu-id="78e1a-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="78e1a-106">Er erstellt einen Kommunikationskanal zwischen der Entwicklungsumgebung und einem oder mehreren Webbrowsern.</span><span class="sxs-lookup"><span data-stu-id="78e1a-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="78e1a-107">Sie können die Browser Verknüpfung verwenden, um Ihre Web-App in mehreren Browsern gleichzeitig zu aktualisieren, was für Browser übergreifende Tests nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="78e1a-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="78e1a-108">Browser Link einrichten</span><span class="sxs-lookup"><span data-stu-id="78e1a-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e1a-109">Fügen Sie dem Projekt das [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) -Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="78e1a-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="78e1a-110">Aktivieren Sie für ASP.net Core Razor Pages-oder MVC-Projekte auch die Lauf Zeit Kompilierung von Razor-Dateien ( *. cshtml*), wie unter <xref:mvc/views/view-compilation>beschrieben.</span><span class="sxs-lookup"><span data-stu-id="78e1a-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="78e1a-111">Razor-Syntax Änderungen werden nur angewendet, wenn die Laufzeit-Kompilierung aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="78e1a-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="78e1a-112">Wenn Sie ein ASP.net Core 2,0-Projekt in ASP.net Core 2,1 und den Übergang in das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" umrechnen, installieren Sie das Paket " [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) " für Browser Link Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="78e1a-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="78e1a-113">In den ASP.net Core 2,1-Projektvorlagen wird standardmäßig das `Microsoft.AspNetCore.App` Metapaket verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e1a-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78e1a-114">In den Projektvorlagen für die ASP.net Core 2,0- **Webanwendung**, **leere**und **Web-API** wird das [Microsoft. aspnetcore. all-Metapaket](xref:fundamentals/metapackage)verwendet, das einen Paket Verweis für [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)enthält.</span><span class="sxs-lookup"><span data-stu-id="78e1a-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="78e1a-115">Daher ist für die Verwendung des `Microsoft.AspNetCore.All` Metapakets keine weitere Aktion erforderlich, um den Browser Link zur Verwendung verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="78e1a-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="78e1a-116">Die Projektvorlage "ASP.net Core 1. x- **Webanwendung** " enthält einen Paket Verweis für das Paket " [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ".</span><span class="sxs-lookup"><span data-stu-id="78e1a-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="78e1a-117">Andere Projekttypen erfordern, dass Sie `Microsoft.VisualStudio.Web.BrowserLink`einen Paket Verweis hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="78e1a-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="78e1a-118">-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="78e1a-118">Configuration</span></span>

<span data-ttu-id="78e1a-119">Rufen Sie `UseBrowserLink` in der `Startup.Configure`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="78e1a-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="78e1a-120">Der `UseBrowserLink`-Befehl wird in der Regel in einem `if`-Block platziert, der nur den Browser Link in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e1a-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="78e1a-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e1a-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="78e1a-122">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="78e1a-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="78e1a-123">Verwenden von Browser Link</span><span class="sxs-lookup"><span data-stu-id="78e1a-123">How to use Browser Link</span></span>

<span data-ttu-id="78e1a-124">Wenn Sie ein ASP.net Core Projekt geöffnet haben, zeigt Visual Studio das Browser Link Symbolleisten-Steuerelement neben dem Symbolleisten-Steuerelement **Debug-Ziel** an:</span><span class="sxs-lookup"><span data-stu-id="78e1a-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Browser Link-Dropdown Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="78e1a-126">Im Browser Link Symbolleisten-Steuerelement können Sie folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="78e1a-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="78e1a-127">Aktualisieren Sie die Web-App in mehreren Browsern gleichzeitig.</span><span class="sxs-lookup"><span data-stu-id="78e1a-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="78e1a-128">Öffnen Sie das **Browser Link-Dashboard**.</span><span class="sxs-lookup"><span data-stu-id="78e1a-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="78e1a-129">Aktiviert oder deaktiviert den **Browser Link**.</span><span class="sxs-lookup"><span data-stu-id="78e1a-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="78e1a-130">Hinweis: der Browser Link ist in Visual Studio standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e1a-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="78e1a-131">Aktivieren oder deaktivieren Sie die [Automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="78e1a-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="78e1a-132">Die Web-App in mehreren Browsern gleichzeitig aktualisieren</span><span class="sxs-lookup"><span data-stu-id="78e1a-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="78e1a-133">Verwenden Sie zum Auswählen eines einzelnen Webbrowsers, der beim Starten des Projekts gestartet werden soll, das Dropdown Menü im Symbolleisten-Steuerelement **Debug-Ziel** :</span><span class="sxs-lookup"><span data-stu-id="78e1a-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5-Dropdown Menü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="78e1a-135">Wenn Sie mehrere Browser gleichzeitig öffnen möchten, wählen Sie in der Dropdown Liste die Option **Durchsuchen mit...** aus.</span><span class="sxs-lookup"><span data-stu-id="78e1a-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="78e1a-136">Halten Sie die <kbd>STRG</kbd> -Taste gedrückt, um die gewünschten Browser auszuwählen, und klicken Sie dann auf **Durchsuchen**:</span><span class="sxs-lookup"><span data-stu-id="78e1a-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Viele Browser gleichzeitig öffnen](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="78e1a-138">Der folgende Screenshot zeigt Visual Studio mit der geöffneten Index Ansicht und zwei geöffneten Browsern:</span><span class="sxs-lookup"><span data-stu-id="78e1a-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Beispiel für Synchronisierung mit zwei Browsern](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="78e1a-140">Zeigen Sie auf das Symbolleisten-Steuerelement Browser Link, um die Browser anzuzeigen, die mit dem Projekt verbunden sind:</span><span class="sxs-lookup"><span data-stu-id="78e1a-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hover-Tipp](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="78e1a-142">Ändern Sie die Index Sicht, und alle verbundenen Browser werden aktualisiert, wenn Sie auf die Schaltfläche zum Aktualisieren des Browser Links klicken:</span><span class="sxs-lookup"><span data-stu-id="78e1a-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![Browser-Sync-zu-Änderungen](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="78e1a-144">Browser Link funktioniert auch mit Browsern, die Sie von außerhalb von Visual Studio starten, und navigieren Sie zur App-URL.</span><span class="sxs-lookup"><span data-stu-id="78e1a-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="78e1a-145">Das Browser Link-Dashboard</span><span class="sxs-lookup"><span data-stu-id="78e1a-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="78e1a-146">Öffnen Sie im Dropdown Menü Browser Link das Fenster **Browser Link-Dashboard** , um die Verbindung mit geöffneten Browsern zu verwalten:</span><span class="sxs-lookup"><span data-stu-id="78e1a-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="78e1a-148">Wenn kein Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, indem Sie den Link **in Browser anzeigen** auswählen:</span><span class="sxs-lookup"><span data-stu-id="78e1a-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="78e1a-150">Andernfalls werden die verbundenen Browser mit dem Pfad zu der Seite angezeigt, die von den einzelnen Browsern angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e1a-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="78e1a-152">Sie können auch auf einen einzelnen Browser Namen klicken, um nur diesen Browser zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="78e1a-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="78e1a-153">Browser Link aktivieren oder deaktivieren</span><span class="sxs-lookup"><span data-stu-id="78e1a-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="78e1a-154">Wenn Sie den Browser Link nach der Deaktivierung erneut aktivieren, müssen Sie die Browser aktualisieren, um Sie erneut zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="78e1a-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="78e1a-155">Aktivieren oder Deaktivieren der automatischen CSS-Synchronisierung</span><span class="sxs-lookup"><span data-stu-id="78e1a-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="78e1a-156">Wenn die automatische CSS-Synchronisierung aktiviert ist, werden verbundene Browser automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.</span><span class="sxs-lookup"><span data-stu-id="78e1a-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="78e1a-157">So funktioniert's</span><span class="sxs-lookup"><span data-stu-id="78e1a-157">How it works</span></span>

<span data-ttu-id="78e1a-158">Browser Link verwendet [SignalR](xref:signalr/introduction) , um einen Kommunikationskanal zwischen Visual Studio und dem Browser zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="78e1a-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="78e1a-159">Wenn der Browser Link aktiviert ist, fungiert Visual Studio als SignalR Server, mit dem mehrere Clients (Browser) eine Verbindung herstellen können.</span><span class="sxs-lookup"><span data-stu-id="78e1a-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="78e1a-160">Der Browser Link registriert auch eine Middlewarekomponente in der ASP.net Core Anforderungs Pipeline.</span><span class="sxs-lookup"><span data-stu-id="78e1a-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="78e1a-161">Diese Komponente fügt besondere `<script>` Verweise auf jede Seiten Anforderung vom Server ein.</span><span class="sxs-lookup"><span data-stu-id="78e1a-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="78e1a-162">Sie können die Skript Verweise anzeigen, indem Sie im Browser **Quelle anzeigen** auswählen und einen Bildlauf zum Ende des `<body>`-Taginhalts durchführen:</span><span class="sxs-lookup"><span data-stu-id="78e1a-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="78e1a-163">Die Quelldateien werden nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="78e1a-163">Your source files aren't modified.</span></span> <span data-ttu-id="78e1a-164">Die Middleware-Komponente fügt die Skript Verweise dynamisch ein.</span><span class="sxs-lookup"><span data-stu-id="78e1a-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="78e1a-165">Da es sich bei dem Browser seitigen Code um JavaScript handelt, funktioniert er für alle Browser, die SignalR unterstützt, ohne dass ein Browser-Plug-in erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="78e1a-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
