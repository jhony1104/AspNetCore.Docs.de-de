---
title: Migrieren von ASP.NET MVC zu ASP.net Core MVC
author: ardalis
description: Erfahren Sie, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu ASP.net Core MVC beginnen.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371877"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="b5c87-103">Migrieren von ASP.NET MVC zu ASP.net Core MVC</span><span class="sxs-lookup"><span data-stu-id="b5c87-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="b5c87-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)und [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="b5c87-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="b5c87-105">In diesem Artikel wird beschrieben, wie Sie die Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](../mvc/overview.md)starten.</span><span class="sxs-lookup"><span data-stu-id="b5c87-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="b5c87-106">In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben.</span><span class="sxs-lookup"><span data-stu-id="b5c87-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="b5c87-107">Die Migration von ASP.NET MVC ist ein Prozess mit mehreren Schritten. dieser Artikel behandelt die anfängliche Einrichtung, die grundlegenden Controller und Sichten, statische Inhalte und Client seitige Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="b5c87-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="b5c87-108">In weiteren Artikeln wird die Migration von Konfigurations-und Identitätscode in vielen ASP.NET MVC-Projekten behandelt.</span><span class="sxs-lookup"><span data-stu-id="b5c87-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="b5c87-109">Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell.</span><span class="sxs-lookup"><span data-stu-id="b5c87-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="b5c87-110">Möglicherweise müssen Sie Ihre Projekte entsprechend aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="b5c87-111">Erstellen des MVC-Projekts Starter ASP.net</span><span class="sxs-lookup"><span data-stu-id="b5c87-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="b5c87-112">Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app.</span><span class="sxs-lookup"><span data-stu-id="b5c87-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="b5c87-113">Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem ASP.net Core Projekt übereinstimmt, das im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b5c87-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio Dialogfeld "Neues Projekt"](mvc/_static/new-project.png)

