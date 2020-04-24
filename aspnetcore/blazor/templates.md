---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661717"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="1d051-103">ASP.NET Core-Blazor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="1d051-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="1d051-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d051-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1d051-105">Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:</span><span class="sxs-lookup"><span data-stu-id="1d051-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="1d051-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="1d051-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="1d051-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="1d051-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="1d051-108">Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1d051-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="1d051-109">Eine ausführliche Anleitung zum Erstellen einer Blazor-App aus einer Vorlage finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="1d051-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="1d051-110">Vorlagenoptionen sind verfügbar, indem Sie die `--help`-Option an den CLI-Befehl [dotnet new](/dotnet/core/tools/dotnet-new) übergeben:</span><span class="sxs-lookup"><span data-stu-id="1d051-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="1d051-111">-Projektstruktur</span><span class="sxs-lookup"><span data-stu-id="1d051-111"> project structure</span></span>

<span data-ttu-id="1d051-112">Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Vorlage generiert wird:</span><span class="sxs-lookup"><span data-stu-id="1d051-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="1d051-113">*Program.cs:* der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="1d051-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="1d051-114">der ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="1d051-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="1d051-115">der WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der BlazorWebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="1d051-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="1d051-116">Die `App`-Komponente, die die Stammkomponente der App ist, wird als `app`-DOM-Element für die `Add`-Methode angegeben.</span><span class="sxs-lookup"><span data-stu-id="1d051-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="1d051-117">Dienste können mit der `ConfigureServices`-Methode auf dem Host-Generator konfiguriert werden (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="1d051-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="1d051-118">Die Konfiguration kann über den Host-Generator (`builder.Configuration`) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1d051-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="1d051-119">*Startup.cs* (Blazor Server): Diese Datei enthält die Startlogik der App.</span><span class="sxs-lookup"><span data-stu-id="1d051-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="1d051-120">Die `Startup`-Klasse definiert zwei Methoden:</span><span class="sxs-lookup"><span data-stu-id="1d051-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="1d051-121">`ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App.</span><span class="sxs-lookup"><span data-stu-id="1d051-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="1d051-122">In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> hinzugefügt, und `WeatherForecastService` wird zum Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="1d051-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="1d051-123">`Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:</span><span class="sxs-lookup"><span data-stu-id="1d051-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="1d051-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten.</span><span class="sxs-lookup"><span data-stu-id="1d051-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="1d051-125">Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.</span><span class="sxs-lookup"><span data-stu-id="1d051-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="1d051-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (*Pages/_Host.cshtml*) einzurichten und die Navigation zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="1d051-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="1d051-127">*wwwroot/index.html* (Blazor WebAssembly): die Stammseite der App, die als HTML-Seite implementiert wird:</span><span class="sxs-lookup"><span data-stu-id="1d051-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="1d051-128">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1d051-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="1d051-129">Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="1d051-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="1d051-130">Die `App`-Komponente (*App.razor*) wird in der `AddComponent`-Methode in `Startup.Configure` als `app`-DOM-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="1d051-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="1d051-131">Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:</span><span class="sxs-lookup"><span data-stu-id="1d051-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="1d051-132">Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.</span><span class="sxs-lookup"><span data-stu-id="1d051-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="1d051-133">Sie initialisiert die Runtime, damit die App ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d051-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="1d051-134">*App.razor*: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet.</span><span class="sxs-lookup"><span data-stu-id="1d051-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="1d051-135">Die `Router`-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.</span><span class="sxs-lookup"><span data-stu-id="1d051-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="1d051-136">*Pages*: Dieser Ordner enthält die routingfähigen Komponenten und Seiten ( *.razor*), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor-Server-App.</span><span class="sxs-lookup"><span data-stu-id="1d051-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="1d051-137">Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben.</span><span class="sxs-lookup"><span data-stu-id="1d051-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1d051-138">Die Vorlage besteht aus den folgenden Teilen:</span><span class="sxs-lookup"><span data-stu-id="1d051-138">The template includes the following:</span></span>
  * <span data-ttu-id="1d051-139">*_Host.cshtml* (Blazor-Server): die Stammseite der App, die als Razor-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="1d051-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="1d051-140">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1d051-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="1d051-141">Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="1d051-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="1d051-142">Auf der Hostseite wird angegeben, wo die `App`-Stammkomponente (*App.razor*) gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="1d051-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="1d051-143">`Counter` (*Counter.razor*) implementiert die Zählerseite</span><span class="sxs-lookup"><span data-stu-id="1d051-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="1d051-144">`Error` (*Error.razor*, nur Blazor Server-Apps): wird gerendert, wenn ein Ausnahmefehler in der App auftritt</span><span class="sxs-lookup"><span data-stu-id="1d051-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="1d051-145">`FetchData` (*FetchData.razor*): implementiert die Seite zum Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="1d051-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="1d051-146">`Index` (*Index.razor*): implementiert die Startseite</span><span class="sxs-lookup"><span data-stu-id="1d051-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="1d051-147">*Shared*: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten ( *.razor*), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1d051-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="1d051-148">`MainLayout` (*MainLayout.razor*): die Layoutkomponente der App</span><span class="sxs-lookup"><span data-stu-id="1d051-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="1d051-149">`NavMenu` (*NavMenu.razor*): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="1d051-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="1d051-150">Sie schließt die [NavLink-Komponente](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="1d051-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="1d051-151">Die `NavLink`-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1d051-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="1d051-152">*_Imports.razor*: enthält gängige Razor-Anweisungen, die in die Komponenten der App ( *.razor*) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces</span><span class="sxs-lookup"><span data-stu-id="1d051-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="1d051-153">*Data* (Blazor Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="1d051-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="1d051-154">*wwwroot*: der [Web Root](xref:fundamentals/index#web-root)-Ordner für die App, der die öffentlichen statischen Ressourcen der App enthält</span><span class="sxs-lookup"><span data-stu-id="1d051-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="1d051-155">*appsettings.json* (Blazor Server): Konfigurationseinstellungen für die App</span><span class="sxs-lookup"><span data-stu-id="1d051-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
