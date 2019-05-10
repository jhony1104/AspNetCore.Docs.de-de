---
title: Verwenden von JavaScriptServices zum Erstellen von einseitigen Anwendungen in ASP.NET Core
author: scottaddie
description: Informationen Sie zu den Vorteilen der Verwendung von JavaScriptServices zum Erstellen einer Single-Page Anwendung (SPA) von ASP.NET Core unterstützt wird.
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 08/02/2017
uid: client-side/spa-services
ms.openlocfilehash: ee772e67ef14608bcc6e3498ade00424ff6090e5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893947"
---
# <a name="use-javascriptservices-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="66439-103">Verwenden von JavaScriptServices zum Erstellen von einseitigen Anwendungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66439-103">Use JavaScriptServices to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="66439-104">Durch [Scott Addie](https://github.com/scottaddie) und [Fiyaz Hasan](http://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="66439-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](http://fiyazhasan.me/)</span></span>

<span data-ttu-id="66439-105">Eine Single-Page-Anwendung (SPA) ist ein Webanwendungstyp, der aufgrund seiner hohen Servicequalität und Leistungsstärke sehr beliebt ist.</span><span class="sxs-lookup"><span data-stu-id="66439-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="66439-106">Die Integration clientseitiger SPA-Frameworks oder -Bibliotheken wie [Angular](https://angular.io/) oder [React](https://facebook.github.io/react/) mit serverseitigen Frameworks wie ASP.NET Core kann schwierig sein.</span><span class="sxs-lookup"><span data-stu-id="66439-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks like ASP.NET Core can be difficult.</span></span> <span data-ttu-id="66439-107">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) wurde entwickelt, um Inkonsistenzen im Integrationsprozess zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="66439-107">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="66439-108">Hierdurch wird ein reibungsloser Betrieb verschiedener Client- und Servertechnologiestapel möglich.</span><span class="sxs-lookup"><span data-stu-id="66439-108">It enables seamless operation between the different client and server technology stacks.</span></span>

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a><span data-ttu-id="66439-109">Was ist JavaScriptServices</span><span class="sxs-lookup"><span data-stu-id="66439-109">What is JavaScriptServices</span></span>

<span data-ttu-id="66439-110">JavaScriptServices ist eine Sammlung von clientseitigen Technologien für ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66439-110">JavaScriptServices is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="66439-111">Das Ziel ist, die ASP.NET Core als Entwickler bevorzugte serverseitige Plattform zum Erstellen von SPAs zu positionieren.</span><span class="sxs-lookup"><span data-stu-id="66439-111">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="66439-112">JavaScriptServices besteht aus drei unterschiedlichen NuGet-Pakete:</span><span class="sxs-lookup"><span data-stu-id="66439-112">JavaScriptServices consists of three distinct NuGet packages:</span></span>

* <span data-ttu-id="66439-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="66439-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="66439-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="66439-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>
* <span data-ttu-id="66439-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span><span class="sxs-lookup"><span data-stu-id="66439-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span></span>

<span data-ttu-id="66439-116">Diese Pakete sind nützlich, wenn Sie:</span><span class="sxs-lookup"><span data-stu-id="66439-116">These packages are useful if you:</span></span>

* <span data-ttu-id="66439-117">JavaScript auf dem Server ausführen.</span><span class="sxs-lookup"><span data-stu-id="66439-117">Run JavaScript on the server</span></span>
* <span data-ttu-id="66439-118">Ein SPA-Framework oder eine Bibliothek verwenden möchten</span><span class="sxs-lookup"><span data-stu-id="66439-118">Use a SPA framework or library</span></span>
* <span data-ttu-id="66439-119">Clientseitige Assets mit Webpack erstellen wollen</span><span class="sxs-lookup"><span data-stu-id="66439-119">Build client-side assets with Webpack</span></span>

<span data-ttu-id="66439-120">Der Schwerpunkt dieses Artikels liegt auf der Zuhilfenahme des SpaServices-Pakets.</span><span class="sxs-lookup"><span data-stu-id="66439-120">Much of the focus in this article is placed on using the SpaServices package.</span></span>

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a><span data-ttu-id="66439-121">Was ist SpaServices</span><span class="sxs-lookup"><span data-stu-id="66439-121">What is SpaServices</span></span>

