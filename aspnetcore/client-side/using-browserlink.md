---
title: Browserlink in ASP.NET Core
author: ncarandini
description: Erläutert, wie Browser Link für ein Visual Studio-Feature ist, die die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpft.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894707"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="a8f10-103">Browserlink in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8f10-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="a8f10-104">Durch [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a8f10-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a8f10-105">Browserverknüpfung ist ein Feature in Visual Studio, der einen Kommunikationskanal zwischen der Entwicklungs- und eine oder mehrere Webbrowser erstellt.</span><span class="sxs-lookup"><span data-stu-id="a8f10-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="a8f10-106">Sie können Browser Link, um Ihre Webanwendung in mehreren Browsern gleichzeitig zu aktualisieren, dies ist hilfreich für browserübergreifende Tests verwenden.</span><span class="sxs-lookup"><span data-stu-id="a8f10-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="a8f10-107">Setup für Browser Link</span><span class="sxs-lookup"><span data-stu-id="a8f10-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a8f10-108">Beim Konvertieren von einem ASP.NET Core 2.0-Projekt in ASP.NET Core 2.1 und auf der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), installieren Sie die [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) für Paket BrowserLink-Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="a8f10-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="a8f10-109">Verwenden Sie die Projektvorlagen für ASP.NET Core 2.1 die `Microsoft.AspNetCore.App` metapaket standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="a8f10-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a8f10-110">ASP.NET Core 2.0 **Webanwendung**, **leere**, und **Web-API-** Projekt Vorlagen verwenden die [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) , die für die Paketverweise enthält [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="a8f10-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="a8f10-111">Daher wird die Verwendung der `Microsoft.AspNetCore.All` metapaket erfordert keine weitere Aktion aus, um Browser Link für die Verwendung verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="a8f10-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="a8f10-112">Die ASP.NET Core 1.x **Webanwendung** -Projektvorlage verfügt über einen Paketverweis für die [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Paket.</span><span class="sxs-lookup"><span data-stu-id="a8f10-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="a8f10-113">Die **leere** oder **Web-API-** -Vorlagenprojekten müssen Sie zum Hinzufügen von paketverweisen zu `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="a8f10-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="a8f10-114">Da dies eine Visual Studio-Funktion, die einfachste Möglichkeit, fügen das Paket ist eine **leere** oder **Web-API-** -Vorlagenprojekt besteht darin, öffnen die **-Paket-Manager-Konsole** (**Ansicht** > **Other Windows** > **-Paket-Manager-Konsole**), und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="a8f10-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="a8f10-115">Alternativ können Sie **NuGet Package Manager**.</span><span class="sxs-lookup"><span data-stu-id="a8f10-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="a8f10-116">Mit der rechten Maustaste in des Namens des Projekts **Projektmappen-Explorer** , und wählen Sie **NuGet-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="a8f10-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Öffnen von NuGet-Paket-Manager](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="a8f10-118">Suchen Sie und installieren Sie das Paket:</span><span class="sxs-lookup"><span data-stu-id="a8f10-118">Find and install the package:</span></span>

![Paket mit NuGet-Paket-Manager hinzufügen](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="a8f10-120">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a8f10-120">Configuration</span></span>

<span data-ttu-id="a8f10-121">Für die `Startup.Configure`-Methode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a8f10-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="a8f10-122">Der Code in der Regel befindet sich in einem `if` Block, der Browser Link in der Entwicklungsumgebung, nur wie hier gezeigt kann:</span><span class="sxs-lookup"><span data-stu-id="a8f10-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="a8f10-123">Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a8f10-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="a8f10-124">Gewusst wie: Verwenden von Browser Link</span><span class="sxs-lookup"><span data-stu-id="a8f10-124">How to use Browser Link</span></span>

<span data-ttu-id="a8f10-125">Wenn Sie eine ASP.NET Core-Projekt geöffnet haben, zeigt Visual Studio das Symbolleisten-Steuerelement von Browser Link neben der **Debugziel** Toolbar-Steuerelement:</span><span class="sxs-lookup"><span data-stu-id="a8f10-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Browser Link Dropdown-Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="a8f10-127">Auf dem Browserlink-Symbolleisten-Steuerelement können Sie folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="a8f10-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="a8f10-128">Aktualisieren Sie die Webanwendung in mehreren Browsern gleichzeitig.</span><span class="sxs-lookup"><span data-stu-id="a8f10-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="a8f10-129">Öffnen der **Browserlink-Dashboard**.</span><span class="sxs-lookup"><span data-stu-id="a8f10-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="a8f10-130">Aktivieren oder deaktivieren Sie **Browserlink**.</span><span class="sxs-lookup"><span data-stu-id="a8f10-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="a8f10-131">Hinweis: Browserverknüpfung ist in Visual Studio 2017 (Version 15.3) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a8f10-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="a8f10-132">Aktivieren oder deaktivieren Sie [automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="a8f10-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="a8f10-133">Einige Visual Studio-Plug-ins, insbesondere *2015 für Web-Erweiterung Pack* und *Web Erweiterung Pack 2017*, bieten Sie erweiterte Funktionen für Browser Link, aber einige der zusätzlichen Features funktionieren nicht mit ASP. NET-Core-Projekte.</span><span class="sxs-lookup"><span data-stu-id="a8f10-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="a8f10-134">Aktualisieren Sie die Web-app in mehreren Browsern gleichzeitig</span><span class="sxs-lookup"><span data-stu-id="a8f10-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="a8f10-135">Um einen einzelnen Webbrowser zum Starten, wenn das Projekt starten zu auszuwählen, verwenden Sie im Dropdown-Menü in der **Debugziel** Toolbar-Steuerelement:</span><span class="sxs-lookup"><span data-stu-id="a8f10-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5-Dropdownmenü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="a8f10-137">Wählen Sie zum Öffnen von mehreren Browsern gleichzeitig **Browserauswahl...**  aus der gleichen Dropdownliste aus.</span><span class="sxs-lookup"><span data-stu-id="a8f10-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="a8f10-138">Halten Sie die STRG-Taste auf die Browser auswählen, werden soll, und klicken Sie dann auf **Durchsuchen**:</span><span class="sxs-lookup"><span data-stu-id="a8f10-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Öffnen Sie die vielen Browsern gleichzeitig](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="a8f10-140">Hier ist ein Screenshot der Visual Studio mit der Ansicht "Index" öffnen und zwei geöffneten Browser:</span><span class="sxs-lookup"><span data-stu-id="a8f10-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Synchronisieren Sie mit zwei Browser-Beispiel](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="a8f10-142">Zeigen Sie auf dem Browserlink-Symbolleisten-Steuerelement auf den Browsern, die dem Projekt verbunden sind, finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="a8f10-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Zeigen Sie mit tip](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="a8f10-144">Ändern die Ansicht "Index", und aller verbundenen Browser werden aktualisiert, wenn Sie den Browserlink-Schaltfläche "Aktualisieren" klicken:</span><span class="sxs-lookup"><span data-stu-id="a8f10-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![Browser-Sync-zu-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="a8f10-146">Browserverknüpfung funktioniert auch mit Browsern, die Sie außerhalb von Visual Studio starten, und navigieren Sie zu der Anwendungs-URL.</span><span class="sxs-lookup"><span data-stu-id="a8f10-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="a8f10-147">Die Browserlink-Dashboard</span><span class="sxs-lookup"><span data-stu-id="a8f10-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="a8f10-148">Öffnen Sie den Browserlink-Dashboard, aus der Browserlink-Dropdown-Menü, um die Verbindung mit geöffneten Browser zu verwalten:</span><span class="sxs-lookup"><span data-stu-id="a8f10-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="a8f10-150">Wenn kein Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, durch Auswählen der *in Browser anzeigen* Link:</span><span class="sxs-lookup"><span data-stu-id="a8f10-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="a8f10-152">Andernfalls werden die verbundenen Browsern durch den Pfad zu der Seite angezeigt, die von jedem Browser angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a8f10-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="a8f10-154">Wenn Sie möchten, können Sie einen Namen aufgeführten Browser diese einzelnen Browser aktualisieren, klicken.</span><span class="sxs-lookup"><span data-stu-id="a8f10-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="a8f10-155">Aktivieren oder Deaktivieren von Browser Link</span><span class="sxs-lookup"><span data-stu-id="a8f10-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="a8f10-156">Wenn Sie Browser Link erneut aktivieren nach dem deaktivieren, müssen Sie den Browser, um sie erneut eine Verbindung herstellen aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a8f10-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="a8f10-157">Aktivieren Sie oder deaktivieren Sie die automatische CSS-Synchronisierung</span><span class="sxs-lookup"><span data-stu-id="a8f10-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="a8f10-158">Wenn die automatische CSS-Synchronisierung aktiviert ist, werden die verbundene Browsern automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.</span><span class="sxs-lookup"><span data-stu-id="a8f10-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="a8f10-159">So funktioniert es</span><span class="sxs-lookup"><span data-stu-id="a8f10-159">How it works</span></span>

<span data-ttu-id="a8f10-160">Browserverknüpfung verwendet SignalR, um einen Kommunikationskanal zwischen Visual Studio und den Browser zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a8f10-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="a8f10-161">Wenn Browser Link aktiviert ist, fungiert Visual Studio als SignalR-Server, dem mehrere Clients (Browser) herstellen können.</span><span class="sxs-lookup"><span data-stu-id="a8f10-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="a8f10-162">Browserverknüpfung registriert auch eine middlewarekomponente in der ASP.NET Core-Anforderungspipeline.</span><span class="sxs-lookup"><span data-stu-id="a8f10-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="a8f10-163">Diese Komponente fügt spezielle `<script>` verweisen in jede Anforderung einer Seite vom Server.</span><span class="sxs-lookup"><span data-stu-id="a8f10-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="a8f10-164">Sie sehen die Skriptverweise dazu **Quelltext anzeigen** im Browser, und scrollen bis zum Ende der `<body>` Inhalte:</span><span class="sxs-lookup"><span data-stu-id="a8f10-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="a8f10-165">Die Quelldateien werden nicht geändert werden.</span><span class="sxs-lookup"><span data-stu-id="a8f10-165">Your source files aren't modified.</span></span> <span data-ttu-id="a8f10-166">Die middlewarekomponente fügt die Skriptverweise dynamisch.</span><span class="sxs-lookup"><span data-stu-id="a8f10-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="a8f10-167">Da der Browser-Side-Code alle JavaScript ist, funktioniert in allen Browsern, die SignalR unterstützt werden, ohne dass einen Browser-Plug-in.</span><span class="sxs-lookup"><span data-stu-id="a8f10-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
