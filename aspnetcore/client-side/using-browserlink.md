---
title: Browser Link in ASP.net Core
author: ncarandini
description: Erläutert, wie Browser Link eine Visual Studio-Funktion ist, die die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpft.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962780"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="96b80-103">Browser Link in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="96b80-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="96b80-104">Von [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="96b80-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="96b80-105">Browser Link ist eine Funktion in Visual Studio, die einen Kommunikationskanal zwischen der Entwicklungsumgebung und einem oder mehreren Webbrowsern erstellt.</span><span class="sxs-lookup"><span data-stu-id="96b80-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="96b80-106">Sie können die Browser Verknüpfung verwenden, um Ihre Webanwendung in mehreren Browsern gleichzeitig zu aktualisieren, was für Browser übergreifende Tests nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="96b80-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="96b80-107">Browser Link einrichten</span><span class="sxs-lookup"><span data-stu-id="96b80-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="96b80-108">Wenn Sie ein ASP.net Core 2,0-Projekt in ASP.net Core 2,1 und den Übergang in das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app), installieren Sie das Paket [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) für die browserlink-Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="96b80-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="96b80-109">In den ASP.net Core 2,1-Projektvorlagen wird standardmäßig das `Microsoft.AspNetCore.App` Metapaket verwendet.</span><span class="sxs-lookup"><span data-stu-id="96b80-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="96b80-110">In den Projektvorlagen für die ASP.net Core 2,0- **Webanwendung**, **leere**und **Web-API** wird das [Microsoft. aspnetcore. all-Metapaket](xref:fundamentals/metapackage)verwendet, das einen Paket Verweis für [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)enthält.</span><span class="sxs-lookup"><span data-stu-id="96b80-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="96b80-111">Daher ist für die Verwendung des `Microsoft.AspNetCore.All` Metapakets keine weitere Aktion erforderlich, um den Browser Link zur Verwendung verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="96b80-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="96b80-112">Die Projektvorlage "ASP.net Core 1. x- **Webanwendung** " enthält einen Paket Verweis für das Paket " [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ".</span><span class="sxs-lookup"><span data-stu-id="96b80-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="96b80-113">Die **leeren** oder **Web-API** -Vorlagen Projekte erfordern, dass Sie `Microsoft.VisualStudio.Web.BrowserLink`einen Paket Verweis hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="96b80-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="96b80-114">Da es sich hierbei um ein Visual Studio-Feature handelt, ist die einfachste Möglichkeit zum Hinzufügen des Pakets zu einem **leeren** oder **Web-API** -Vorlagen Projekt das Öffnen der Paket- **Manager-Konsole** (**anzeigen** > **anderen Windows** > **Package Manager-Konsole**) und das Ausführen des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="96b80-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="96b80-115">Alternativ können Sie den **nuget-Paket-Manager**verwenden.</span><span class="sxs-lookup"><span data-stu-id="96b80-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="96b80-116">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie **nuget-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="96b80-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Nuget-Paket-Manager öffnen](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="96b80-118">Suchen und installieren Sie das Paket:</span><span class="sxs-lookup"><span data-stu-id="96b80-118">Find and install the package:</span></span>

