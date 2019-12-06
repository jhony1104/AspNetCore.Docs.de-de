---
title: Migrieren von ASP.net-Web-API zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von der ASP.NET 4. x-Web-API zu ASP.net Core MVC migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: c68cf83f427f53b110075168c6d5e4d021808782
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881149"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="c85cc-103">Migrieren von ASP.net-Web-API zu ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c85cc-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="c85cc-104">Von [Scott Adder](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c85cc-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c85cc-105">Eine ASP.NET 4. x-Web-API ist ein HTTP-Dienst, der eine breite Palette von Clients, einschließlich Browser und mobiler Geräte, erreicht.</span><span class="sxs-lookup"><span data-stu-id="c85cc-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="c85cc-106">ASP.net Core vereinheitlicht die MVC-und Web-API-APP-Modelle von ASP.NET 4. x in ein einfacheres Programmiermodell, das als ASP.net Core MVC bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="c85cc-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="c85cc-107">In diesem Artikel werden die erforderlichen Schritte zum Migrieren von der ASP.NET 4. x-Web-API zu ASP.net Core MVC erläutert.</span><span class="sxs-lookup"><span data-stu-id="c85cc-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="c85cc-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c85cc-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c85cc-109">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c85cc-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="c85cc-110">ASP.NET 4. x-Web-API-Projekt überprüfen</span><span class="sxs-lookup"><span data-stu-id="c85cc-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="c85cc-111">Als Ausgangspunkt wird in diesem Artikel das *productapp* -Projekt verwendet, das in erste Schritte [mit ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="c85cc-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="c85cc-112">In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c85cc-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="c85cc-113">In *Global.asax.cs*wird ein-`WebApiConfig.Register`aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="c85cc-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="c85cc-114">Die `WebApiConfig`-Klasse befindet sich im Ordner *App_Start* und verfügt über eine statische `Register`-Methode:</span><span class="sxs-lookup"><span data-stu-id="c85cc-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="c85cc-115">Diese Klasse konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl Sie nicht tatsächlich im Projekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c85cc-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="c85cc-116">Außerdem wird die Routing Tabelle konfiguriert, die von ASP.net-Web-API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c85cc-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="c85cc-117">In diesem Fall erwartet die ASP.NET 4. x-Web-API, dass URLs dem Format `/api/{controller}/{id}`entsprechen, wobei `{id}` optional ist.</span><span class="sxs-lookup"><span data-stu-id="c85cc-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="c85cc-118">Das Projekt *producungapp* enthält einen Controller.</span><span class="sxs-lookup"><span data-stu-id="c85cc-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="c85cc-119">Der Controller erbt von `ApiController` und enthält zwei Aktionen:</span><span class="sxs-lookup"><span data-stu-id="c85cc-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="c85cc-120">Das `Product` Modell, das von `ProductsController` verwendet wird, ist eine einfache Klasse:</span><span class="sxs-lookup"><span data-stu-id="c85cc-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="c85cc-121">In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="c85cc-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="c85cc-122">Ziel Projekt erstellen</span><span class="sxs-lookup"><span data-stu-id="c85cc-122">Create destination project</span></span>

<span data-ttu-id="c85cc-123">Führen Sie in Visual Studio die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="c85cc-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="c85cc-124">Wechseln Sie zu **Datei** > **Neues** > **Projekt** > **andere Projekttypen** > **Visual Studio**-Projektmappen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="c85cc-125">Wählen Sie **leere**Projekt Mappe aus, und nennen Sie die Projekt Mappe *webapimigration*.</span><span class="sxs-lookup"><span data-stu-id="c85cc-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="c85cc-126">Klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="c85cc-126">Click the **OK** button.</span></span>
* <span data-ttu-id="c85cc-127">Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="c85cc-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="c85cc-128">Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="c85cc-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="c85cc-129">Wählen Sie in der Dropdown Liste das Ziel Framework **.net Core** aus, und wählen Sie die Vorlage **API** -Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="c85cc-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="c85cc-130">Nennen Sie das Projekt *productscore*, und klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="c85cc-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="c85cc-131">Die Projekt Mappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="c85cc-131">The solution now contains two projects.</span></span> <span data-ttu-id="c85cc-132">In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.</span><span class="sxs-lookup"><span data-stu-id="c85cc-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="c85cc-133">Konfiguration migrieren</span><span class="sxs-lookup"><span data-stu-id="c85cc-133">Migrate configuration</span></span>

<span data-ttu-id="c85cc-134">ASP.net Core verwendet nicht den Ordner *App_Start* oder die Datei *Global. asax* , und die Datei *Web. config* wird zum Zeitpunkt der Veröffentlichung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="c85cc-135">*Startup.cs* ist der Ersatz für *Global. asax* und befindet sich im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c85cc-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="c85cc-136">Die `Startup`-Klasse behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="c85cc-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="c85cc-137">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c85cc-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="c85cc-138">In ASP.net Core MVC ist das Attribut Routing standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> in `Startup.Configure`aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c85cc-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="c85cc-139">Der folgende `UseMvc`-Befehl ersetzt die *App_Start/webapiconfig.cs* -Datei des Projekts " *producungapp* ":</span><span class="sxs-lookup"><span data-stu-id="c85cc-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="c85cc-140">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="c85cc-140">Migrate models and controllers</span></span>

<span data-ttu-id="c85cc-141">Kopieren Sie den Controller des *productapp* -Projekts und das verwendete Modell.</span><span class="sxs-lookup"><span data-stu-id="c85cc-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="c85cc-142">Führen Sie folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="c85cc-142">Follow these steps:</span></span>

1. <span data-ttu-id="c85cc-143">Kopieren Sie " *Controllers/productioncontroller. cs* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="c85cc-144">Kopieren Sie den gesamten Ordner " *Models* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="c85cc-145">Ändern Sie die Namespaces der kopierten Dateien so, dass Sie mit dem neuen Projektnamen (*productscore*) identisch sind.</span><span class="sxs-lookup"><span data-stu-id="c85cc-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="c85cc-146">Passen Sie auch die `using ProductsApp.Models;` Anweisung in *ProductsController.cs* an.</span><span class="sxs-lookup"><span data-stu-id="c85cc-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="c85cc-147">Zu diesem Zeitpunkt führt das Entwickeln der APP zu einer Reihe von Kompilierungs Fehlern.</span><span class="sxs-lookup"><span data-stu-id="c85cc-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="c85cc-148">Die Fehler treten auf, weil die folgenden Komponenten nicht in ASP.net Core vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="c85cc-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="c85cc-149">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="c85cc-149">`ApiController` class</span></span>
* <span data-ttu-id="c85cc-150">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="c85cc-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="c85cc-151">`IHttpActionResult`-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="c85cc-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="c85cc-152">Beheben Sie die Fehler wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c85cc-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="c85cc-153">Änderung `ApiController` zu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="c85cc-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="c85cc-154">Fügen Sie `using Microsoft.AspNetCore.Mvc;` hinzu, um den `ControllerBase` Verweis aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="c85cc-155">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="c85cc-156">Ändern Sie den Rückgabetyp der `GetProduct` Aktion von `IHttpActionResult` in `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="c85cc-157">Vereinfachen Sie die `return`-Anweisung der `GetProduct` Aktion wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c85cc-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="c85cc-158">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="c85cc-158">Configure routing</span></span>

<span data-ttu-id="c85cc-159">Konfigurieren Sie das Routing wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c85cc-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="c85cc-160">Markieren Sie die `ProductsController`-Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="c85cc-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="c85cc-161">Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers.</span><span class="sxs-lookup"><span data-stu-id="c85cc-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="c85cc-162">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) -Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c85cc-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="c85cc-163">Das Attribut Routing unterstützt Token, z. b. `[controller]` und `[action]`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="c85cc-164">Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="c85cc-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="c85cc-165">Die Token reduzieren die Anzahl der magischen Zeichen folgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="c85cc-166">Die Token stellen außerdem sicher, dass Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn die refactoren für automatisches umbenennen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c85cc-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="c85cc-167">Legen Sie den Kompatibilitätsmodus des Projekts auf ASP.net Core 2,2 fest:</span><span class="sxs-lookup"><span data-stu-id="c85cc-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="c85cc-168">Die vorherige Änderung:</span><span class="sxs-lookup"><span data-stu-id="c85cc-168">The preceding change:</span></span>

    * <span data-ttu-id="c85cc-169">Ist erforderlich, um das `[ApiController]`-Attribut auf Controller Ebene zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c85cc-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="c85cc-170">Gibt an, dass potenziell wichtige Verhaltensweisen in ASP.net Core 2,2 eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c85cc-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="c85cc-171">HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:</span><span class="sxs-lookup"><span data-stu-id="c85cc-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="c85cc-172">Wenden Sie das [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) -Attribut auf die `GetAllProducts` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="c85cc-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="c85cc-173">Wenden Sie das `[HttpGet("{id}")]`-Attribut auf die `GetProduct` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="c85cc-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="c85cc-174">Nach den vorangehenden Änderungen und dem Entfernen nicht verwendeter `using`-Anweisungen sieht die *ProductsController.cs* -Datei wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="c85cc-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="c85cc-175">Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="c85cc-176">Es wird eine vollständige Liste der drei Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-176">A full list of three products appears.</span></span> <span data-ttu-id="c85cc-177">Wechseln Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="c85cc-178">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="c85cc-179">Kompatibilitäts-Shim</span><span class="sxs-lookup"><span data-stu-id="c85cc-179">Compatibility shim</span></span>

