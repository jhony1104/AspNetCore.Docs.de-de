---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: f582e8201a3393b848cf3f2c21ce3a7df5554100
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105531"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="c5d3b-103">ASP.NET Core-Blazor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="c5d3b-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="c5d3b-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c5d3b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c5d3b-105">Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="c5d3b-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="c5d3b-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="c5d3b-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="c5d3b-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="c5d3b-108">Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="c5d3b-109">Eine ausführliche Anleitung zum Erstellen einer Blazor-App aus einer Vorlage finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="c5d3b-110">Vorlagenoptionen sind verfügbar, indem Sie die `--help`-Option an den CLI-Befehl [dotnet new](/dotnet/core/tools/dotnet-new) übergeben:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="c5d3b-111">-Projektstruktur</span><span class="sxs-lookup"><span data-stu-id="c5d3b-111"> project structure</span></span>

<span data-ttu-id="c5d3b-112">Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Vorlage generiert wird:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="c5d3b-113">*Program.cs*: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-113">*Program.cs*: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="c5d3b-114">der ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="c5d3b-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="c5d3b-115">WebAssembly-Host (Blazor-WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor-WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="c5d3b-116">Die `App`-Komponente, die die Stammkomponente der App ist, wird als `app`-DOM-Element für die `Add`-Methode angegeben.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="c5d3b-117">Dienste können mit der `ConfigureServices`-Methode auf dem Host-Generator konfiguriert werden (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="c5d3b-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="c5d3b-118">Die Konfiguration kann über den Host-Generator (`builder.Configuration`) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="c5d3b-119">*Startup.cs* (Blazor-Server): Diese Datei enthält die Startlogik der App.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-119">*Startup.cs* (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="c5d3b-120">Die `Startup`-Klasse definiert zwei Methoden:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="c5d3b-121">`ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="c5d3b-122">In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird zum Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="c5d3b-123">`Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="c5d3b-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="c5d3b-125">Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="c5d3b-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (*Pages/_Host.cshtml*) einzurichten und die Navigation zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="c5d3b-127">*wwwroot/index.html* (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-127">*wwwroot/index.html* (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="c5d3b-128">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="c5d3b-129">Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="c5d3b-130">Die `App`-Komponente (*App.razor*) wird in der `AddComponent`-Methode in `Startup.Configure` als `app`-DOM-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="c5d3b-131">Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="c5d3b-132">Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="c5d3b-133">Sie initialisiert die Runtime, damit die App ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="c5d3b-134">*App.razor*: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-134">*App.razor*: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="c5d3b-135">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="c5d3b-136">Ordner *Pages*: Dieser Ordner enthält die routingfähigen Komponenten und Seiten (*RAZOR-Dateien*), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor-Server-App.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-136">*Pages* folder: Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="c5d3b-137">Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="c5d3b-138">Die Vorlage besteht aus den folgenden Teilen:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-138">The template includes the following:</span></span>
  * <span data-ttu-id="c5d3b-139">*_Host.cshtml* (Blazor-Server): Die Stammseite der App, die als Razor-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-139">*_Host.cshtml* (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="c5d3b-140">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="c5d3b-141">Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="c5d3b-142">Auf der Hostseite wird angegeben, wo die `App`-Stammkomponente (*App.razor*) gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="c5d3b-143">`Counter` (*Counter.razor*): Implementiert die Zählerseite.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-143">`Counter` (*Counter.razor*): Implements the Counter page.</span></span>
  * <span data-ttu-id="c5d3b-144">`Error` (*Error.razor*, nur Blazor-Server-App): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-144">`Error` (*Error.razor*, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="c5d3b-145">`FetchData` (*FetchData.razor*): Implementiert die Seite zum Abrufen von Daten.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-145">`FetchData` (*FetchData.razor*): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="c5d3b-146">`Index` (*Index.razor*): Implementiert die Homepage.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-146">`Index` (*Index.razor*): Implements the Home page.</span></span>

* <span data-ttu-id="c5d3b-147">Ordner *Shared*: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (*RAZOR-Dateien*), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c5d3b-147">*Shared* folder: Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="c5d3b-148">`MainLayout` (*MainLayout.razor*): Die Layoutkomponente der App.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-148">`MainLayout` (*MainLayout.razor*): The app's layout component.</span></span>
  * <span data-ttu-id="c5d3b-149">`NavMenu` (*NavMenu.razor*): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-149">`NavMenu` (*NavMenu.razor*): Implements sidebar navigation.</span></span> <span data-ttu-id="c5d3b-150">Sie schließt die [NavLink-Komponente](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="c5d3b-151">Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="c5d3b-152">*_Imports.razor*: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (*RAZOR-Dateien*) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-152">*_Imports.razor*: Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="c5d3b-153">Ordner *Data* (Blazor-Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-153">*Data* folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="c5d3b-154">*wwwroot*: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-154">*wwwroot*: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="c5d3b-155">*appsettings.json* (Blazor-Server): Konfigurationseinstellungen für die App.</span><span class="sxs-lookup"><span data-stu-id="c5d3b-155">*appsettings.json* (Blazor Server): Configuration settings for the app.</span></span>