![Hinzufügen eines Pakets mit dem nuget-Paket-Manager](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="96b80-120">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="96b80-120">Configuration</span></span>

<span data-ttu-id="96b80-121">Für die `Startup.Configure`-Methode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="96b80-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="96b80-122">Normalerweise befindet sich der Code in einem `if`-Block, der nur den Browser Link in der Entwicklungsumgebung aktiviert, wie hier gezeigt:</span><span class="sxs-lookup"><span data-stu-id="96b80-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="96b80-123">Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="96b80-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="96b80-124">Verwenden von Browser Link</span><span class="sxs-lookup"><span data-stu-id="96b80-124">How to use Browser Link</span></span>

<span data-ttu-id="96b80-125">Wenn Sie ein ASP.net Core Projekt geöffnet haben, zeigt Visual Studio das Browser Link Symbolleisten-Steuerelement neben dem Symbolleisten-Steuerelement **Debug-Ziel** an:</span><span class="sxs-lookup"><span data-stu-id="96b80-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Browser Link-Dropdown Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="96b80-127">Im Browser Link Symbolleisten-Steuerelement können Sie folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="96b80-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="96b80-128">Aktualisieren Sie die Webanwendung in mehreren Browsern gleichzeitig.</span><span class="sxs-lookup"><span data-stu-id="96b80-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="96b80-129">Öffnen Sie das **Browser Link-Dashboard**.</span><span class="sxs-lookup"><span data-stu-id="96b80-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="96b80-130">Aktiviert oder deaktiviert den **Browser Link**.</span><span class="sxs-lookup"><span data-stu-id="96b80-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="96b80-131">Hinweis: der Browser Link ist in Visual Studio 2017 (15,3) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="96b80-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="96b80-132">Aktivieren oder deaktivieren Sie die [Automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="96b80-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="96b80-133">Einige Visual Studio-Plug-ins, insbesondere *Web Extension Pack 2015* und *Web Extension Pack 2017*, bieten erweiterte Funktionen für den Browser Link. einige der zusätzlichen Features funktionieren jedoch nicht mit ASP.net Core Projekten.</span><span class="sxs-lookup"><span data-stu-id="96b80-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="96b80-134">Die Web-App in mehreren Browsern gleichzeitig aktualisieren</span><span class="sxs-lookup"><span data-stu-id="96b80-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="96b80-135">Verwenden Sie zum Auswählen eines einzelnen Webbrowsers, der beim Starten des Projekts gestartet werden soll, das Dropdown Menü im Symbolleisten-Steuerelement **Debug-Ziel** :</span><span class="sxs-lookup"><span data-stu-id="96b80-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5-Dropdown Menü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="96b80-137">Wenn Sie mehrere Browser gleichzeitig öffnen möchten, wählen Sie in der Dropdown Liste die Option **Durchsuchen mit...** aus.</span><span class="sxs-lookup"><span data-stu-id="96b80-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="96b80-138">Halten Sie die STRG-Taste gedrückt, um die gewünschten Browser auszuwählen, und klicken Sie dann auf **Durchsuchen**:</span><span class="sxs-lookup"><span data-stu-id="96b80-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Viele Browser gleichzeitig öffnen](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="96b80-140">Der folgende Screenshot zeigt Visual Studio mit der geöffneten Index Ansicht und zwei geöffneten Browsern:</span><span class="sxs-lookup"><span data-stu-id="96b80-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Beispiel für Synchronisierung mit zwei Browsern](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="96b80-142">Zeigen Sie auf das Symbolleisten-Steuerelement Browser Link, um die Browser anzuzeigen, die mit dem Projekt verbunden sind:</span><span class="sxs-lookup"><span data-stu-id="96b80-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hover-Tipp](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="96b80-144">Ändern Sie die Index Sicht, und alle verbundenen Browser werden aktualisiert, wenn Sie auf die Schaltfläche zum Aktualisieren des Browser Links klicken:</span><span class="sxs-lookup"><span data-stu-id="96b80-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![Browser-Sync-zu-Änderungen](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="96b80-146">Browser Link funktioniert auch mit Browsern, die Sie von außerhalb von Visual Studio starten und zur Anwendungs-URL navigieren.</span><span class="sxs-lookup"><span data-stu-id="96b80-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="96b80-147">Das Browser Link-Dashboard</span><span class="sxs-lookup"><span data-stu-id="96b80-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="96b80-148">Öffnen Sie im Dropdown Menü Browser Link das Browser Link-Dashboard, um die Verbindung mit geöffneten Browsern zu verwalten:</span><span class="sxs-lookup"><span data-stu-id="96b80-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Open-browserslink-Dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="96b80-150">Wenn kein Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, indem Sie den Link *in Browser anzeigen* auswählen:</span><span class="sxs-lookup"><span data-stu-id="96b80-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-Dashboard-keine-Verbindungen](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="96b80-152">Andernfalls werden die verbundenen Browser mit dem Pfad zu der Seite angezeigt, die von den einzelnen Browsern angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="96b80-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-Dashboard-Two-Connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="96b80-154">Wenn Sie möchten, können Sie auf einen aufgelisteten Browser Namen klicken, um den einzelnen Browser zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="96b80-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="96b80-155">Browser Link aktivieren oder deaktivieren</span><span class="sxs-lookup"><span data-stu-id="96b80-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="96b80-156">Wenn Sie den Browser Link nach der Deaktivierung erneut aktivieren, müssen Sie die Browser aktualisieren, um Sie erneut zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="96b80-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="96b80-157">Aktivieren oder Deaktivieren der automatischen CSS-Synchronisierung</span><span class="sxs-lookup"><span data-stu-id="96b80-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="96b80-158">Wenn die automatische CSS-Synchronisierung aktiviert ist, werden verbundene Browser automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.</span><span class="sxs-lookup"><span data-stu-id="96b80-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="96b80-159">Funktionsweise</span><span class="sxs-lookup"><span data-stu-id="96b80-159">How it works</span></span>

<span data-ttu-id="96b80-160">Browser Link verwendet SignalR, um einen Kommunikationskanal zwischen Visual Studio und dem Browser zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="96b80-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="96b80-161">Wenn der Browser Link aktiviert ist, fungiert Visual Studio als SignalR Server, mit dem mehrere Clients (Browser) eine Verbindung herstellen können.</span><span class="sxs-lookup"><span data-stu-id="96b80-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="96b80-162">Der Browser Link registriert auch eine Middlewarekomponente in der ASP.net Core Anforderungs Pipeline.</span><span class="sxs-lookup"><span data-stu-id="96b80-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="96b80-163">Diese Komponente fügt besondere `<script>` Verweise auf jede Seiten Anforderung vom Server ein.</span><span class="sxs-lookup"><span data-stu-id="96b80-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="96b80-164">Sie können die Skript Verweise anzeigen, indem Sie im Browser **Quelle anzeigen** auswählen und einen Bildlauf zum Ende des `<body>`-Taginhalts durchführen:</span><span class="sxs-lookup"><span data-stu-id="96b80-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="96b80-165">Die Quelldateien werden nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="96b80-165">Your source files aren't modified.</span></span> <span data-ttu-id="96b80-166">Die Middleware-Komponente fügt die Skript Verweise dynamisch ein.</span><span class="sxs-lookup"><span data-stu-id="96b80-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="96b80-167">Da es sich bei dem Browser seitigen Code um JavaScript handelt, funktioniert er für alle Browser, die SignalR unterstützt, ohne dass ein Browser-Plug-in erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="96b80-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