![Dialogfeld für neue Webanwendung: Im Bereich "ASP.net Templates" ausgewählte MVC-Projektvorlage](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="b5c87-116">*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="b5c87-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="b5c87-117">Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.</span><span class="sxs-lookup"><span data-stu-id="b5c87-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="b5c87-118">Erstellen des ASP.net Core Projekts</span><span class="sxs-lookup"><span data-stu-id="b5c87-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="b5c87-119">Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt ( *"WebApp1"* ), damit die Namespaces in den beiden Projekten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="b5c87-120">Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="b5c87-121">Sie müssen dieses Projekt in einem anderen Verzeichnis als dem vorherigen Projekt erstellen, um denselben Namen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": In ASP.net Core Vorlagen Panel ist eine leere Projektvorlage ausgewählt.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="b5c87-124">*Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app.</span><span class="sxs-lookup"><span data-stu-id="b5c87-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="b5c87-125">Nennen Sie das Projekt *"WebApp1"* , und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus.</span><span class="sxs-lookup"><span data-stu-id="b5c87-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="b5c87-126">Benennen Sie diese APP in *fullaspnetcore*um.</span><span class="sxs-lookup"><span data-stu-id="b5c87-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="b5c87-127">Wenn Sie dieses Projekt erstellen, sparen Sie Zeit bei der Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="b5c87-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="b5c87-128">Sie können sich den von der Vorlage generierten Code ansehen, um das Endergebnis anzuzeigen oder Code in das Konvertierungs Projekt zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="b5c87-129">Es ist auch hilfreich, wenn Sie an einem Konvertierungs Schritt hängen bleiben, um mit dem Vorlagen generierten Projekt zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="b5c87-130">Konfigurieren des Standorts für die Verwendung von MVC</span><span class="sxs-lookup"><span data-stu-id="b5c87-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b5c87-131">Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="b5c87-132">Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="b5c87-133">Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="b5c87-134">Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket "Microsoft. aspnetcore. all](xref:fundamentals/metapackage) " verwiesen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="b5c87-135">Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="b5c87-136">Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="b5c87-137">Wenn .net Core oder .NET Framework als Ziel verwendet wird, werden Pakete, die häufig von MVC-Apps verwendet werden, einzeln in der Projektdatei aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="b5c87-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="b5c87-138">`Microsoft.AspNetCore.Mvc`ist das ASP.net Core MVC-Framework.</span><span class="sxs-lookup"><span data-stu-id="b5c87-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="b5c87-139">`Microsoft.AspNetCore.StaticFiles`ist der statische Datei Handler.</span><span class="sxs-lookup"><span data-stu-id="b5c87-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="b5c87-140">Die ASP.net Core-Laufzeit ist modular aufgebaut, und Sie müssen sich explizit für die Bereitstellung statischer Dateien entscheiden (siehe [statische Dateien](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="b5c87-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="b5c87-141">Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:</span><span class="sxs-lookup"><span data-stu-id="b5c87-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="b5c87-142">Die `UseStaticFiles` Erweiterungsmethode fügt den statischen Datei Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="b5c87-143">Wie bereits erwähnt, ist die ASP.NET-Laufzeit modular aufgebaut, und Sie müssen sich explizit für die Bereitstellung statischer Dateien entscheiden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="b5c87-144">Die `UseMvc` Erweiterungsmethode fügt Routing hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="b5c87-145">Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="b5c87-146">Controller und Ansicht hinzufügen</span><span class="sxs-lookup"><span data-stu-id="b5c87-146">Add a controller and view</span></span>

<span data-ttu-id="b5c87-147">In diesem Abschnitt fügen Sie einen minimalen Controller und eine Ansicht hinzu, die als Platzhalter für den ASP.NET MVC-Controller und die Ansichten fungieren, die Sie im nächsten Abschnitt migrieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="b5c87-148">Fügen Sie einen *Controller* Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="b5c87-149">Fügen Sie dem Ordner *Controllers* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="b5c87-151">Fügen Sie einen *views* -Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="b5c87-152">Fügen Sie einen *views/Home-* Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="b5c87-153">Fügen Sie dem Ordner *views/Home* eine **Razor-Ansicht** mit dem Namen *Index. cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

<span data-ttu-id="b5c87-155">Die Projektstruktur wird unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="b5c87-155">The project structure is shown below:</span></span>

![Projektmappen-Explorer, die Dateien und Ordner von "WebApp1" anzeigt](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="b5c87-157">Ersetzen Sie den Inhalt der Datei *views/Home/Index. cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b5c87-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="b5c87-158">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b5c87-158">Run the app.</span></span>

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

<span data-ttu-id="b5c87-160">Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="b5c87-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="b5c87-161">Nachdem wir nun über ein minimales Funktions ASP.net Core Projekt verfügen, können wir mit der Migration von Funktionen aus dem ASP.NET MVC-Projekt beginnen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="b5c87-162">Wir müssen Folgendes verschieben:</span><span class="sxs-lookup"><span data-stu-id="b5c87-162">We need to move the following:</span></span>

* <span data-ttu-id="b5c87-163">Client seitiger Inhalt (CSS, Schriftarten und Skripts)</span><span class="sxs-lookup"><span data-stu-id="b5c87-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="b5c87-164">Controller</span><span class="sxs-lookup"><span data-stu-id="b5c87-164">controllers</span></span>

* <span data-ttu-id="b5c87-165">Ansichten</span><span class="sxs-lookup"><span data-stu-id="b5c87-165">views</span></span>

* <span data-ttu-id="b5c87-166">Modelle</span><span class="sxs-lookup"><span data-stu-id="b5c87-166">models</span></span>

* <span data-ttu-id="b5c87-167">anbietet</span><span class="sxs-lookup"><span data-stu-id="b5c87-167">bundling</span></span>

* <span data-ttu-id="b5c87-168">Filter</span><span class="sxs-lookup"><span data-stu-id="b5c87-168">filters</span></span>

* <span data-ttu-id="b5c87-169">Anmelden/Abmelden, Identität (Dies erfolgt im nächsten Tutorial).</span><span class="sxs-lookup"><span data-stu-id="b5c87-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="b5c87-170">Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="b5c87-170">Controllers and views</span></span>

* <span data-ttu-id="b5c87-171">Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="b5c87-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="b5c87-172">Beachten Sie, dass in ASP.NET MVC der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage " [Action result](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);" lautet. in ASP.net Core MVC wird stattdessen die Aktionsmethode `IActionResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b5c87-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="b5c87-173">`ActionResult`implementiert `IActionResult`, sodass es nicht erforderlich ist, den Rückgabetyp ihrer Aktionsmethoden zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b5c87-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="b5c87-174">Kopieren Sie die Razor-Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* " vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="b5c87-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="b5c87-175">Führen Sie die ASP.net Core-App aus, und testen Sie jede Methode.</span><span class="sxs-lookup"><span data-stu-id="b5c87-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="b5c87-176">Wir haben die Layoutdatei oder Stile noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="b5c87-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="b5c87-177">Die Layoutdatei generiert keine Verknüpfungen für die `About` Ansichten `Contact` und. Daher müssen Sie Sie über den Browser aufrufen (ersetzen Sie **4492** durch die Portnummer, die in Ihrem Projekt verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="b5c87-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Kontaktseite](mvc/_static/contact-page.png)

<span data-ttu-id="b5c87-179">Beachten Sie den Mangel an Formatierungs-und Menü Elementen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="b5c87-180">Dies soll im nächsten Abschnitt behoben werden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="b5c87-181">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="b5c87-181">Static content</span></span>

<span data-ttu-id="b5c87-182">In früheren Versionen von ASP.NET MVC wurde statischer Inhalt vom Stamm des Webprojekts gehostet und mit serverseitigen Dateien gemischt.</span><span class="sxs-lookup"><span data-stu-id="b5c87-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="b5c87-183">In ASP.net Core wird statischer Inhalt im Ordner " *wwwroot* " gehostet.</span><span class="sxs-lookup"><span data-stu-id="b5c87-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="b5c87-184">Sie möchten den statischen Inhalt aus ihrer alten ASP.NET MVC-app in den Ordner " *wwwroot* " in Ihrem ASP.net Core Projekt kopieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="b5c87-185">In dieser Beispiel Konvertierung:</span><span class="sxs-lookup"><span data-stu-id="b5c87-185">In this sample conversion:</span></span>

* <span data-ttu-id="b5c87-186">Kopieren Sie die Datei *Favicon. ico* aus dem alten MVC-Projekt in den Ordner *wwwroot* im Projekt ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="b5c87-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="b5c87-187">Das alte ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Ordnern *Inhalt* und *Skripts* .</span><span class="sxs-lookup"><span data-stu-id="b5c87-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="b5c87-188">Die Vorlage, die das alte ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="b5c87-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="b5c87-189">Sie könnten die Dateien " *Bootstrap. js* " und " *Bootstrap. CSS* " aus dem ASP.NET-MVC-Projekt in den Ordner " *wwwroot* " im neuen Projekt kopieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="b5c87-190">Stattdessen wird die Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b5c87-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="b5c87-191">Migrieren der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="b5c87-191">Migrate the layout file</span></span>

* <span data-ttu-id="b5c87-192">Kopieren Sie die Datei *_ViewStart. cshtml* aus dem alten Ordner *views* des ASP.NET MVC-Projekts in den Ordner *views* des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="b5c87-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="b5c87-193">Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="b5c87-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="b5c87-194">Erstellen Sie einen *views/Shared-* Ordner.</span><span class="sxs-lookup"><span data-stu-id="b5c87-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="b5c87-195">*Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Ordner *views* -Ordner des *fullaspnetcore* -MVC-Projekts in den Ordner *views* des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="b5c87-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="b5c87-196">Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b5c87-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b5c87-197">Die Datei *_ViewImports. cshtml* stellt Namespaces für alle Ansichts Dateien bereit und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus.</span><span class="sxs-lookup"><span data-stu-id="b5c87-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="b5c87-198">Taghilfsprogramme werden in der neuen Layoutdatei verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5c87-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="b5c87-199">Die Datei *_ViewImports. cshtml* ist für ASP.net Core neu.</span><span class="sxs-lookup"><span data-stu-id="b5c87-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="b5c87-200">Kopieren Sie die Datei " *_Layout. cshtml* " aus dem alten " *views/Shared* "-Ordner des ASP.NET-MVC-Projekts in die *Ansichten/* den freigegebenen Ordner des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="b5c87-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="b5c87-201">Öffnen Sie die Datei *_Layout. cshtml* , und nehmen Sie die folgenden Änderungen vor (der vollständige Code wird unten gezeigt):</span><span class="sxs-lookup"><span data-stu-id="b5c87-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="b5c87-202">Ersetzen `@Styles.Render("~/Content/css")` Sie dies `<link>` durch ein-Element, das *Bootstrap. CSS* laden soll (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="b5c87-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="b5c87-203">Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="b5c87-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="b5c87-204">Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie `@*...*@`die Zeile mit).</span><span class="sxs-lookup"><span data-stu-id="b5c87-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="b5c87-205">Weitere Informationen finden [Sie unter Migrieren von Authentifizierung und Identität zu ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="b5c87-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="b5c87-206">Ersetzen `@Scripts.Render("~/bundles/jquery")` Sie dies `<script>` durch ein-Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="b5c87-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="b5c87-207">Ersetzen `@Scripts.Render("~/bundles/bootstrap")` Sie dies `<script>` durch ein-Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="b5c87-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="b5c87-208">Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="b5c87-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="b5c87-209">Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="b5c87-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="b5c87-210">Die aktualisierte Datei *_Layout. cshtml* wird unten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b5c87-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="b5c87-211">Zeigen Sie die Website im Browser an.</span><span class="sxs-lookup"><span data-stu-id="b5c87-211">View the site in the browser.</span></span> <span data-ttu-id="b5c87-212">Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b5c87-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="b5c87-213">*Optional:* Möglicherweise möchten Sie versuchen, die neue Layoutdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5c87-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="b5c87-214">Für dieses Projekt können Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt kopieren.</span><span class="sxs-lookup"><span data-stu-id="b5c87-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="b5c87-215">Die neue Layoutdatei [](xref:mvc/views/tag-helpers/intro) verwendet taghilfsprogramme und bietet weitere Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="b5c87-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="b5c87-216">Konfigurieren von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="b5c87-216">Configure bundling and minification</span></span>

<span data-ttu-id="b5c87-217">Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="b5c87-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="b5c87-218">Beheben von HTTP 500-Fehlern</span><span class="sxs-lookup"><span data-stu-id="b5c87-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="b5c87-219">Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält.</span><span class="sxs-lookup"><span data-stu-id="b5c87-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="b5c87-220">Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der nicht in Ihrem Projekt vorhanden ist, erhalten Sie einen HTTP 500-Fehler.</span><span class="sxs-lookup"><span data-stu-id="b5c87-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="b5c87-221">Standardmäßig wird die `UseDeveloperExceptionPage` Erweiterung in ASP.net Core-apps dem `IApplicationBuilder` hinzugefügt und ausgeführt, wenn die Konfiguration *entwickelt*wird.</span><span class="sxs-lookup"><span data-stu-id="b5c87-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="b5c87-222">Dies wird im folgenden Code ausführlich erläutert:</span><span class="sxs-lookup"><span data-stu-id="b5c87-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="b5c87-223">ASP.net Core konvertiert nicht behandelte Ausnahmen in einer Web-App in HTTP 500-Fehler Antworten.</span><span class="sxs-lookup"><span data-stu-id="b5c87-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="b5c87-224">Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b5c87-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="b5c87-225">Weitere Informationen finden **Sie unter Verwenden der Seite "Entwickler Ausnahme** " unter [Behandeln von Fehlern](../fundamentals/error-handling.md) .</span><span class="sxs-lookup"><span data-stu-id="b5c87-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5c87-226">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5c87-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
