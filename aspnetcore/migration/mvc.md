---
title: Migrieren von ASP.NET MVC zu ASP.NET Core MVC
author: ardalis
description: Informationen Sie zum Migrieren eines ASP.NET MVC-Projekts zu ASP.NET Core MVC beginnen.
ms.author: riande
ms.date: 02/13/2019
uid: migration/mvc
ms.openlocfilehash: 2ca51a145243444722ad8081fd8cdbb65d72b53a
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248042"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="752a3-103">Migrieren von ASP.NET MVC zu ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="752a3-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="752a3-104">Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), und [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="752a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="752a3-105">In diesem Artikel zeigt, wie für den Einstieg in das Migrieren eines ASP.NET MVC-Projekts zu [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="752a3-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="752a3-106">Im Prozess wird hervorgehoben viele Funktionen, die von ASP.NET MVC geändert haben.</span><span class="sxs-lookup"><span data-stu-id="752a3-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="752a3-107">Migrieren von ASP.NET MVC ist ein mehrstufiger Prozess, und dieser Artikel behandelt die ersteinrichtung, einfachen Controller und Ansichten, statischen Inhalt und clientseitige Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="752a3-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="752a3-108">Zusätzliche Artikel decken Migrieren von Konfiguration und Code für die Identität in ASP.NET MVC-Projekte, die viele gefunden.</span><span class="sxs-lookup"><span data-stu-id="752a3-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="752a3-109">Die Versionsnummern in den Beispielen möglicherweise keine aktuellen.</span><span class="sxs-lookup"><span data-stu-id="752a3-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="752a3-110">Sie müssen Ihre Projekte entsprechend aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="752a3-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="752a3-111">Beim Start von ASP.NET MVC-Projekt erstellen</span><span class="sxs-lookup"><span data-stu-id="752a3-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="752a3-112">Um das Upgrade zu demonstrieren, beginnen wir durch das Erstellen einer ASP.NET MVC-app.</span><span class="sxs-lookup"><span data-stu-id="752a3-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="752a3-113">Erstellen sie mit dem Namen *"WebApp1"* damit der Namespace des ASP.NET Core-Projekts entspricht, wir im nächsten Schritt erstellen.</span><span class="sxs-lookup"><span data-stu-id="752a3-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio-Projekt-Dialogfeld](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage, die im Bereich von ASP.NET Vorlagen ausgewählt](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="752a3-116">*Optional:* Ändern Sie den Namen der Lösung von *"WebApp1"* zu *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="752a3-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="752a3-117">Visual Studio zeigt den Namen der neuen Projektmappe (*Mvc5*), die erleichtert es, teilen Sie dieses Projekt aus das nächste Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="752a3-118">ASP.NET Core-Projekt erstellen</span><span class="sxs-lookup"><span data-stu-id="752a3-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="752a3-119">Erstellen Sie ein neues *leere* ASP.NET Core-Web-app mit den gleichen Namen wie das vorherige Projekt (*"WebApp1"*) damit die Namespaces in beiden Projekten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="752a3-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="752a3-120">Mit den gleichen Namespace erleichtert es, Code zwischen den beiden Projekten kopieren.</span><span class="sxs-lookup"><span data-stu-id="752a3-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="752a3-121">Sie müssen zum Erstellen des Projekts in einem anderen Verzeichnis als das vorherige Projekt, das den gleichen Namen verwenden.</span><span class="sxs-lookup"><span data-stu-id="752a3-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Web Application": Leere Projektvorlage, die im Bereich von ASP.NET Core-Vorlagen ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="752a3-124">*Optional:* Erstellen Sie eine neue ASP.NET Core-app, mit der *Webanwendung* Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="752a3-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="752a3-125">Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungsoption "des **einzelne Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="752a3-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="752a3-126">Benennen Sie diese app *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="752a3-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="752a3-127">Erstellen dieses Projekt sparen Sie Zeit bei der Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="752a3-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="752a3-128">Sie können der Vorlage generierten Codes das Endergebnis oder Code in das Konvertierungsprojekt zu kopieren, ansehen.</span><span class="sxs-lookup"><span data-stu-id="752a3-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="752a3-129">Es ist auch hilfreich, wenn Sie auf einen Konvertierungsschritt Vergleich mit der Vorlage generierte Projekt hängen bleiben.</span><span class="sxs-lookup"><span data-stu-id="752a3-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="752a3-130">Konfigurieren Sie den Standort für die Verwendung von MVC</span><span class="sxs-lookup"><span data-stu-id="752a3-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="752a3-131">Wenn .NET Core als Ziel der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) wird standardmäßig verwiesen.</span><span class="sxs-lookup"><span data-stu-id="752a3-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="752a3-132">Dieses Paket enthält Pakete, die häufig verwendete Pakete von MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="752a3-132">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="752a3-133">Wenn .NET Framework abzielt, müssen die Paketverweise einzeln in der Projektdatei aufgeführt sein.</span><span class="sxs-lookup"><span data-stu-id="752a3-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="752a3-134">Wenn .NET Core als Ziel der [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) wird standardmäßig verwiesen.</span><span class="sxs-lookup"><span data-stu-id="752a3-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="752a3-135">Dieses Paket enthält Pakete, die häufig verwendete Pakete von MVC-apps.</span><span class="sxs-lookup"><span data-stu-id="752a3-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="752a3-136">Wenn .NET Framework abzielt, müssen die Paketverweise einzeln in der Projektdatei aufgeführt sein.</span><span class="sxs-lookup"><span data-stu-id="752a3-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="752a3-137">Wenn .NET Core oder .NET Framework abgezielt wird, werden Pakete, die häufig verwendete Pakete von MVC-apps in der Projektdatei einzeln aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="752a3-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="752a3-138">`Microsoft.AspNetCore.Mvc` ist das ASP.NET Core MVC-Framework.</span><span class="sxs-lookup"><span data-stu-id="752a3-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="752a3-139">`Microsoft.AspNetCore.StaticFiles` ist der Handler statische Dateien.</span><span class="sxs-lookup"><span data-stu-id="752a3-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="752a3-140">Die ASP.NET Core-Runtime ist modular aufgebaut, und Sie müssen ausdrücklich zum Bereitstellen statischer Dateien (finden Sie unter [statische Dateien](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="752a3-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="752a3-141">Öffnen der *"Startup.cs"* Datei, und ändern Sie den Code entsprechend der folgenden:</span><span class="sxs-lookup"><span data-stu-id="752a3-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="752a3-142">Die `UseStaticFiles` -Erweiterungsmethode fügt den Handler statische Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="752a3-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="752a3-143">Wie bereits erwähnt, ist die ASP.NET-Laufzeit modular aufgebaut, und Sie müssen ausdrücklich zum Bereitstellen statischer Dateien.</span><span class="sxs-lookup"><span data-stu-id="752a3-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="752a3-144">Die `UseMvc` Erweiterungsmethode fügt routing.</span><span class="sxs-lookup"><span data-stu-id="752a3-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="752a3-145">Weitere Informationen finden Sie unter [Anwendungsstart](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="752a3-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="752a3-146">Fügen Sie einen Controller und Ansicht</span><span class="sxs-lookup"><span data-stu-id="752a3-146">Add a controller and view</span></span>

<span data-ttu-id="752a3-147">In diesem Abschnitt fügen Sie einen minimalen Controller und eine Ansicht als Platzhalter für den ASP.NET MVC-Controller und Ansichten, dass Sie im nächsten Abschnitt migrieren müssen.</span><span class="sxs-lookup"><span data-stu-id="752a3-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="752a3-148">Hinzufügen einer *Controller* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="752a3-149">Hinzufügen einer **"Controller"-Klasse** mit dem Namen *"HomeController.cs"* auf die *Controller* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="752a3-151">Hinzufügen einer *Ansichten* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="752a3-152">Hinzufügen einer *Views/Home* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="752a3-153">Hinzufügen einer **Razor-Ansicht** mit dem Namen *"Index.cshtml"* auf die *Views/Home* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

<span data-ttu-id="752a3-155">Die Projektstruktur ist unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="752a3-155">The project structure is shown below:</span></span>

![Projektmappen-Explorer mit Dateien und Ordner von "WebApp1"](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="752a3-157">Ersetzen Sie den Inhalt von der *Views/Home/Index.cshtml* Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="752a3-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="752a3-158">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="752a3-158">Run the app.</span></span>

![Web-app in Microsoft Edge öffnen](mvc/_static/hello-world.png)

<span data-ttu-id="752a3-160">Finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="752a3-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="752a3-161">Nun, da wir ein minimale funktionierende ASP.NET Core-Projekt haben, können wir beginnen, migrieren Funktionen von ASP.NET MVC-Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="752a3-162">Wir müssen die folgenden verschieben:</span><span class="sxs-lookup"><span data-stu-id="752a3-162">We need to move the following:</span></span>

* <span data-ttu-id="752a3-163">Client-Side-Inhalt (CSS, Schriftarten und Skripts)</span><span class="sxs-lookup"><span data-stu-id="752a3-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="752a3-164">Controller</span><span class="sxs-lookup"><span data-stu-id="752a3-164">controllers</span></span>

* <span data-ttu-id="752a3-165">Ansichten</span><span class="sxs-lookup"><span data-stu-id="752a3-165">views</span></span>

* <span data-ttu-id="752a3-166">Modelle</span><span class="sxs-lookup"><span data-stu-id="752a3-166">models</span></span>

* <span data-ttu-id="752a3-167">Bündeln</span><span class="sxs-lookup"><span data-stu-id="752a3-167">bundling</span></span>

* <span data-ttu-id="752a3-168">Filter</span><span class="sxs-lookup"><span data-stu-id="752a3-168">filters</span></span>

* <span data-ttu-id="752a3-169">Melden Sie sich in/out "," Identity (Dies erfolgt im nächsten Tutorial.)</span><span class="sxs-lookup"><span data-stu-id="752a3-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="752a3-170">Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="752a3-170">Controllers and views</span></span>

* <span data-ttu-id="752a3-171">Kopieren Sie jede der Methoden aus der ASP.NET MVC `HomeController` mit dem neuen `HomeController`.</span><span class="sxs-lookup"><span data-stu-id="752a3-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="752a3-172">Beachten Sie, dass in ASP.NET MVC, der integrierten Vorlage Controller Aktion Methodenrückgabetyp [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, die Aktionsmethoden return `IActionResult` stattdessen.</span><span class="sxs-lookup"><span data-stu-id="752a3-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="752a3-173">`ActionResult` implementiert `IActionResult`, daher keine Notwendigkeit besteht, den Rückgabetyp der Ihre Aktionsmethoden zu ändern.</span><span class="sxs-lookup"><span data-stu-id="752a3-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="752a3-174">Kopieren der *"About.cshtml"*, *"Contact.cshtml"*, und *"Index.cshtml"* Razor-Ansichtsdateien aus dem ASP.NET MVC-Projekt auf ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="752a3-175">Führen Sie die ASP.NET Core-app, und Testen Sie jede Methode.</span><span class="sxs-lookup"><span data-stu-id="752a3-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="752a3-176">Wir noch nicht die Layoutdatei oder Stile noch migriert. daher die gerenderten Ansichten nur den Inhalt in die Ansichtsdateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="752a3-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="752a3-177">Sie müssen also nicht die Layout-Datei generiert, Links, um die `About` und `Contact` anzeigt, daher Sie sie aus dem Browser aufzurufen müssen (ersetzen Sie **4492** mit der Portnummer, die in Ihrem Projekt verwendet).</span><span class="sxs-lookup"><span data-stu-id="752a3-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Seite "Kontakt"](mvc/_static/contact-page.png)

<span data-ttu-id="752a3-179">Beachten Sie, dass der Stil und Menüelementen.</span><span class="sxs-lookup"><span data-stu-id="752a3-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="752a3-180">Dies soll im nächsten Abschnitt behoben werden.</span><span class="sxs-lookup"><span data-stu-id="752a3-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="752a3-181">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="752a3-181">Static content</span></span>

<span data-ttu-id="752a3-182">In früheren Versionen von ASP.NET MVC statischer Inhalt wurde vom Stamm des Webprojekts gehostet und wurde mit einem serverseitigen Dateien vermischt.</span><span class="sxs-lookup"><span data-stu-id="752a3-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="752a3-183">In ASP.NET Core, statischer Inhalt gehostet wird, der *"Wwwroot"* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="752a3-184">Sie sollten den statischen Inhalt Ihre alten ASP.NET MVC-App zum Kopieren der *"Wwwroot"* Ordner in Ihrem ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="752a3-185">Bei dieser Konvertierung Beispiel:</span><span class="sxs-lookup"><span data-stu-id="752a3-185">In this sample conversion:</span></span>

* <span data-ttu-id="752a3-186">Kopieren der *favicon.ico* -Datei aus dem alten MVC-Projekt in der *"Wwwroot"* Ordner im ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="752a3-187">Die alte ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für die Formatierung und speichert, Dateien mit der Bootstrap-Stil, der *Content* und *Skripts* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="752a3-188">Die Vorlage, die mit das alte ASP.NET MVC-Projekt generiert wird, verweist auf Bootstrap in der Layoutdatei (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="752a3-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="752a3-189">Sie können Kopieren der *"Bootstrap.js"* und *Datei "Bootstrap.CSS"* Dateien von ASP.NET MVC-Projekt auf die *"Wwwroot"* Ordner in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="752a3-190">Stattdessen fügen wir Unterstützung für den Bootstrap-Stil (und andere clientseitige Bibliotheken) mithilfe des CDNs im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="752a3-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="752a3-191">Migrieren Sie die Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="752a3-191">Migrate the layout file</span></span>

* <span data-ttu-id="752a3-192">Kopieren der *_ViewStart.cshtml* Datei aus des alten ASP.NET MVC-Projekts *Ansichten* Ordner in des ASP.NET Core-Projekts *Ansichten* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="752a3-193">Die *_ViewStart.cshtml* Datei wurde in ASP.NET Core MVC nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="752a3-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="752a3-194">Erstellen Sie eine *Views/Shared* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="752a3-195">*Optional:* Kopie *_ViewImports.cshtml* aus der *FullAspNetCore* MVC-Projekt *Ansichten* Ordner in des ASP.NET Core-Projekts *Ansichten* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="752a3-196">Entfernen Sie alle Namespacedeklaration in der *_ViewImports.cshtml* Datei.</span><span class="sxs-lookup"><span data-stu-id="752a3-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="752a3-197">Die *_ViewImports.cshtml* Datei stellt die Namespaces für alle Ansichtsdateien bereit und verbindet [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="752a3-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="752a3-198">Taghilfsprogramme werden in die Neue Layoutdatei verwendet.</span><span class="sxs-lookup"><span data-stu-id="752a3-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="752a3-199">Die *_ViewImports.cshtml* Datei ist neu in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="752a3-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="752a3-200">Kopieren der *"_Layout.cshtml"* Datei aus des alten ASP.NET MVC-Projekts *Views/Shared* Ordner in des ASP.NET Core-Projekts *Views/Shared* Ordner.</span><span class="sxs-lookup"><span data-stu-id="752a3-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="752a3-201">Open *"_Layout.cshtml"* Datei, und stellen Sie die folgenden Änderungen vor (der vollständige Code wird unten dargestellt):</span><span class="sxs-lookup"><span data-stu-id="752a3-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="752a3-202">Ersetzen Sie dies `@Styles.Render("~/Content/css")` mit einem `<link>` Element geladen *Datei "Bootstrap.CSS"* (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="752a3-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="752a3-203">Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="752a3-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="752a3-204">Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile (setzen Sie die Zeile mit `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="752a3-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="752a3-205">Weitere Informationen finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="752a3-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="752a3-206">Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` mit einem `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="752a3-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="752a3-207">Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` mit einem `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="752a3-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="752a3-208">Das Ersatz-Markup für die Aufnahme der Bootstrap-CSS:</span><span class="sxs-lookup"><span data-stu-id="752a3-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="752a3-209">Das Ersatz-Markup für jQuery und Bootstrap-JavaScript-einschließen:</span><span class="sxs-lookup"><span data-stu-id="752a3-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="752a3-210">Die aktualisierte *"_Layout.cshtml"* Datei ist unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="752a3-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="752a3-211">Die Website im Browser anzeigen.</span><span class="sxs-lookup"><span data-stu-id="752a3-211">View the site in the browser.</span></span> <span data-ttu-id="752a3-212">Es sollte jetzt ordnungsgemäß mit den erwarteten Formatvorlagen direktes Laden.</span><span class="sxs-lookup"><span data-stu-id="752a3-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="752a3-213">*Optional:* Sie möchten möglicherweise versuchen Sie es mit der neuen Layoutdatei.</span><span class="sxs-lookup"><span data-stu-id="752a3-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="752a3-214">Für dieses Projekt kopieren Sie die Layoutdatei "aus der *FullAspNetCore* Projekt.</span><span class="sxs-lookup"><span data-stu-id="752a3-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="752a3-215">Die Neue Layoutdatei verwendet [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und weitere Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="752a3-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="752a3-216">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="752a3-216">Configure bundling and minification</span></span>

<span data-ttu-id="752a3-217">Informationen zum Konfigurieren der Bündelung und Minimierung, finden Sie unter [Bündelung und Minimierung](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="752a3-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="752a3-218">Beheben von HTTP 500-Fehler</span><span class="sxs-lookup"><span data-stu-id="752a3-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="752a3-219">Es gibt viele Probleme, die eine HTTP 500 Fehlermeldung führen können, die keine Informationen für die Quelle des Problems enthalten.</span><span class="sxs-lookup"><span data-stu-id="752a3-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="752a3-220">Z. B. wenn die *Views/_viewimports.cshtml* -Datei enthält einen Namespace, der in Ihrem Projekt nicht vorhanden ist, erhalten Sie einen HTTP 500-Fehler.</span><span class="sxs-lookup"><span data-stu-id="752a3-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="752a3-221">Standardmäßig in ASP.NET Core-apps die `UseDeveloperExceptionPage` Erweiterung hinzugefügt wird die `IApplicationBuilder` und ausgeführt, wenn die Konfiguration ist *Entwicklung*.</span><span class="sxs-lookup"><span data-stu-id="752a3-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="752a3-222">Der folgende Code veranschaulicht dies:</span><span class="sxs-lookup"><span data-stu-id="752a3-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="752a3-223">ASP.NET Core konvertiert nicht behandelte Ausnahmen in einer Web-app in HTTP 500-Fehlerantworten.</span><span class="sxs-lookup"><span data-stu-id="752a3-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="752a3-224">Fehlerdetails werden nicht in der Regel in diese Antworten Offenlegung potenziell vertraulicher Informationen über den Server enthalten.</span><span class="sxs-lookup"><span data-stu-id="752a3-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="752a3-225">Finden Sie unter **mithilfe der Ausnahmeseite** in [Fehlerbehandlung](../fundamentals/error-handling.md) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="752a3-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="752a3-226">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="752a3-226">Additional resources</span></span>

* <xref:razor-components/index>
* <xref:mvc/views/tag-helpers/intro>