<span data-ttu-id="66439-122">SpaServices wurde erstellt, damit Entwickler ASP.NET Core bevorzugt als serverseitige Plattform zum Erstellen von SPAs zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="66439-122">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="66439-123">SpaServices ist nicht erforderlich, um SPAs mit ASP.NET Core zu entwickeln, und ist nicht an ein bestimmtes Clientframework gebunden.</span><span class="sxs-lookup"><span data-stu-id="66439-123">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock you into a particular client framework.</span></span>

<span data-ttu-id="66439-124">SpaServices bietet nützliche Infrastruktur wie z.B.:</span><span class="sxs-lookup"><span data-stu-id="66439-124">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="66439-125">Serverseitige prerendering</span><span class="sxs-lookup"><span data-stu-id="66439-125">Server-side prerendering</span></span>](#server-prerendering)
* [<span data-ttu-id="66439-126">Webpack-Dev-Middleware</span><span class="sxs-lookup"><span data-stu-id="66439-126">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="66439-127">Der Austausch eines "Hot"</span><span class="sxs-lookup"><span data-stu-id="66439-127">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="66439-128">Routing-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="66439-128">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="66439-129">Diese Infrastrukturkomponenten beschleunigen sowohl den Entwicklungsworkflow als auch die Zusammenarbeit mit der Common Language Runtime-Umgebung.</span><span class="sxs-lookup"><span data-stu-id="66439-129">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="66439-130">Die Komponenten können einzeln verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66439-130">The components can be adopted individually.</span></span>

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="66439-131">Voraussetzungen für die Verwendung von SpaServices</span><span class="sxs-lookup"><span data-stu-id="66439-131">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="66439-132">Führen Sie die folgenden Schritte aus, um mit SpaServices zu arbeiten:</span><span class="sxs-lookup"><span data-stu-id="66439-132">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="66439-133">[Node.js](https://nodejs.org/) (Version 6 oder höher) mit Npm</span><span class="sxs-lookup"><span data-stu-id="66439-133">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>
  * <span data-ttu-id="66439-134">Führen Sie den folgenden Befehl über die Befehlszeile aus, um zu überprüfen, ob diese Komponenten installiert sind und gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="66439-134">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

<span data-ttu-id="66439-135">Hinweis: Wenn Sie in einer Azure-Website bereitstellen möchten, müssen Sie nicht hier nichts &mdash; Node.js installiert ist und in der Server-Umgebung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66439-135">Note: If you're deploying to an Azure web site, you don't need to do anything here &mdash; Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="66439-136">Wenn Sie Windows mit Visual Studio 2017 verwenden, wird das SDK durch Auswählen der Option **plattformübergreifende Entwicklung mit .NET Core** installiert.</span><span class="sxs-lookup"><span data-stu-id="66439-136">If you're on Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="66439-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="66439-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a><span data-ttu-id="66439-138">Serverseitige prerendering</span><span class="sxs-lookup"><span data-stu-id="66439-138">Server-side prerendering</span></span>

<span data-ttu-id="66439-139">Eine universelle Anwendung (auch bekannt als isomorphe Anwendung) ist eine JavaScript-Anwendung, die sowohl auf dem Server als auch auf dem Client ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="66439-139">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="66439-140">Angular, React und andere beliebte Frameworks bieten eine universelle Plattform für diesen Anwendungsentwicklungsstil.</span><span class="sxs-lookup"><span data-stu-id="66439-140">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="66439-141">Die Idee dahinter ist, zunächst die Framework-Komponenten über Node.js auf dem Server zu rendern und dann weitere Ausführungen an den Client zu delegieren.</span><span class="sxs-lookup"><span data-stu-id="66439-141">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="66439-142">ASP.NET Core-[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), bereitgestellt durch SpaServices, vereinfachen die Implementierung von serverseitigem Prerendering durch Bereitstellen und Aufrufen der JavaScript-Funktionen auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="66439-142">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="66439-143">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66439-143">Prerequisites</span></span>

<span data-ttu-id="66439-144">Installieren Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66439-144">Install the following:</span></span>

* <span data-ttu-id="66439-145">[ASPNET-Prerendering](https://www.npmjs.com/package/aspnet-prerendering) Npm-Paket:</span><span class="sxs-lookup"><span data-stu-id="66439-145">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a><span data-ttu-id="66439-146">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66439-146">Configuration</span></span>

<span data-ttu-id="66439-147">Die Taghilfsprogramme werden im Projekt über die Namespace-Registrierung in der *_ViewImports.cshtml*-Datei erkennbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="66439-147">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="66439-148">Diese Taghilfsprogramme abstrahieren die Feinheiten der direkten Kommunikation mit Low-Level-APIs durch die Nutzung einer HTML-ähnlichen Syntax in der Razor-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="66439-148">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a><span data-ttu-id="66439-149">Die `asp-prerender-module` Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="66439-149">The `asp-prerender-module` Tag Helper</span></span>

<span data-ttu-id="66439-150">Das`asp-prerender-module`-Taghilfsprogramm aus dem vorherigen Beispiel führt *ClientApp/dist/main-server.js* auf dem Server über Node.js aus.</span><span class="sxs-lookup"><span data-stu-id="66439-150">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="66439-151">Für die Verständlichkeit: Die *main-server.js*-Datei ist ein Artefakt der TypeScript-zu-JavaScript-Transpilation aus dem [Webpack](http://webpack.github.io/)-Buildprozess.</span><span class="sxs-lookup"><span data-stu-id="66439-151">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](http://webpack.github.io/) build process.</span></span> <span data-ttu-id="66439-152">Webpack definiert einen Einstiegspunktalias für `main-server`, dessen Abhängigkeitsdiagramm und Durchlauf bei der *ClientApp/boot-server.ts*-Datei beginnt:</span><span class="sxs-lookup"><span data-stu-id="66439-152">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="66439-153">Im folgenden Angular-Beispiel nutzt die *ClientApp/boot-server.ts*-Datei die `createServerRenderer`-Funktion und den `RenderResult`-Typ des `aspnet-prerendering`-Npm-Pakets, um das Serverrendering über Node.js zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="66439-153">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="66439-154">Das für das Server-Prerendering bestimmte HTML-Markup wird an eine Auflösungsfunktion übergeben. die von einem stark typisierten `Promise`-Objekt von JavaScript umgeben ist.</span><span class="sxs-lookup"><span data-stu-id="66439-154">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="66439-155">Die Wichtigkeit des `Promise`-Objekts liegt darin, dass es asynchron HTML-Markup zur Injektion auf der Seite in das DOM-Platzhalterelement bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="66439-155">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a><span data-ttu-id="66439-156">Die `asp-prerender-data` Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="66439-156">The `asp-prerender-data` Tag Helper</span></span>

<span data-ttu-id="66439-157">In Kombination mit dem `asp-prerender-module`-Taghilfsprogramm kann das `asp-prerender-data`-Taghilfsprogramm dazu verwendet werden, um die Kontextinformationen an das serverseitige JavaScript von der Razor-Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="66439-157">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="66439-158">Das folgende Markup übergibt beispielsweise Benutzerdaten an das `main-server`-Modul:</span><span class="sxs-lookup"><span data-stu-id="66439-158">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="66439-159">Das empfangene `UserName`-Argument wird mit dem integrierten JSON-Serialisierungsprogramm serialisiert und befindet sich im `params.data`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="66439-159">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="66439-160">Im folgenden Angular-Beispiel werden diese Daten verwendet, um eine persönliche Begrüßung mittels eines `h1`-Elementes zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="66439-160">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="66439-161">Hinweis: Taghilfsprogramme übergebene Eigenschaftennamen mit dargestellt **PascalCase** Notation.</span><span class="sxs-lookup"><span data-stu-id="66439-161">Note: Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="66439-162">Im Gegensatz dazu stehen die gleichen Eigenschaftennamen, die im Javascript mit **CamelCase** dargestellt sind.</span><span class="sxs-lookup"><span data-stu-id="66439-162">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="66439-163">Die Standardkonfiguration der JSON-Serialisierung ist für diesen Unterschied verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="66439-163">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="66439-164">Aufbauend auf das vorherige Codebeispiel, können Daten vom Server an die Ansicht per Hydration der `globals`-Eigenschaft bereitgestellt werden, um diese an die `resolve`-Funktion weiterzureichen:</span><span class="sxs-lookup"><span data-stu-id="66439-164">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="66439-165">Das im `globals`-Objekt definierte `postList`-Array wird an das globale `window`-Javascript-Objekt gebunden.</span><span class="sxs-lookup"><span data-stu-id="66439-165">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="66439-166">Das Hoisting dieser Variablen in den globalen Bereich führt zur Deduplizierung von Aufwand, vor allem mit Bezug des Ladevorgangs der Daten einmal auf dem Server und erneut auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="66439-166">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![Globale postList Variable Window-Objekt angefügt](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a><span data-ttu-id="66439-168">Webpack-Dev-Middleware</span><span class="sxs-lookup"><span data-stu-id="66439-168">Webpack Dev Middleware</span></span>

<span data-ttu-id="66439-169">[Webpack-Dev-Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) bietet einen optimierten Entwicklungsworkflow, bei dem Webpack die Ressourcen bei Bedarf erstellt.</span><span class="sxs-lookup"><span data-stu-id="66439-169">[Webpack Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="66439-170">Die Middleware kompiliert und stellt clientseitig benötigte Ressourcen automatisch bereit, wenn eine Seite im Browser geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66439-170">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="66439-171">Der alternative Ansatz ist Webpack über das Npm-Buildskript des Projektes manuell aufzurufen, wenn eine Drittanbieterabhängigkeit oder der benutzerdefinierte Code geändert wird.</span><span class="sxs-lookup"><span data-stu-id="66439-171">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="66439-172">Ein Npm-Buildskript aus der *"Package.JSON"*-Datei ist im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="66439-172">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="prerequisites"></a><span data-ttu-id="66439-173">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66439-173">Prerequisites</span></span>

<span data-ttu-id="66439-174">Installieren Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66439-174">Install the following:</span></span>

* <span data-ttu-id="66439-175">[ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) Npm-Paket:</span><span class="sxs-lookup"><span data-stu-id="66439-175">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a><span data-ttu-id="66439-176">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66439-176">Configuration</span></span>

<span data-ttu-id="66439-177">Die Webpack-Dev-Middleware wird registriert und in die HTTP-Aufrufpipeline über den folgenden Code in der `Configure`-Methode der *"Startup.cs"*-Datei implementiert:</span><span class="sxs-lookup"><span data-stu-id="66439-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="66439-178">Die `UseWebpackDevMiddleware`-Erweiterungsmethode muss über die `UseStaticFiles`-Erweiterungsmethode vor dem [Registrieren des Hostings statischer Dateien](xref:fundamentals/static-files) aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="66439-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="66439-179">Registrieren Sie die Middleware aus Sicherheitsgründen nur, wenn die app im Entwicklungsmodus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="66439-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="66439-180">Die `output.publicPath`-Eigenschaft der *webpack.config.js*-Datei schreibt der Middleware vor, das `dist`-Verzeichnis auf Änderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="66439-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a><span data-ttu-id="66439-181">Hot Module Replacement</span><span class="sxs-lookup"><span data-stu-id="66439-181">Hot Module Replacement</span></span>

<span data-ttu-id="66439-182">Stellen Sie sich das Feature [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) von Webpack als Weiterentwicklung von [Webpack Dev Middleware](#webpack-dev-middleware) vor.</span><span class="sxs-lookup"><span data-stu-id="66439-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="66439-183">HMR verfügt über die gleichen Vorteile, optimiert den Entwicklungsworkflow allerdings noch weiter, indem die automatische Aktualisierung der Seiteninhalte nach dem Kompilieren der Änderungen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="66439-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="66439-184">Verwechseln Sie dies nicht mit einer Aktualisierung des Browsers, die den aktuellen In-Memory-Status und die Debugsitzung der SPA beeinträchtigen würde.</span><span class="sxs-lookup"><span data-stu-id="66439-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="66439-185">Es gibt einen Live-Link zwischen dem Webpack Dev Middleware-Dienst und dem Browser, was bedeutet, dass Änderungen an den Browser mithilfe von Push übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="66439-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="66439-186">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66439-186">Prerequisites</span></span>

<span data-ttu-id="66439-187">Installieren Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66439-187">Install the following:</span></span>

* <span data-ttu-id="66439-188">[Webpack-hot-Middleware](https://www.npmjs.com/package/webpack-hot-middleware) Npm-Paket:</span><span class="sxs-lookup"><span data-stu-id="66439-188">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a><span data-ttu-id="66439-189">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66439-189">Configuration</span></span>

<span data-ttu-id="66439-190">Die Komponente HMR muss in der MVC-HTTP-Aufrufpipeline in der `Configure`-Methode registriert werden:</span><span class="sxs-lookup"><span data-stu-id="66439-190">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="66439-191">Genau wie bei [Webpack Dev Middleware](#webpack-dev-middleware) muss die `UseWebpackDevMiddleware`-Erweiterungsmethode vor der `UseStaticFiles`-Erweiterungsmethode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="66439-191">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="66439-192">Registrieren Sie die Middleware aus Sicherheitsgründen nur, wenn die app im Entwicklungsmodus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="66439-192">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="66439-193">Die *webpack.config.js*-Datei muss ein `plugins`-Array definieren, auch wenn es leer gelassen wird:</span><span class="sxs-lookup"><span data-stu-id="66439-193">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="66439-194">Nach dem Laden der App im Browser zeigt die Registerkarte für die Konsole der Entwicklertools im Browser die Bestätigung der HMR-Aktivierung an:</span><span class="sxs-lookup"><span data-stu-id="66439-194">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

!["Hot" verbundene Austausch eines Controllermoduls-Nachricht](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a><span data-ttu-id="66439-196">Routing-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="66439-196">Routing helpers</span></span>

<span data-ttu-id="66439-197">In den meisten ASP.NET Core-basierten SPAs möchten Sie clientseitiges Routing zusätzlich zum serverseitigen Routing verwenden.</span><span class="sxs-lookup"><span data-stu-id="66439-197">In most ASP.NET Core-based SPAs, you'll want client-side routing in addition to server-side routing.</span></span> <span data-ttu-id="66439-198">Die SPA- und MVC-Routingsysteme können ohne Störung unabhängig voneinander arbeiten.</span><span class="sxs-lookup"><span data-stu-id="66439-198">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="66439-199">Es gibt jedoch einen Grenzfall, bei dem die Groß-/Kleinschreibung zur Herausforderung wird, und zwar beim Identifizieren von 404-HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="66439-199">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="66439-200">Betrachten Sie das Szenario, in dem eine Route ohne Erweiterung, `/some/page`, verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66439-200">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="66439-201">Angenommen, die Anforderung stimmt nicht mit dem Muster eines serverseitigen Pfades überein, jedoch mit einer clientseitigen Route.</span><span class="sxs-lookup"><span data-stu-id="66439-201">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="66439-202">Betrachten Sie nun eine eingehende Anforderung für `/images/user-512.png`, die in der Regel davon ausgeht, eine Bilddatei auf dem Server zu finden.</span><span class="sxs-lookup"><span data-stu-id="66439-202">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="66439-203">Wenn dieser Pfad für die angeforderte Ressource keiner serverseitigen Route oder statischen Datei entspricht, ist es unwahrscheinlich, dass die Route durch die clientseitige Anwendung verarbeitet werden soll. In der Regel soll ein HTTP-Statuscode 404 zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66439-203">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it — you generally want to return a 404 HTTP status code.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="66439-204">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66439-204">Prerequisites</span></span>

<span data-ttu-id="66439-205">Installieren Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66439-205">Install the following:</span></span>

* <span data-ttu-id="66439-206">Das npm-Paket für das clientseitige Routing.</span><span class="sxs-lookup"><span data-stu-id="66439-206">The client-side routing npm package.</span></span> <span data-ttu-id="66439-207">Verwenden Sie Angular als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66439-207">Using Angular as an example:</span></span>

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a><span data-ttu-id="66439-208">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66439-208">Configuration</span></span>

<span data-ttu-id="66439-209">Eine Erweiterungsmethode namens `MapSpaFallbackRoute` wird in der `Configure`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="66439-209">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="66439-210">Tipp: Routen werden in der Reihenfolge ausgewertet, in denen sie konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="66439-210">Tip: Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="66439-211">Daher wird die `default`-Route im obigen Codebeispiel zuerst für den Musterabgleich verwendet.</span><span class="sxs-lookup"><span data-stu-id="66439-211">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a><span data-ttu-id="66439-212">Erstellen eines neuen Projekts</span><span class="sxs-lookup"><span data-stu-id="66439-212">Creating a new project</span></span>

<span data-ttu-id="66439-213">JavaScriptServices bietet vorkonfigurierte Anwendungsvorlagen an.</span><span class="sxs-lookup"><span data-stu-id="66439-213">JavaScriptServices provides pre-configured application templates.</span></span> <span data-ttu-id="66439-214">SpaServices wird in dieser Vorlagen in Verbindung mit verschiedenen Frameworks und Bibliotheken wie Angular und React mit Redux verwendet.</span><span class="sxs-lookup"><span data-stu-id="66439-214">SpaServices is used in these templates, in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="66439-215">Diese Vorlagen können über die .NET Core-CLI mithilfe des folgenden Befehls installiert werden:</span><span class="sxs-lookup"><span data-stu-id="66439-215">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="66439-216">Eine Liste der verfügbaren SPA-Vorlagen wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66439-216">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="66439-217">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="66439-217">Templates</span></span>                                 | <span data-ttu-id="66439-218">Kurzname</span><span class="sxs-lookup"><span data-stu-id="66439-218">Short Name</span></span> | <span data-ttu-id="66439-219">Sprache</span><span class="sxs-lookup"><span data-stu-id="66439-219">Language</span></span> | <span data-ttu-id="66439-220">Tags</span><span class="sxs-lookup"><span data-stu-id="66439-220">Tags</span></span>        |
|:------------------------------------------|:-----------|:---------|:------------|
| <span data-ttu-id="66439-221">MVC, ASP.NET Core mit Angular</span><span class="sxs-lookup"><span data-stu-id="66439-221">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="66439-222">angular</span><span class="sxs-lookup"><span data-stu-id="66439-222">angular</span></span>    | <span data-ttu-id="66439-223">[C#]</span><span class="sxs-lookup"><span data-stu-id="66439-223">[C#]</span></span>     | <span data-ttu-id="66439-224">MVC/Web/SPA</span><span class="sxs-lookup"><span data-stu-id="66439-224">Web/MVC/SPA</span></span> |
| <span data-ttu-id="66439-225">MVC, ASP.NET Core mit React.js</span><span class="sxs-lookup"><span data-stu-id="66439-225">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="66439-226">react</span><span class="sxs-lookup"><span data-stu-id="66439-226">react</span></span>      | <span data-ttu-id="66439-227">[C#]</span><span class="sxs-lookup"><span data-stu-id="66439-227">[C#]</span></span>     | <span data-ttu-id="66439-228">MVC/Web/SPA</span><span class="sxs-lookup"><span data-stu-id="66439-228">Web/MVC/SPA</span></span> |
| <span data-ttu-id="66439-229">MVC, ASP.NET Core mit React.js und Redux</span><span class="sxs-lookup"><span data-stu-id="66439-229">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="66439-230">reactredux</span><span class="sxs-lookup"><span data-stu-id="66439-230">reactredux</span></span> | <span data-ttu-id="66439-231">[C#]</span><span class="sxs-lookup"><span data-stu-id="66439-231">[C#]</span></span>     | <span data-ttu-id="66439-232">MVC/Web/SPA</span><span class="sxs-lookup"><span data-stu-id="66439-232">Web/MVC/SPA</span></span> |

<span data-ttu-id="66439-233">Schließen Sie zum Erstellen eines neuen Projekts mithilfe einer der SPA-Vorlagen den **Kurznamen** der Vorlage in den Befehl [dotnet new](/dotnet/core/tools/dotnet-new) ein.</span><span class="sxs-lookup"><span data-stu-id="66439-233">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="66439-234">Der folgende Befehl erstellt eine Angular-Anwendung mit ASP.NET Core MVC, die für die serverseitige Verwendung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="66439-234">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="66439-235">Festlegen des Laufzeitkonfigurationsmodus</span><span class="sxs-lookup"><span data-stu-id="66439-235">Set the runtime configuration mode</span></span>

<span data-ttu-id="66439-236">Es sind zwei Modi der primären Laufzeitkonfiguration vorhanden:</span><span class="sxs-lookup"><span data-stu-id="66439-236">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="66439-237">**Entwicklung**:</span><span class="sxs-lookup"><span data-stu-id="66439-237">**Development**:</span></span>
  * <span data-ttu-id="66439-238">Enthält Quellzuordnungen zur Erleichterung des Debuggings.</span><span class="sxs-lookup"><span data-stu-id="66439-238">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="66439-239">Der clientseitige Code wird nicht für Leistung optimiert.</span><span class="sxs-lookup"><span data-stu-id="66439-239">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="66439-240">**Produktion**:</span><span class="sxs-lookup"><span data-stu-id="66439-240">**Production**:</span></span>
  * <span data-ttu-id="66439-241">Schließt Quellzuordnungen aus.</span><span class="sxs-lookup"><span data-stu-id="66439-241">Excludes source maps.</span></span>
  * <span data-ttu-id="66439-242">Optimiert den clientseitigen Code über Bündelung und Minimierung.</span><span class="sxs-lookup"><span data-stu-id="66439-242">Optimizes the client-side code via bundling & minification.</span></span>

<span data-ttu-id="66439-243">ASP.NET Core verwendet eine Umgebungsvariable namens `ASPNETCORE_ENVIRONMENT` zum Speichern des Konfigurationsmodus.</span><span class="sxs-lookup"><span data-stu-id="66439-243">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="66439-244">Weitere Informationen finden Sie unter **[Festlegen der Umgebung](xref:fundamentals/environments#set-the-environment)**.</span><span class="sxs-lookup"><span data-stu-id="66439-244">See **[Set the environment](xref:fundamentals/environments#set-the-environment)** for more information.</span></span>

### <a name="running-with-net-core-cli"></a><span data-ttu-id="66439-245">Mit .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="66439-245">Running with .NET Core CLI</span></span>

<span data-ttu-id="66439-246">Stellen Sie die erforderlichen NuGet und Npm-Pakete mithilfe des folgenden Befehls auf das Stammverzeichnis des Projekts wieder her:</span><span class="sxs-lookup"><span data-stu-id="66439-246">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="66439-247">Erstellen und Ausführen der Anwendung:</span><span class="sxs-lookup"><span data-stu-id="66439-247">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="66439-248">Die Anwendung wird auf dem Localhost gemäß des [Laufzeitkonfigurationsmodus](#runtime-config-mode) gestartet.</span><span class="sxs-lookup"><span data-stu-id="66439-248">The application starts on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> <span data-ttu-id="66439-249">Navigieren Sie zu `http://localhost:5000` im Browser, wo die Landing Page angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="66439-249">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="running-with-visual-studio-2017"></a><span data-ttu-id="66439-250">Ausführen mit Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="66439-250">Running with Visual Studio 2017</span></span>

<span data-ttu-id="66439-251">Öffnen Sie die *csproj*-Datei, die mittels des [dotnet new](/dotnet/core/tools/dotnet-new)-Befehls erzeugt wurde.</span><span class="sxs-lookup"><span data-stu-id="66439-251">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="66439-252">Die erforderlichen NuGet- und npm-Pakete werden automatisch beim Projektstart wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="66439-252">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="66439-253">Dieser Wiederherstellungsprozess kann einige Minuten dauern, und die Anwendung kann ausgeführt werden, wenn der Vorgang abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="66439-253">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="66439-254">Klicken Sie auf die grüne Schaltfläche "Ausführen", oder drücken Sie `Ctrl + F5`, und der Browser zur Landing Page der Anwendung wird geöffnet .</span><span class="sxs-lookup"><span data-stu-id="66439-254">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="66439-255">Die Anwendung wird auf dem Localhost gemäß des [Laufzeitkonfigurationsmodus](#runtime-config-mode) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66439-255">The application runs on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span>

<a name="app-testing"></a>

## <a name="testing-the-app"></a><span data-ttu-id="66439-256">Testen der App</span><span class="sxs-lookup"><span data-stu-id="66439-256">Testing the app</span></span>

<span data-ttu-id="66439-257">SpaServices-Vorlagen sind so vorkonfiguriert, dass sie clientseitige Tests mithilfe von [Karma](https://karma-runner.github.io/1.0/index.html) und [Jasmine](https://jasmine.github.io/) entwickeln können.</span><span class="sxs-lookup"><span data-stu-id="66439-257">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="66439-258">Jasmine ist ein beliebtes Komponententest-Framework für JavaScript, während Karma ein Test Runner für diese Tests ist.</span><span class="sxs-lookup"><span data-stu-id="66439-258">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="66439-259">Karma ist so konfiguriert, dass mit der [Webpack Dev Middleware](#webpack-dev-middleware) so zusammen gearbeitet wird, dass der Entwickler Tests nicht jedes Mal anhalten und wieder ausführen muss, wenn Änderungen vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="66439-259">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="66439-260">Der Test wird automatisch ausgeführt, egal ob der Code für den Testfall ausgeführt wird oder der Testfall selbst.</span><span class="sxs-lookup"><span data-stu-id="66439-260">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="66439-261">Verwenden Sie als Beispiel die Angular-Anwendung, in der bereits zwei Jasmine-Testfälle für `CounterComponent` in der *counter.component.spec.ts*-Datei bereit stehen:</span><span class="sxs-lookup"><span data-stu-id="66439-261">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="66439-262">Öffnen Sie die Eingabeaufforderung im *ClientApp*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="66439-262">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="66439-263">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="66439-263">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="66439-264">Das Skript startet den Karma-Test Runner, der die in der *karma.conf.js*-Datei definierten Einstellungen liest.</span><span class="sxs-lookup"><span data-stu-id="66439-264">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="66439-265">Neben anderen Einstellungen identifiziert die *karma.conf.js*-Datei die Testdateien, die über das `files`-Array ausgeführt werden sollen:</span><span class="sxs-lookup"><span data-stu-id="66439-265">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a><span data-ttu-id="66439-266">Veröffentlichen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="66439-266">Publishing the application</span></span>

<span data-ttu-id="66439-267">Es kann aufwendig sein, die generierten clientseitigen Ressourcen und die veröffentlichten ASP.NET Core-Artefakte in einem Paket, das bereitgestellt werden kann, zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="66439-267">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="66439-268">Glücklicherweise orchestriert SpaServices diesen gesamten Veröffentlichungsprozess mit einem benutzerdefinierten MSBuild-Ziel mit dem Namen `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="66439-268">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="66439-269">Das MSBuild-Ziel hat die folgenden Verantwortungen:</span><span class="sxs-lookup"><span data-stu-id="66439-269">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="66439-270">Die Npm-Pakete wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="66439-270">Restore the npm packages</span></span>
1. <span data-ttu-id="66439-271">Erstellen eines Builds auf Produktionsniveau von clientseitigen Assets von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="66439-271">Create a production-grade build of the third-party, client-side assets</span></span>
1. <span data-ttu-id="66439-272">Erstellen eines Builds auf Produktionsniveau der benutzerdefinierten clientseitigen Assets</span><span class="sxs-lookup"><span data-stu-id="66439-272">Create a production-grade build of the custom client-side assets</span></span>
1. <span data-ttu-id="66439-273">Kopieren der generierten Webpack-Assets in den Ordner für die Veröffentlichung.</span><span class="sxs-lookup"><span data-stu-id="66439-273">Copy the Webpack-generated assets to the publish folder</span></span>

<span data-ttu-id="66439-274">Das MSBuild-Ziel wird aufgerufen, wenn Folgendes ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="66439-274">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="66439-275">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="66439-275">Additional resources</span></span>

* [<span data-ttu-id="66439-276">Angular-Docs</span><span class="sxs-lookup"><span data-stu-id="66439-276">Angular Docs</span></span>](https://angular.io/docs)