<span data-ttu-id="c85cc-180">Die [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek stellt einen Kompatibilitäts-Shim bereit, um ASP.NET 4. x-Web-API-Projekte in ASP.net Core zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="c85cc-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="c85cc-181">Der Kompatibilitäts-Shim erweitert ASP.net Core, um eine Reihe von Konventionen von ASP.NET 4. x Web-API 2 zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="c85cc-182">Das zuvor in diesem Dokument portierte Beispiel ist so einfach, dass das Kompatibilitäts-Shim unnötig ist.</span><span class="sxs-lookup"><span data-stu-id="c85cc-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="c85cc-183">Bei größeren Projekten kann die Verwendung des Kompatibilitäts-Shims nützlich sein, um die API-Lücke zwischen ASP.net Core und ASP.NET 4. x-Web-API 2 vorübergehend zu überbrücken.</span><span class="sxs-lookup"><span data-stu-id="c85cc-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="c85cc-184">Das Web-API-Kompatibilitäts-Shim ist als temporäre Maßnahme zur Unterstützung der Migration von großen ASP.NET 4. x-Web-API-Projekten zu ASP.net Core gedacht.</span><span class="sxs-lookup"><span data-stu-id="c85cc-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="c85cc-185">Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.net Core Muster zu verwenden, anstatt sich auf den Kompatibilitäts-Shim zu verlassen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="c85cc-186">Zu den in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` enthaltenen Kompatibilitäts Features gehören:</span><span class="sxs-lookup"><span data-stu-id="c85cc-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="c85cc-187">Fügt einen `ApiController` Typ hinzu, sodass die Basis Typen der Controller nicht aktualisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="c85cc-188">Ermöglicht die Modell Bindung im Web-API-Stil.</span><span class="sxs-lookup"><span data-stu-id="c85cc-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="c85cc-189">ASP.net Core die MVC-Modell Bindung ähnlich der von ASP.NET 4. x MVC 5 standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="c85cc-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="c85cc-190">Mit dem Kompatibilitäts-Shim wird die Modell Bindung so geändert, dass Sie den Modell Bindungs Konventionen von ASP.NET 4. x Web API 2 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="c85cc-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="c85cc-191">Komplexe Typen werden z. b. automatisch vom Anforderungs Text gebunden.</span><span class="sxs-lookup"><span data-stu-id="c85cc-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="c85cc-192">Erweitert die Modell Bindung, sodass Controller Aktionen Parameter vom Typ `HttpRequestMessage`annehmen können.</span><span class="sxs-lookup"><span data-stu-id="c85cc-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="c85cc-193">Fügt nachrichtenformatierer hinzu, sodass Aktionen Ergebnisse vom Typ `HttpResponseMessage`zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="c85cc-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="c85cc-194">Fügt zusätzliche Antwort Methoden hinzu, die von Web-API 2-Aktionen verwendet werden können, um Antworten zu erfüllen:</span><span class="sxs-lookup"><span data-stu-id="c85cc-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="c85cc-195">`HttpResponseMessage` Generatoren:</span><span class="sxs-lookup"><span data-stu-id="c85cc-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="c85cc-196">Aktions Ergebnis Methoden:</span><span class="sxs-lookup"><span data-stu-id="c85cc-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="c85cc-197">Fügt dem Container für die Abhängigkeitsinjektion der App eine Instanz von `IContentNegotiator` hinzu und stellt die inhaltsaushandlungs bezogenen Typen von [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="c85cc-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="c85cc-198">Beispiele für solche Typen sind `DefaultContentNegotiator` und `MediaTypeFormatter`.</span><span class="sxs-lookup"><span data-stu-id="c85cc-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="c85cc-199">So verwenden Sie den Kompatibilitäts-Shim:</span><span class="sxs-lookup"><span data-stu-id="c85cc-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="c85cc-200">Installieren Sie das nuget-Paket [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="c85cc-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="c85cc-201">Registrieren Sie die Dienste des Kompatibilitäts-Shims mit dem di-Container der APP, indem Sie `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c85cc-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="c85cc-202">Definieren Sie Web-API-spezifische Routen mithilfe von `MapWebApiRoute` auf der `IRouteBuilder` im `IApplicationBuilder.UseMvc`-Befehl der app.</span><span class="sxs-lookup"><span data-stu-id="c85cc-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c85cc-203">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c85cc-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
