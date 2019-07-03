---
title: Migrieren von ASP.NET Web-API in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von ASP.NET 4.x-Web-API ASP.NET Core MVC zu migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 74c7730a667ebc979241489733cdace149cacdf2
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555742"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="16e6b-103">Migrieren von ASP.NET Web-API in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16e6b-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="16e6b-104">Durch [Scott Addie](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="16e6b-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="16e6b-105">Eine ASP.NET 4.x-Web-API ist ein HTTP-Dienst, der eine Breite Palette von Clients, einschließlich Browsern und mobilen Geräten erreicht.</span><span class="sxs-lookup"><span data-stu-id="16e6b-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="16e6b-106">ASP.NET Core vereinheitlicht, ASP.NET 4.x MVC, und in ein einfacheres Programmiermodell, bekannt als ASP.NET Core MVC-Web-API-app-Modellen.</span><span class="sxs-lookup"><span data-stu-id="16e6b-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="16e6b-107">Dieser Artikel beschreibt die erforderlichen Schritte zum Migrieren von ASP.NET 4.x-Web-API zu ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="16e6b-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="16e6b-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16e6b-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="16e6b-109">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="16e6b-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="16e6b-110">Überprüfen Sie die ASP.NET 4.x-Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="16e6b-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="16e6b-111">Als Ausgangspunkt, in diesem Artikel wird die *ProductsApp* in erstelltes Projekt [erste Schritte mit ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="16e6b-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="16e6b-112">In diesem Projekt ist ein einfaches ASP.NET 4.x-Web-API-Projekt wie folgt konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="16e6b-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="16e6b-113">In *"Global.asax.cs"* , erfolgt ein Aufruf zum `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="16e6b-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="16e6b-114">Die `WebApiConfig` Klasse befindet sich der *App_Start* Ordner und weist eine statische `Register` Methode:</span><span class="sxs-lookup"><span data-stu-id="16e6b-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="16e6b-115">Diese Klasse konfiguriert [attributrouting](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl sie nicht tatsächlich im Projekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="16e6b-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="16e6b-116">Außerdem konfiguriert die Routingtabelle, die von ASP.NET Web-API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="16e6b-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="16e6b-117">In diesem Fall erwartet ASP.NET 4.x-Web-API-URLs, mit dem Format übereinstimmen `/api/{controller}/{id}`, mit `{id}` wird optional.</span><span class="sxs-lookup"><span data-stu-id="16e6b-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="16e6b-118">Die *ProductsApp* -Projekt enthält einen Controller.</span><span class="sxs-lookup"><span data-stu-id="16e6b-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="16e6b-119">Der Controller erbt `ApiController` und enthält zwei Aktionen:</span><span class="sxs-lookup"><span data-stu-id="16e6b-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="16e6b-120">Die `Product` verwendeten Modell `ProductsController` ist eine einfache Klasse:</span><span class="sxs-lookup"><span data-stu-id="16e6b-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="16e6b-121">Die folgenden Abschnitte zeigen bei der Migration der Web-API-Projekts zu ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="16e6b-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="16e6b-122">Zielprojekt erstellen</span><span class="sxs-lookup"><span data-stu-id="16e6b-122">Create destination project</span></span>

<span data-ttu-id="16e6b-123">Führen Sie die folgenden Schritte aus, in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="16e6b-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="16e6b-124">Wechseln Sie zu **Datei** > **neue** > **Projekt** > **anderen Projekttypen**  >  **Visual Studio-Projektmappen**.</span><span class="sxs-lookup"><span data-stu-id="16e6b-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="16e6b-125">Wählen Sie **leere Projektmappe**, und nennen Sie die Projektmappe *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="16e6b-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="16e6b-126">Klicken Sie auf die **OK** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="16e6b-126">Click the **OK** button.</span></span>
* <span data-ttu-id="16e6b-127">Fügen Sie der vorhandenen *ProductsApp* Projekt der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="16e6b-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="16e6b-128">Fügen Sie einen neuen **ASP.NET Core-Webanwendung** Projekt der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="16e6b-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="16e6b-129">Wählen Sie die **.NET Core** Zielframework aus der Dropdownliste aus, und wählen Sie die **API** Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="16e6b-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="16e6b-130">Nennen Sie das Projekt *ProductsCore*, und klicken Sie auf die **OK** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="16e6b-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="16e6b-131">Die Projektmappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="16e6b-131">The solution now contains two projects.</span></span> <span data-ttu-id="16e6b-132">In den folgenden Abschnitten wird erläutert, migrieren die *ProductsApp* Inhalt des Projekts, um die *ProductsCore* Projekt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="16e6b-133">Migrieren der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="16e6b-133">Migrate configuration</span></span>

<span data-ttu-id="16e6b-134">ASP.NET Core verwendet nicht die *App_Start* Ordner oder die *"Global.asax"* -Datei, und die *"Web.config"* am Datei hinzugefügt wird, Zeitpunkt der Veröffentlichung.</span><span class="sxs-lookup"><span data-stu-id="16e6b-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="16e6b-135">*"Startup.cs"* ist der Ersatz für *"Global.asax"* und befindet sich in das Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="16e6b-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="16e6b-136">Die `Startup` Klasse behandelt alle app-starttasks.</span><span class="sxs-lookup"><span data-stu-id="16e6b-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="16e6b-137">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="16e6b-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="16e6b-138">In ASP.NET Core MVC attributrouting ist standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> unter "lautet" `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="16e6b-139">Die folgenden `UseMvc` aufrufen ersetzt die *ProductsApp* des Projekts *app_start/webapiconfig.cs* Datei:</span><span class="sxs-lookup"><span data-stu-id="16e6b-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="16e6b-140">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="16e6b-140">Migrate models and controllers</span></span>

<span data-ttu-id="16e6b-141">Kopieren Sie die *ProductApp* des Projekts-Controller und das Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="16e6b-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="16e6b-142">Führen Sie folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="16e6b-142">Follow these steps:</span></span>

1. <span data-ttu-id="16e6b-143">Kopie *Controllers/ProductsController.cs* aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="16e6b-144">Kopieren Sie das gesamte *Modelle* Ordner aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="16e6b-145">Ändern Sie die kopierten Dateien Namespaces entsprechend den neuen Projektnamen (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="16e6b-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="16e6b-146">Anpassen der `using ProductsApp.Models;` -Anweisung in *ProductsController.cs* zu.</span><span class="sxs-lookup"><span data-stu-id="16e6b-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="16e6b-147">An diesem Punkt erstellen die app-Ergebnisse in eine Anzahl von Kompilierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="16e6b-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="16e6b-148">Dieser Fehler tritt auf, da die folgenden Komponenten in ASP.NET Core nicht vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="16e6b-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="16e6b-149">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="16e6b-149">`ApiController` class</span></span>
* <span data-ttu-id="16e6b-150">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="16e6b-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="16e6b-151">`IHttpActionResult` Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="16e6b-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="16e6b-152">Wie folgt vor, um die Fehler zu beheben:</span><span class="sxs-lookup"><span data-stu-id="16e6b-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="16e6b-153">Änderung `ApiController` zu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="16e6b-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="16e6b-154">Hinzufügen `using Microsoft.AspNetCore.Mvc;` zum Auflösen der `ControllerBase` Verweis.</span><span class="sxs-lookup"><span data-stu-id="16e6b-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="16e6b-155">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="16e6b-156">Ändern der `GetProduct` Rückgabetyp der Aktion von `IHttpActionResult` zu `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="16e6b-157">Vereinfachen der `GetProduct` Aktion `return` -Anweisung wie folgt:</span><span class="sxs-lookup"><span data-stu-id="16e6b-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="16e6b-158">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="16e6b-158">Configure routing</span></span>

<span data-ttu-id="16e6b-159">Konfigurieren des Routings wie folgt:</span><span class="sxs-lookup"><span data-stu-id="16e6b-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="16e6b-160">Ergänzen Sie die `ProductsController` Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="16e6b-160">Decorate the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="16e6b-161">Die obige [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfigurierte clientlaufzeitobjekt des Controllers Attribut-routing-Muster.</span><span class="sxs-lookup"><span data-stu-id="16e6b-161">The preceding [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="16e6b-162">Die [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut macht eine Voraussetzung für alle Aktionen in diesem Controller attributrouting.</span><span class="sxs-lookup"><span data-stu-id="16e6b-162">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="16e6b-163">Attributrouting unterstützt das Token, z. B. `[controller]` und `[action]`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="16e6b-164">Zur Laufzeit wird jedes Token, mit dem Namen des Controllers oder der Aktion ersetzt, der das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="16e6b-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="16e6b-165">Die Token reduzieren die Anzahl der Magic-Zeichenfolgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="16e6b-166">Die Token stellen Sie sicher auch Routen mit den entsprechenden Controller synchronisiert bleiben, und Aktionen, wenn automatische umbenennen Refactorings gelten.</span><span class="sxs-lookup"><span data-stu-id="16e6b-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="16e6b-167">Festlegen des Projekts-Kompatibilitätsmodus in ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="16e6b-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="16e6b-168">Die zuvor vorgenommene Änderung:</span><span class="sxs-lookup"><span data-stu-id="16e6b-168">The preceding change:</span></span>

    * <span data-ttu-id="16e6b-169">Ist erforderlich, um Sie verwenden die `[ApiController]` Attribut auf der Controllerebene.</span><span class="sxs-lookup"><span data-stu-id="16e6b-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="16e6b-170">Verwendet möglicherweise wichtige Verhaltensweisen, die in ASP.NET Core 2.2 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="16e6b-171">Aktivieren von HTTP-Get-Anforderungen an die `ProductController` Aktionen:</span><span class="sxs-lookup"><span data-stu-id="16e6b-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="16e6b-172">Anwenden der [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) -Attribut auf die `GetAllProducts` Aktion.</span><span class="sxs-lookup"><span data-stu-id="16e6b-172">Apply the [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="16e6b-173">Anwenden der `[HttpGet("{id}")]` -Attribut auf die `GetProduct` Aktion.</span><span class="sxs-lookup"><span data-stu-id="16e6b-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="16e6b-174">Nachdem Sie die vorangehenden Änderungen und das Entfernen nicht verwendeter `using` Anweisungen *ProductsController.cs* Datei sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="16e6b-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="16e6b-175">Führen Sie die migrierte Projekt, und navigieren Sie zu `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="16e6b-176">Eine vollständige Liste der drei Produkte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="16e6b-176">A full list of three products appears.</span></span> <span data-ttu-id="16e6b-177">Wechseln Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="16e6b-178">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="16e6b-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="16e6b-179">Kompatibilitäts-Shims</span><span class="sxs-lookup"><span data-stu-id="16e6b-179">Compatibility shim</span></span>

<span data-ttu-id="16e6b-180">Die [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek bietet eine kompatibilitätsshim zum Verschieben von ASP.NET 4.x-Web-API-Projekten in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="16e6b-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="16e6b-181">Der Kompatibilitäts-Shims erweitert ASP.NET Core, um eine Reihe von Konventionen von ASP.NET 4.x-Web-API 2 zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="16e6b-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="16e6b-182">Das Beispiel, das zuvor in diesem Dokument portiert ist einfach genug, dass der Kompatibilitäts-Shims nicht erforderlich war.</span><span class="sxs-lookup"><span data-stu-id="16e6b-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="16e6b-183">Für größere Projekte kann mithilfe der Kompatibilitäts-Shims für vorübergehend die API-Lücke, zwischen ASP.NET Core und ASP.NET 4.x-Web-API 2 nützlich.</span><span class="sxs-lookup"><span data-stu-id="16e6b-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="16e6b-184">Die Web-API-kompatibilitätsshim soll als vorübergehende Maßnahme zur Unterstützung der Migration großer ASP.NET 4.x-Web-API-Projekten in ASP.NET Core verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="16e6b-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="16e6b-185">Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.NET Core-Muster zu verwenden, statt die standardremotedatenbank von der Kompatibilitäts-Shims.</span><span class="sxs-lookup"><span data-stu-id="16e6b-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="16e6b-186">Kompatibilitätsfeatures in enthaltenen `Microsoft.AspNetCore.Mvc.WebApiCompatShim` enthalten:</span><span class="sxs-lookup"><span data-stu-id="16e6b-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="16e6b-187">Fügt eine `ApiController` geben, damit Controllern Basistypen nicht aktualisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="16e6b-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="16e6b-188">Ermöglicht die modellbindung für die Web-API-Format.</span><span class="sxs-lookup"><span data-stu-id="16e6b-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="16e6b-189">ASP.NET Core MVC-Modell Bindung funktioniert ähnlich, die von ASP.NET 4.x MVC 5, in der Standardeinstellung.</span><span class="sxs-lookup"><span data-stu-id="16e6b-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="16e6b-190">Der Shim-kompatibilitätsänderungen modellbindung ähnelt eher der ASP.NET 4.x-Web-API 2-Bindung modellkonventionen sein.</span><span class="sxs-lookup"><span data-stu-id="16e6b-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="16e6b-191">Komplexe Typen werden beispielsweise automatisch aus dem Anforderungstext gebunden.</span><span class="sxs-lookup"><span data-stu-id="16e6b-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="16e6b-192">Modellbindung erweitert, sodass Controlleraktionen Parameter des Typs nutzen können `HttpRequestMessage`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="16e6b-193">Fügt der Nachrichtenformatierungsprogramme zulassen von Aktionen zum Zurückgeben von Ergebnissen vom Typ `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="16e6b-194">Fügt zusätzliche Response-Methoden, die Web-API 2-Aktionen verwendet haben können, um Antworten zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="16e6b-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="16e6b-195">`HttpResponseMessage` Generatoren:</span><span class="sxs-lookup"><span data-stu-id="16e6b-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="16e6b-196">Ergebnis-Aktionsmethoden:</span><span class="sxs-lookup"><span data-stu-id="16e6b-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="16e6b-197">Fügt eine Instanz der `IContentNegotiator` an die app die Container für Abhängigkeitsinjektion (DI) und die Aushandlung bezogenen Inhaltstypen aus zur Verfügung gestellt [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="16e6b-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="16e6b-198">Beispiele für solche Typen `DefaultContentNegotiator` und `MediaTypeFormatter`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="16e6b-199">So verwenden Sie die Kompatibilitäts-Shims</span><span class="sxs-lookup"><span data-stu-id="16e6b-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="16e6b-200">Installieren Sie die [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="16e6b-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="16e6b-201">Registrieren der Kompatibilitäts-Shims für die Dienste mit der app-DI-Container durch Aufrufen von `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="16e6b-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="16e6b-202">Definieren Sie die Web-API-spezifische Routen mit `MapWebApiRoute` auf die `IRouteBuilder` der App `IApplicationBuilder.UseMvc` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="16e6b-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16e6b-203">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="16e6b-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
